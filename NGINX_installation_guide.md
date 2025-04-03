# Configuración de NGINX como Proxy Inverso.

### ¿Por qué no utilizar directamente el puerto 80 en mi aplicación de Spring Boot?

En Spring Boot, el puerto por defecto para escuchar solicitudes HTTP es el 8080. Sin embargo, no es posible configurar directamente el puerto 80 en la propiedad server.port debido a restricciones de seguridad y permisos.

Aquí tienes algunas razones por las que no se recomienda usar el puerto 80 directamente:

- Privilegios de Puerto: El puerto 80 está reservado para servicios web estándar (HTTP). En sistemas operativos Unix/Linux, los puertos por debajo del 1024 requieren privilegios de superusuario para ser utilizados. Ejecutar una aplicación Spring Boot con privilegios de superusuario no es seguro y no se recomienda.

- Conflictos con Servicios Existentes: El puerto 80 podría estar ocupado por otros servicios web en la misma máquina. Si intentamos usarlo directamente, podríamos enfrentarnos a conflictos y errores al iniciar nuestra aplicación.

- Recomendaciones de Seguridad: Por razones de seguridad, se sugiere utilizar un puerto no privilegiado (por encima del 1024) para aplicaciones web. Esto ayuda a evitar posibles vulnerabilidades y ataques.

### ¿Qué es un Proxy Inverso?

Un proxy inverso es un servidor que actúa como intermediario entre los clientes (navegadores, aplicaciones móviles, etc.) y los servidores de aplicaciones.

NGINX se utiliza comúnmente como un proxy inverso debido a su eficiencia y flexibilidad.
Ventajas de Usar un Proxy Inverso con NGINX:

- Seguridad: El proxy inverso oculta los detalles de la infraestructura detrás de él, protegiendo los servidores de aplicaciones de ataques directos.

- Balanceo de Carga: NGINX puede distribuir las solicitudes entrantes entre varios servidores de aplicaciones, mejorando la escalabilidad.

- Caché: Puede almacenar en caché respuestas para reducir la carga en los servidores de aplicaciones.

- SSL/TLS: NGINX puede manejar la terminación SSL/TLS, liberando a los servidores de aplicaciones de esta tarea. Esto significa que NGINX se encarga de descifrar las solicitudes cifradas (HTTPS) enviadas por el cliente y luego reenvía las solicitudes sin cifrar al servidor de aplicaciones (que puede estar ejecutando Spring Boot).

## Instalar NGINX en AWS Ubuntu

### Paso 1: Instalar NGINX

Para instalar NGINX en AWS Ubuntu, utilice el siguiente comando:

   ```bash
   sudo apt install nginx
   ```

### Paso 2: Validar el servicio NGINX

Debemos validar que el servicio NGINX se encuentra activo `active` y ejecutándose. 

   ```bash
   sudo service nginx status  
   ```

También se puede validar en el navegador con la dirección ip, donde veremos la página default de NGINX.

## Configurar NGINX como proxy inverso

Para configurar un proxy inverso de NGINX, puedes hacerlo directamente en el archivo `nginx.conf` o puedes optar por la estructura más organizada de tener carpetas `sites-available` y `sites-enabled`. En la configuración directa en `nginx.conf`, generalmente buscarías la sección http y agregarías las directivas necesarias para configurar el proxy inverso. 

Por otro lado, al usar las carpetas `sites-available` y `sites-enabled`, puedes crear archivos de configuración separados para cada sitio o aplicación que necesite un proxy inverso, lo que puede ser más fácil de mantener en entornos con múltiples configuraciones. Una vez que hayas configurado los archivos de configuración en sites-available, puedes habilitarlos creando enlaces simbólicos desde sites-enabled. Esto permite una gestión más modular y organizada de las configuraciones de proxy inverso en NGINX.

### Paso 1. Configurar archivo app

Cambiarse al directorio de configuración de NGINX, donde podemos configurar los sitios disponibles

   ```bash
   cd /etc/nginx/sites-available/
   ```

### Paso 2. Crear archivo de configuración

Es necesario configurar el archivo app utilizando Nano o Vim, para ello abrimos el archivo el archivo en el editor de preferencia:

   ```bash
   sudo vi app
   ```

Dentro del editor copiamos lo siguiente. Ten en cuenta que el valor de `server_name` es la dirección ip de la instancia de AWS

   ```bash
   # This configuration effectively sets up a reverse proxy
   # server on port 80 that forwards incoming requests to a
   # backend server running on localhost:8080

   server {
      listen   	80;
      listen   	[::]:80;
      server_name  localhost;

      location / {
      proxy_pass http://localhost:8080;
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
      }
   }
   ```

### Paso 3: Crear enlaces entre `site-enabled` y `site-available`

- sites-available: Este directorio se utiliza para almacenar archivos de configuración de sitios web disponibles. Estos archivos pueden contener la configuración de diferentes sitios web que podrían ser servidos por NGINX, pero no están activos actualmente.

- sites-enabled: En este directorio se colocan enlaces simbólicos (también conocidos como "symlinks") a los archivos de configuración de los sitios web que están activos y se están sirviendo actualmente. NGINX lee la configuración de los sitios web desde estos archivos en lugar de los archivos ubicados directamente en sites-available.

Crear enlace con el comando:

   ```bash
   sudo ln -s /etc/nginx/sites-available/app /etc/nginx/sites-enabled
   ```

### Paso 4. Cambiar la configuración principal de nginx en vim o nano.

Regresamos a la carpeta de configuración de NGINX que se encuentra en la ruta `/etc/nginx` y abrimos el archivo `nginx.conf` en nuestro editor de texto de preferencia (nano o vim)

   ```bash
   sudo vi /etc/nginx/nginx.conf
   ```

En la sección `http` verificar que se encuentre la línea `include /etc/nginx/sites-enabled/*;` Si no se encuentra se debe agregar debajo de la línea `include /etc/nginx/conf.d/*.conf;`

### Paso 5. Validar sintaxis de configuración

Para validar la sintaxis de las configuraciones de nginx utilizamos:

   ```bash
   sudo nginx -t
   ```

En caso de que todo se encuentre funcionando correctamente debemos obtener por salida el mensaje:

   ```bash
   nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
   nginx: configuration file /etc/nginx/nginx.conf test is successful
   ```

### Paso 6. Reiniciar servicio de NGINX

   ```bash
   sudo service nginx restart
   ```

### Paso 7. Probar la App

En el navegador es necesario conectarse utilizando la dirección ip de la instancia y como endpoint las rutas del frontend generadas en html.

## Nota

Siempre podemos reiniciar el sistema Ubuntu para que se reflejen los cambios necesarios

   ```bash
   sudo systemctl daemon-reload
   ```