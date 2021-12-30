PROYECTO DE DESPLIEGUE CON DOCKER DE UN SERVIDOR DE CONEXIONES REMOTAS
			APACHE GUACAMOLE
***********************************************************************

Apache guacamole es un servidor de conexiones remotas que acepta los 
protocolos VNC, RDP, y SSH entre otros. Es una software Open Source
que he montado en producción en la empresa Pont Grup Correduría de
Seguros S.L.

El docker-compose.yml está compuesto de 4 máquinas:

+ init-guac-db: Crea la base de datos que utilizará guacamole. Sólo es
necesario levantarla la primera vez para que cree el archivo .sql en
el directorio /data/postgres

+ guacamole-daemon: Es el servicio guacd. Es el que se encarga de
establecer las conexiones remotas con los clientes.

+ guacamole-web: Es el servidor web Tomcat que se encarga de darnos
el panel web con el cual trabaja la aplicación.

+guacamole-db: un postgres que contendrá los datos de la aplicación.


Para desplegar la aplicación, tan solo hace falta crear y configurar
 el .env (como en el .env.example) y lanzar, primero, init-guac-db para
crear el archivo .sql de la base de datos:
	
	docker-compose up -d init-guac-db

Luego basta con levantar el docker-compose entero:

	docker-compose up -d

Ya podréis acceder al panel de administración a través de http://localhost.
El usuario por defecto es guacadmin y la contraseña guacadmin.

Notas aclarativas:
+++++++++++++++++++

El servidor web usa el puerto 8080 por defecto, está redirigido al
puerto 80 pero si lo tenéis ocupado podéis cambiarlo por el que queráis
modificando el docker-compose.yml en la linea ports después de web:

  web:
    image: guacamole/guacamole
    container_name: guacamole-web
    restart: always
    ports:
      - "80:8080"
    volumes:
      - $PWD/conf/guacamole:/etc/guacamole
    environment:
      GUACAMOLE_HOME: /etc/guacamole
      GUACD_HOSTNAME: guacd
      POSTGRES_HOSTNAME: postgres
      POSTGRES_DATABASE: ${POSTGRES_USER}
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
    depends_on:
      - postgres
      - guacd

+++++++++++++++++++

Este proyecto se ha creado a través de la recopilación de otros
repositorios y de información gratuita sacada de internet; por lo cual
sois libres de usar, modificar y distribuir este proyecto libremente.