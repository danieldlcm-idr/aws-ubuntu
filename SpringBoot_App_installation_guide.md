# Configuración para la ejecución de una Aplicación Spring Boot en AWS EC2.

### ¿Qué es Spring Boot?

Spring Boot es framework de código abierto para crear aplicaciones Java de manera rápida y con un mínimo de configuración. Está construido sobre el proyecto Spring Framework y proporciona un conjunto de herramientas y convenciones que simplifican el desarrollo de aplicaciones empresariales.

### ¿Qué es una Api Rest?

Una API REST (Representational State Transfer) es una arquitectura para el diseño de sistemas distribuidos basados en la web. En una API REST, los recursos (como datos o funcionalidades) se representan como URI (Identificadores de Recursos Uniformes) y se pueden acceder a través de operaciones estándar HTTP (GET, POST, PUT, DELETE, etc.).

Una API REST permite a los desarrolladores crear aplicaciones que pueden comunicarse entre sí a través de Internet de manera flexible y ligera. Facilita la integración y el intercambio de datos entre diferentes sistemas y aplicaciones, lo que resulta en una mayor eficiencia y escalabilidad.

## Instalar Java 17 en AWS Ubuntu

### Paso 1: Instalar OpenJDK.

Para trabajar con la instancia es necesario instalar OpenJDK

   ```bash
   sudo apt install openjdk-17-jdk
   ```

### Paso 2: Validar la versión

Debemos validar que se haya instalado openjdk 17.0.13 

   ```bash
   java --version
   ```

## Configurando los archivos en la instancia

### Paso 1. Subir el archivo jar

Salir de la conexión del servidor escribiendo `exit`. En la misma carpeta donde vive nuestro archivo `pem` se debe encontrar el archivo `jar` generado. Es necesario subir el archivo .jar al servidor

   ```bash
   scp -i your_key.pem archivo.jar ubuntu@direccionIP:archivo.jar
   ```

### Paso 2. Reubicar el archivo jar

Volver a conectarse al servidor y validar que el archivo existe, ejecutando `ls -la`

Es necesario copiar el archivo .jar a una nueva carpeta llamada `src`. Para ello creamos la carpeta y copiamos el archivo

   ```bash
   sudo mkdir src
   sudo cp 'archivo'.jar /home/ubuntu/src
   ```

### Paso 3: Crear archivo service

Es necesario crear un archivo `service` que nos permite exponer la información de nuestra base de datos y credenciales del usuario.
Para ello, salimos del servidor y dentro de la carpeta que hemos trabajado de manera local crearemos una archivo llamado `'archivo'.service`

   ```bash
   touch 'archivo'.service
   ```

Una vez creado, copiamos la siguiente información dentro del archivo service. Es necesario que modifiques la información con base en la configuración definida en la base de datos de MariaDB (db_name, userDB_name y password) de las líneas 10 - 12.
Así mismo, modifica el nombre del archivo jar (línea 8).

   ```bash
   [Unit]
   Description=Spring Boot App
   After=syslog.target

   [Service]
   User=ubuntu
   WorkingDirectory=/home/ubuntu/src
   ExecStart=/usr/bin/java -jar /home/ubuntu/src/archivo.jar
   Environment="PROD_DB_HOST=localhost"
   Environment="PROD_DB_NAME=db_name"
   Environment="PROD_DB_USERNAME=userDB_name"
   Environment="PROD_DB_PASSWORD=password"
   Environment="PROD_DDL=update"
   Restart=always
   SuccessExitStatus=143

   [Install]
   WantedBy=multi-user.target
   ```

### Paso 4. Subir el archivo service

Es necesario subir el archivo .service al servidor

   ```bash
   scp -i your_key.pem archivo.service ubuntu@direccionIP:archivo.service
   ```

### Paso 5. Validar el contenido del archivo service

Volver a conectarse al servidor y validar que el archivo existe, así como la información dentro de él.

   ```bash
   cat archivo.service
   ```

### Paso 6. Copiar service en los servicios de la instancia

Es necesario que el archivo service se encuentre dentro de los servicios de la instancia de AWS, para ello copiamos el archivo que subimos previamente.

   ```bash 
   sudo cp archivo.service /etc/systemd/system/
   ```

### Paso 7. Validar el estado del servicio

Debemos validar que `archivo.service` forme parte de los servicios de la instancia y se encuentre activado (enabled).

   ```bash
   sudo service 'archivo' status
   ```

### Paso 8. Habilitar el servicio

Habilitar el servicio y permitir que inicie cuando el sistema operativo inicia

   ```bash
   sudo systemctl enable 'archivo'
   ```

### Paso 9. Iniciar el servicio.

Para poder iniciar el servicio en AWS debemos activarlo con el comando.

   ```bash
   sudo service 'ecommerce' start
   ```

### Paso 10. Validar el servicio

Validar que el servicio responde

   ```bash
   curl http://localhost:8080
   ```

Si el servicio no responde, se puede revisar el log del servicio con el comando:

   ```bash
   sudo journalctl -u ecommerce.service
   ```

Si muestra mucho contenido pueden usar la tecla `q` para salir.
   Tecla G para ir al final
   Tecla g para ir al principio


## Pasos siguientes

Debemos instalar y configurar el servidor Nginx. Para ello consulta el archivo [NGINX_installtion](/NGINX_installation_guide.md).

## Nota

Siempre podemos reiniciar el sistema Ubuntu para que se reflejen los cambios necesarios

   ```bash
   sudo systemctl daemon-reload
   ```


