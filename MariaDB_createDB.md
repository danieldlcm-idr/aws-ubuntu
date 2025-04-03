# Configuración de una Base de Datos en MariaDB.

En esta guía, aprenderás cómo crear una base de datos en MariaDB instalada en una instancia EC2 con Ubuntu. La base de datos estará diseñada para una aplicación de comercio electrónico.

## Pasos para Crear la Base de Datos.

### Paso 1: Crea la Base de Datos.

Ejecuta el siguiente comando SQL para crear la base de datos.

   ```sql
    CREATE DATABASE ecommerce;
   ```

### Paso 2: Crea Usuarios.

#### a. Usuario con Todos los Privilegios.
Crea un usuario con todos los privilegios en la base de datos de comercio electrónico. Reemplaza `username` y `password`  con tu nombre de usuario y contraseña deseados:

- Crear usuario

   ```sql
   CREATE USER 'username'@'%' IDENTIFIED BY 'password';
   ```

- Otorgar permisos al usuario sobre la base de datos

   ```sql
   GRANT ALL PRIVILEGES ON ecommerce.* TO 'username'@'%';
   ```

- Refrescar los permisos

   ```sql
   FLUSH PRIVILEGES;
   ```

#### b. Usuario de Solo Lectura.
Crea un usuario con privilegios de solo lectura en la base de datos de comercio electrónico. Reemplaza 'readonly_username' y 'readonly_password' con tu nombre de usuario y contraseña deseados:

   ```sql
    CREATE USER 'readonly_username'@'localhost' IDENTIFIED BY 'readonly_password';
    GRANT SELECT ON ecommerce.* TO 'readonly_username'@'localhost';
    FLUSH PRIVILEGES;
   ```

### Paso 3: Validar que el Usuario se creó.

Puedes verificar que la base de datos y los usuarios se hayan creado correctamente listando las bases de datos y los usuarios.

- Verificar usuarios

   ```sql
   SELECT user FROM mysql.user;
   ```

- Mostrar Bases de Datos

   ```sql
   SHOW DATABASES;
   ```

### Paso 4. Verifica los Permisos del Usuario

Puedes verificar los permisos de un usuario específico ejecutando la siguiente consulta SQL.

   ```sql
   SHOW GRANTS FOR 'username'@'%';
   ```

### Paso 5. Salir de MariaDB.
Salir de la interfaz de línea de comandos de MariaDB.

   ```sql
    EXIT;
   ```

## Pasos siguientes

Necesitamos configurar la Aplicación de SpringBoot, para ello consulta el archivo [Configurando Proyecto de SpringBoot](SpringBoot_App_installation_guide.md)