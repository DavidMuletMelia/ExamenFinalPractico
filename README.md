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

![1](https://user-images.githubusercontent.com/91748517/173109851-ec5787b5-d8cf-4bf9-b607-4d3c0fa3573b.PNG)

Cuando estemos dentro, lo unico que tendremos que hacer es ejecutar el docker compose con el comando `docker-compose up -d` y esperar a que se levanten los contenedores

![2](https://user-images.githubusercontent.com/91748517/173109967-65910d6e-e7e6-4065-8713-c148c4620a7a.PNG)

![3](https://user-images.githubusercontent.com/91748517/173110001-86e6d0f2-f599-42c3-9ae3-66836ef4a9e4.PNG)

Al acabar y comprobar que los contenedores estan corriendo correctamente, ya solo queda comprobar si podemos acceder correctamente a los recursos mediante localhost

![4](https://user-images.githubusercontent.com/91748517/173110104-e7c94440-6974-47e0-b340-8fb339f55bf7.PNG)

![5](https://user-images.githubusercontent.com/91748517/173110144-c53b4c91-1972-4112-97ca-f6c577b416f5.PNG)

![6](https://user-images.githubusercontent.com/91748517/173110158-11d3c34d-74a6-4c2d-b466-f400e4bc2082.PNG)

![7](https://user-images.githubusercontent.com/91748517/173110189-fc70444c-4ded-4308-b258-62ba7ef9bc1f.PNG)

![8](https://user-images.githubusercontent.com/91748517/173110212-2d6d93d7-b150-4d45-b795-70ab5e200392.PNG)

## Preparación y subida de la imagen

Lo primero de todo que debemos hacer es loggearnos en Docker, mediante el comando `docker login`

![10](https://user-images.githubusercontent.com/91748517/173110016-8c6403d7-65d0-4701-9321-eb67f98f9304.PNG)

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

![9](https://user-images.githubusercontent.com/91748517/173110262-e93e9a14-4372-4235-8c3a-4001a119fca5.PNG)

Una vez montada debemos subirla a dockerhub, y eso lo haremos con el siguente comando `docker push davidmuletmelia/gestionproyectos:latest`

![11](https://user-images.githubusercontent.com/91748517/173110290-f113c6c3-e981-43f4-86e1-4e3adb35d1b1.PNG)

Si estos pasos se han ejecutado correctamente, nuestra imagen estará subida en dockerhub

![12](https://user-images.githubusercontent.com/91748517/173110401-09d0effb-44c9-40d9-9310-5ed08d001ebd.PNG)

![13](https://user-images.githubusercontent.com/91748517/173110420-752ea2e7-556a-40dd-ad60-29696a7a6b87.PNG)


