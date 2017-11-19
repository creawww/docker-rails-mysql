# DOCKER RUBY ON RAILS WIDTH DATABASE MYSQL

Un entorno docker para desarrollo de aplicaciones de Ruby on Rails con base de datos Mysql
- Rails version 5.1.4 (se puede cambiar en app/Gemfile)
- Mysql version 5.7 (se puede cambiar en docker-compose.yml)

    **rails-docker**

    - **[app]**                    # Directorio de persistencia para la aplicacion web   
        - **Gemfile**              # Archivo Gemfile inicial para instalación de rails

    - **[db_file]**                # Directorio de presistencia de datos Mysql

    - **[bundle]**                 # Directorio de persistencia de las gemas de bundler

    - **Docker-compose.yml**       # Archivo con las instrucciones del docker-compose
    - **DockerFileRails**          # Archivo Dockerfile de instalacion de ruby
    - **railsSources**             # Funciones bash para resumir instrucciones docker mediante source  
    - **README.md**
    - **start_ruby_on_rails.png**


## Construir la imagen del contenedor rails (drails)

    docker-compose build

## Arrancar contenedores

    docker-compose up

Dejamos corriendo los dos contenedores y abrimos otro terminal para ejecutar los comandos de rails console

## Instalar Rails con Bundler a partir del Gemfile

    docker exec -it --user $UID:$UID drails bundler install

## Crear un nuevo proyecto de Rails con mysql

En el nuevo terminal ejecutar los comandos

    docker exec -it --user $UID:$UID drails rails new . -d mysql

nos avisa que el Gemfile exite que si queremo reemplazarlo Y

## BASE DE DATOS

Configurar rails para conectar con el docker de mysql

editamos el archivo **app/app/config/database.yml**

    default: &default
      adapter: mysql2
      encoding: utf8
      pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
      username: root
      password: toor
      host: db
      port: 3306

### Crear las bases de datos

    docker exec -it --user $UID:$UID drails rake db:create

Si da ERROR, se pueden crear las tablas a mano. Accedemos al contenedor de mysql 'dbmy' y las creamos de forma manual.


    docker exec -it dbmy /bin/bash

    mysql -u root -p
    toor

    CREATE DATABASE app_development;
    CREATE DATABASE app_test;
    exit;

    exit

### Arrancar el servidor de rails

    docker exec -it --user $UID:$UID drails rails server

y accedemos en el navegador a la url http://localhost:3000

![start_ruby_on_rails](https://image.ibb.co/faMhA6/start_ruby_on_rails.png)


                                      YA TENEMOS NUESTRO RAILS CORRIENDO !!


para apagar el servidor Ctrl-C

## Para el trabajo con contenedores

Para trabajar, siempre tiene que estar corriendo en un terminal el docker-compose y en otro ejecutar los comandos, para no repetir tanto comando de docker usaremos el source, con esto podremos utilizar directamente 'rails', 'rake', 'bundle' dentro del contenedor. Hay que ejecutarlos cada vez que se abra un nuevo terminal para trabajar con el contenedor.

cargamos el script 

    source railsSource

y en este terminal ya no tenemos que poner "docker exec -it --user $UID:$UID drails", para ejecutar el servidor simplemente pondremos:

    rails server    o    rails s

## Algunos comando de Rails

### Mostrar las rutas creadas

    rake routes

### Ejemplos de scaffold

    rails generate scaffold User nombre:string email:string

    rails generate scaffold Post titulo:string contenido:string user_id:integer

## Ejecutar las migraciones

    rake db:migrate
