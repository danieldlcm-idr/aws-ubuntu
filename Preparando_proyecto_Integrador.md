# Preparando nuestro Proyecto Integrador.

El proyecto integrador se encuentra desarrollado de la siguiente manera:
- **Frontend**: HTML, CSS, JavaScript y Bootstrap.
- **Backend**: Spring boot con Java,
- **Base de datos**: MySQL.

## Acoplando frontend con backend.

El primer paso que debemos realizar para que nuestro proyecto pueda vivir de manera remota es realizar el acoplamiento de la carpeta donde se desarrolló el frontend con la carpeta donde se desarrolló el backend.

> Es necesario recordarte que el punto de acceso de nuestro frontend es el archivo `index.html` el cual se debe encontrar en el nivel principal.

Para realizar el acoplamiento, copiaremos el contenido de la carpeta del frontend dentro de la carpeta del backend, en la ruta `src/main/resources/static`

Puedes consultar el siguiente repositorio para observar la nueva estructura del proyecto
[Repositorio de ejemplo](https://github.com/sergiotrrs/aws-ec2-demo.git)

## Configurando mi URL remota.

Solicita la `IP pública` proporcionada por la instancia de AWS a la persona encargada, ya que esta será la dirección que reemplazarás en el frontend y te permitirá vincular con el servidor remoto que configuraremos más adelante.

Hasta este momento hemos desarrollado nuestra API y realizado pruebas HTTP de manera local, pero necesitamos conectarnos a una IP remota para realizar pruebas de manera remota. Por ello, localiza todas las url de tu proyecto que apunten a `localhost` y sustitúyela por la `IP pública` proporcionada.

**Revisa cada archivo de tu código para asegurarte que se realicen los cambios correctos.**

Así mismo, solicita a la persona encargada la información referente a tu instancia aprovisionada en AWS, como Región, Nombre de instancia, Id de instancia y el archivo `'nombre-par-claves'.pem` que servirá como el Par de claves para conectarte posteriormente a la instancia.

## Propiedades para Producción (Variables de entorno).

Por seguridad, es necesario ocultar las propiedades del proyecto como variables de entorno, ya que de esta manera no exponemos información sensible a nivel producción. Para ello, realizamos lo siguiente:

1. Modifica el archivo `application.properties` en la ruta `src/main/resources` 
- Este servirá exclusivamente para configuración de producción.

2. Dentro del archivo `application.properties` copiar lo siguiente:

```properties

spring.datasource.url=jdbc:mysql://${PROD_DB_HOST}:3306/${PROD_DB_NAME}
spring.datasource.username=${PROD_DB_USERNAME}
spring.datasource.password=${PROD_DB_PASSWORD}

spring.jpa.hibernate.ddl-auto=${PROD_DDL}
spring.mvc.pathmatch.matching-strategy=ant_path_matcher
```

## ¿Qué es Gradle y para qué sirve en Spring Boot?

Gradle es una herramienta de automatización de compilación que se utiliza para gestionar y construir proyectos de software. En un proyecto de Spring Boot, Gradle permite gestionar las dependencias, compilar el código, ejecutar pruebas, crear archivos ejecutables (.jar o .war) y desplegar la aplicación de manera eficiente.

### Construyendo el proyecto con Gradle.

Necesitamos ejecutar varias tareas en nuestro proyecto, tales como configurar, descargar dependencias, compilar clases Java, ejecutar pruebas y crear archivos `jar`. Para ello haremos uso de las tareas de gradle.

Tenemos dos opciones disponibles para realizar la construcción de nuestro proyecto (`'archivo'.jar`):

#### Opción 1.
Ubicar las tareas de Gradle `Gradle Task` en el IDE (Eclipse o IntelliJ). Seleccionar la carpeta del proyecto `nombre_proyecto`, seleccionar `build` y dar doble click en la opción de `build` para iniciar la construcción del proyecto.

#### Opción 2.
Ubicarse en la carpeta del proyecto y ejecutar por terminal el comando:

```bash
./gradlew build
```

Una vez finalizada la construción del proyecto, se generan una serie de carpetas y un archivo llamado `'proyecto-version'.jar` según el nombre de nuestro proyecto y la versión del mismo. Este archivo se localiza en la ruta `build/libs/'proyecto-version'.jar`.

Este archivo .jar lo utilizaremos posteriormente para el despliegue en producción.

## Pasos finales.

Para mayor organización, crear una carpeta general que contenga al mismo nivel el `'archivo'.jar` y el archivo `'nombre-par-claves'.pem` para continuar con las siguientes configuraciones de AWS.

> Podemos cambiar el nombre del archivo .jar para evitar un nombre tan largo.

Te invitamos a consultar el archivo [MariaDB_installation_guide.md](MariaDB_installation_guide.md) para conocer las próximas configuraciones necesarias para el despliegue de la Aplicación.
