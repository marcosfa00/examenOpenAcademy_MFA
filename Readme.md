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




