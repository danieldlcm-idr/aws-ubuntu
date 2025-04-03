# Instalación de MariaDB en AWS Ubuntu

### ¿Qué es MariaDB?

MariaDB es un sistema de gestión de bases de datos relacional (RDBMS) de código abierto que es una bifurcación de MySQL. Ofrece características avanzadas, alto rendimiento y es compatible con la mayoría de las aplicaciones MySQL. MariaDB es utilizado por empresas de todo el mundo para gestionar sus datos de manera eficiente y segura.

## Conectándose a Amazon Web Service

### Paso 1: Conectarse a su instancia de AWS Linux.

Utilice su cliente SSH para conectarse a su instancia de AWS Linux.

```bash
ssh -i your_key.pem ubuntu@your_instance_public_ip
```

Es necesario que tu archivo de clave `.pem` tenga permisos de solo lectura; de lo contrario, podrías encontrarte con el error "Permissions 0644 for your_key.pem are too open". Para resolver este problema, establece permisos de solo lectura para el archivo `.pem`.

```bash
chmod 400 your_key.pem
```

El comando `chmod` en sistemas operativos tipo Unix se utiliza para cambiar los permisos de un archivo o directorio. En este comando, 400 especifica que el propietario del archivo (your_key.pem) debe tener permisos de solo lectura, mientras que todos los demás usuarios no tienen ningún permiso.

### Paso 2: Actualizar el sistema.

Antes de instalar cualquier paquete nuevo, es una buena práctica actualizar los paquetes existentes en el sistema.

   ```bash
   sudo apt update
   ```

### Paso 3: Paquetes actualizables.
Mostrar la lista de paquetes actualizables para actualizarlos posteriormente.

   ```bash
   apt list --upgradable
   ```

### Paso 4: Actualizar los paquetes..

   ```bash
   sudo apt upgrade
   ```

## Instalar MariaDB Server

### Paso 1: Instalar MariaDB Server

Para instalar MariaDB Server en AWS Ubuntu, utilice el siguiente comando:

   ```bash
   sudo apt install mariadb-server -y
   ```

### Paso 2: Verificar el servicio MariaDB

Una vez que la inicialización haya finalizado, puede verificar el estado del servicio de MariaDB.

   ```bash
   sudo service mysql status
   ```

### Paso 3: Acceda a los servicios de MariaDB

Una vez configurado, puede acceder a la consola de MariaDB utilizando el siguiente comando:

   ```bash
   sudo mysql
   ```

## Pasos siguientes

Ahora, necesitamos crear la Base de Datos donde vivirá la información de nuestro proyecto. Te invitamos a consultar [MariaDB_CreateDB](MariaDB_createDB.md) para que aprendas cómo configurar la DB, usuarios y permisos.