# ExamenFinalPractico

## Introducción
  
> En esta parte practica del examen desplegaremos nuestro proyecto en docker, mediante el docker compose, haciendo uso de : mysql, phpmyadmin y tomcat.
  Finalmente crearemos una imagen a partir del despliegue y lo subiremos a DockerHub.
  
## Configuración del archivo docker-compose

```
version: '3.3'
services:
   db:
     image: mysql:5.7
     volumes:
       - db_vol:/var/lib/mysql
       - ./mysql-dump:/docker-entrypoint-initdb.d
     environment:
       MYSQL_ROOT_PASSWORD: root
       MYSQL_DATABASE: proyecto
       MYSQL_USER: testuser
       MYSQL_PASSWORD: root
     ports:
       - 3306:3306
   phpmyadmin:
    depends_on:
      - db
    image: phpmyadmin/phpmyadmin
    ports:
      - '8081:80'
    environment:
      PMA_HOST: db
      MYSQL_ROOT_PASSWORD: root
   web:
    build:
      context: .       
    depends_on:
      - db
    image: tomcat
    volumes:
            - ./target/Gestion_Proyectos.war:/usr/local/tomcat/webapps/Gestion_Proyectos.war
    ports:
      - '8080:8080'
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: proyecto
      MYSQL_USER: testuser
      MYSQL_PASSWORD: root
volumes:
    db_vol:      
```

## Pasos para el despliegue de la aplicación

Una vez creado el archivo docker-compose, necesitaremos tener todo los recursos en el mismo directorio.

Los recursos que utilizaremos son:

```
 -El archivo docker-compose.yml
 -El archivo .war
 -La base de datos, en formato .sql
 -Dockerfile
 ```

Una vez tengamos todo esto en el mismo directorio, lo que tendremos que hacer es abrir una consola, preferiblemente de Bash, y situarnos en el directorio, en mi caso es `cd desktop/dockerdefinitivo`

Cuando estemos dentro, lo unico que tendremos que hacer es ejecutar el docker compose con el comando `docker-compose up -d` y esperar a que se levanten los contenedores

Al acabar y comprobar que los contenedores estan corriendo correctamente, ya solo queda comprobar si podemos acceder correctamente a los recursos mediante localhost


## Preparación y subida de la imagen

Lo primero de todo que debemos hacer es loggearnos en Docker, mediante el comando `docker login`

Ahora necesitamos un archivo dockerfile con la sigueiente configuración en mi caso. Hay que tener en cuenta que el dockerfile hay que tenerlo tambien en la misma ruta que los demas recursos

```
FROM tomcat:latest

LABEL maintainer="DavidMuletMelia"

ADD ./target/Gestion_Proyectos.war /usr/local/tomcat/webapps/

EXPOSE 8080

CMD ["catalina.sh", "run"]

```

Para subir la imagen necesitamos tener los contenedores los cuales queramos meter en la imagen, activos, y a continuación ejecutamos el comando 
`docker build -t davidmuletmelia/gestionproyectos:latest .` para montar la imagen

Una vez montada debemos subirla a dockerhub, y eso lo haremos con el siguente comando `docker push davidmuletmelia/gestionproyectos:latest`
