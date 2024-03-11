# Examen SXE Marcos Fernández Avendaño ODOO
###### 11-Mar-2024
Lo primero será levantar un servicio de Docker:


```YML
version: '3.1'
services:
  # Aquí indicamos la imagen web que va a tener odoo
  web:
    image: odoo:16.0
    depends_on:
      - mydb
    volumes:
      - ./addons:/mnt/extra-addons
    ports:
      - "8069:8069" # indicamos que lo vamos a levantar en el puerto 8069
    environment:
      - HOST=mydb # indicamos el nombre del host al que nos vamos a conectar, es decir la base de datos es el database Name que debemos indicar al iniciar odoo
      - USER=odoo # creamos el usuario de esta base d edatos
      - PASSWORD=myodoo # indicamos la contraseña de este usuario
  mydb: # a continuación indicamos que cliente de base de datos vamos a usar
    image: postgres:15 # en este caso usaremos postgres
    environment:
      - POSTGRES_DB=postgres # indicamos que a la base de datos que nos vamos a conectar dentro del cliente postgres será postgres
      - POSTGRES_PASSWORD=myodoo # la contraseña de postgres será myodoo
      - POSTGRES_USER=odoo # el usuario que emplearemos será odoo
    ports:
      - "5433:5432" # aquí indicamos que docker va a usar el puerto por defecto 5432, peor nosotros accederemos a la base d edatos a través del puerto 5433
```

levantamos el servicio con ```docker compose up -d```

Bien una vez iniciado **ODOO** para activar los ajustes de administrador debemos primero instalar una app cualquiera
En mi caso he elegido **CRM**  

### CRM
Este es un chat tipo **TEAMS** o **MEET** que permite a los usuarios creados en odoo chatear entre ellos.

Tras instalar este modulo de **CRM** como caualquier otro, ya nos aparecen otro tipo de ajustes en odoo mucho más avanzados.

Aquñi debemos activar : ```developer mode (with assets)```

esto nos permitirá instalar comodamente OPENACADEMY

# OPEN ACADEMY

para instalar openacademy creamos una carpeta llamada extra-addons, bueno si la indicamos en volumenes ya al crea sola
En mi caso mis volum,es en docker compose son los siguientes:

            volumes:
      - ./addons:/mnt/extra-addons
por lo que es evidente que mi carpeta se llame solo addons, pero es algo de lo que no debemos preocuparnos.

#### Instalación:

Para isntalar open academuy debemos ejecutar el siguiente comando

        docker exec -it nombre-contenedor-web-1 odoo scaffold openacademy /mnt/extra-addons

El comando como tal es ```odoo scaffold openacademy /mnt/extra-addons``` Pero recordemso que odoo está instalado en 
docker, por lo que no contamso el comando odoo para ejecutar ya que no esta en local, para ello, podemos acceder
dentro de nuestro contenedor como terminal con la primera parte del comando:

    docker exec -it examenodoo-web-1

indico evidentemente el contenedor web, ya que el otro es la parte d ela base de datos.

TRAS HACER ESTE COMANDO PODEMSO COMPROBAR COMO YA NOS APARECE DENTRO DE ADDONNS LA CARPETA OPEN ACADEMY,

**carpeta Resources** esta carpeta será donde irán todas las capturas de pantalla

![Screenshot 2024-03-11 at 12.37.18 PM.png](resources%2FScreenshot%202024-03-11%20at%2012.37.18%E2%80%AFPM.png)

Tras instalar el OpenAcademy, reiniciamos el servicio web para que nos aparezca dentro de nuestras APPs

Bien, activamos el open academy y nos ponemso de lleno con el:

## CONFIGURACION OPEN ACADEMY

Para crear la tabla lo primero que debemos hacer es crear una clase con los datos de la tabla:

```Python
from odoo import models, fields #odoo falla porque no esta instalado en local

class productos(models.Model):
    _name = "productos"
    _description = "Tabla de diferentes productos"

    id = fields.Integer(string="ID")
    producto = fields.Char(string="Producto")
    viabilidad = fields.Float(string="Viabilidad", digits=(10, 2))
```

Tras hacer esto, vamos a crear una nueva carpeta que sea datos, dodne definiremso los 
diferentes campso de la tabla

```xml
<odoo>
    <data>
         <record model="productos" id="openacademy.productos">
            <!-->Aquñi indicaremso que campos tiene la talba, o haremso referencia a ellso mejro dicho<-->
            <field name="id">1</field>
            <field name="producto">Patatas</field>
            <field name="viabilidad">7.8</field>

        </record>
    </data>
</odoo>
```

También para crear esta tabla en postgres debemos indicar en el security tanto un id para esta tabla como el nombe de la misma

    id: access_openacademy_productos
    
    nombre tabla: model_productos

la linea compelta sería tal que así: `access_openacademy_productos,openacademy.openacademy,model_productos,base.group_user,1,1,1,1`

tras modificar esta linea debemos **descomentar** la carpeta security en el manifest
Y ojo, que no se nos olvide de añadir la carpeta **data**

````Python
  # always loaded
    'data': [
        'security/ir.model.access.csv',
        'views/views.xml',
        'views/templates.xml',
        'data/datos.xml'
    ],

````

COMO PODEMOS COMPROBAR AL HACER UN UPGRADE EN ODOO, ya nos aparece nuesra base de datos:

![Screenshot 2024-03-11 at 1.00.29 PM.png](resources%2FScreenshot%202024-03-11%20at%201.00.29%E2%80%AFPM.png)
![Screenshot 2024-03-11 at 1.01.31 PM.png](resources%2FScreenshot%202024-03-11%20at%201.01.31%E2%80%AFPM.png)


Bien, procedemos a editar nuestra visat para poder visualizar el menu de openAcademy con nuestra tabla

### views.xml

en este campo debemos descomentar lo siguiente:
```XML
 <!-- explicit list view definition -->

    <record model="ir.ui.view" id="openacademy.listaProductos">
      <field name="name">openacademy list</field>
      <field name="model">productos</field>
      <field name="arch" type="xml">
        <tree>
           <field name="id"/> <!-- Esto lo eliminaremos en un apartado más adelante -->
            <field name="producto"/>
            <field name="viabilidad"/>
        </tree>
      </field>
    </record>


    <!-- actions opening views on models -->

    <record model="ir.actions.act_window" id="openacademy.action_window_productos">
      <field name="name">openacademy window</field>
      <field name="res_model">productos</field>
      <field name="view_mode">tree,form</field>
    </record>

  <!-- Top menu item -->

    <menuitem name="openacademy_MarcosFA" id="openacademy.menu_root"/>
    
<!--
    <menuitem name="Menu 1" id="openacademy.menu_1" parent="openacademy.menu_root"/>
    -->
    <menuitem name="LISTA TABLAS" id="openacademy.menu_2" parent="openacademy.menu_root"/>

    <!-- actions -->

    <menuitem name="PRODUCTOS" id="openacademy.menu_1_list_productos" parent="openacademy.menu_2"
              action="openacademy.action_window_productos"/>


```

TRAS HACER ESTAS MODIFICACIONES YA NOS APARECERÁN LOS SIGUIENTES CAMBIOS:

EL NOMBRE DE LA APLICACIÓN SE CAMBIÓ EN EL MANIFEST:

![Screenshot 2024-03-11 at 1.08.08 PM.png](resources%2FScreenshot%202024-03-11%20at%201.08.08%E2%80%AFPM.png)

![Screenshot 2024-03-11 at 1.11.19 PM.png](resources%2FScreenshot%202024-03-11%20at%201.11.19%E2%80%AFPM.png)
![Screenshot 2024-03-11 at 1.11.52 PM.png](resources%2FScreenshot%202024-03-11%20at%201.11.52%E2%80%AFPM.png)

Bien, ahora debemos editar nuestras tablas, para que previamente se añadan 5 productos, y que al 
esitar los datos no aparezca el id,eso se hará de la siguiente manera

APra que el id no aparezca en el formualrio de edición se debe hacer lo siguiente:

```Python
 id = fields.Integer(string="ID",widget='hidden')
```

Pero ojo tambien lo tenemos que ocultar de la vista:

```xml
  <record model="ir.ui.view" id="openacademy.listaProductos">
      <field name="name">openacademy list</field>
      <field name="model">productos</field>
      <field name="arch" type="xml">
        <tree>

            <field name="producto"/>
            <field name="viabilidad"/>
        </tree>
      </field>
    </record>
```
![Screenshot 2024-03-11 at 1.30.09 PM.png](resources%2FScreenshot%202024-03-11%20at%201.30.09%E2%80%AFPM.png)


Para añadir estos 5 Elementos a la tabla en nuestro **datos.xml** debemos escribir estos campos


```xml
<odoo>
    <data>
         <record model="productos" id="openacademy.productos">
            <!-->Aquñi indicaremso que campos tiene la talba, o haremso referencia a ellso mejro dicho<-->

            <field name="producto">Patatas</field>
            <field name="viabilidad">7.8</field>

        </record>
        <record model="productos" id="productos_manzanas">
    <field name="producto">Manzanas</field>
    <field name="viabilidad">9.5</field>
</record>

<record model="productos" id="productos_lechuga">
    <field name="producto">Lechuga</field>
    <field name="viabilidad">8.2</field>
</record>

<record model="productos" id="productos_queso">
    <field name="producto">Queso</field>
    <field name="viabilidad">6.7</field>
</record>

<record model="productos" id="productos_tomates">
    <field name="producto">Tomates</field>
    <field name="viabilidad">9.0</field>
</record>

<!-- Agrega aquí el registro para el quinto producto -->
<record model="productos" id="productos_quinto">
    <field name="producto">Producto 5</field>
    <field name="viabilidad">8.5</field>
</record>

    </data>
</odoo>


```






