# TAREA SERVIDOR LAMP
Kevin Martínez Martínez

# 1. Objetivo de la práctica
El objetivo principal de esta práctica es que aprendamos a desplegar una aplicación web
estándar sobre un entorno o stack LAMP. Ya sabemos cómo instalar Apache Web Server, por lo
que tendremos que agregar un servidor de bases de datos (MySQL/MariaDB) y el intérprete de
PHP. Al final de la práctica tendremos un servidor web Apache con un CMS Wordpress
instalado y funcionando.

## 1.1. Qué necesitamos para instalar Wordpress.
Antes de realizar el despliegue de una aplicación web, debemos tener claros los requisitos de
la misma, tanto de servicios como versiones, usuarios, permisos, etc.

En el caso de Wordpress podemos echar un vistazo en su web oficial:

![enunciado](./img/imgEnunciado01.png)

Si pinchamos en el botón ‘Consigue Wordpress’ nos llevará a una sección en la que además de
poder descargarlo, si navegamos hacia abajo, podremos ver un apartado de ‘Requisitos’:

![enunciado](./img/imgEnunciado02.png)

Como vemos, los requisitos más importantes que debemos cumplir son:

![enunciado](./img/imgEnunciado03.png)

# 2. Instalación de los componentes necesarios.

Una vez que estamos dentro de nuestro servidor, es buena idea actualizar los repositorios
antes de empezar con las instalaciones. Para ello haremos un update y un upgrade de nuestro
sistema:

```bash
# sudo apt update

# sudo apt upgrade
```

## 2.1. Instalación de Apache Web Server

Si ya tienes una máquina con Apache instalado, puedes saltarte este paso.

Vamos a instalar Apache desde nuestro terminal, con el comando:
```bash
# sudo apt install apache2
```

![enunciado](./img/imgEnunciado04.png)

Una vez que lo hayas instalado, vamos a iniciarlo con el comando:
```bash
# sudo systemctl start apache2
```

![enunciado](./img/imgEnunciado05.png)

Si quisiéramos parar el servicio, ejecutaríamos el comando:
```bash
# sudo systemctl stop apache2
```
![enunciado](./img/imgEnunciado06.png)

Para reiniciar el servicio de Apache, usaremos el comando:
```bash
# sudo systemctl restart apache2
```
![enunciado](./img/imgEnunciado07.png)

Y si queremos ver en qué estado está el servicio o si da algún mensaje de error, ejecutamos el
comando:
```bash
# sudo systemctl status apache2
```
![enunciado](./img/imgEnunciado08.png)

-> APACHE ya lo tenemos instalado:

![apache2](./img/img01.png)


## 2.2. Instalación de PHP

Vamos a instalar PHP desde nuestro terminal, con el comando:

```bash
sudo apt install php
```
Si queremos instalar las librerías más habituales, podemos usar el comando:

```bash
sudo apt install php-common
```

![php](./img/img01_1.png)
![php](./img/img01_2.png)
![php](./img/img01_3.png)
![php](./img/img02.png)
![php](./img/img02_1.png)
![php](./img/img02_2.png)


NOTA; Si queremos instalar todas las librerías que haya disponibles para Php en nuestro
repositorio:

```bash
sudo apt install php-*
```

Si queremos comprobar la versión de Php que tenemos instalada usamos el comando:
```bash
php --version ó php -v
```
![php](./img/img03.png)


## 2.3. Instalación de MariaDB Server.
Para instalar MariaDB server debemos seguir los siguientes pasos:

1. Instalar mariadb-server:
```bash
sudo apt install mariadb-server
```
![mariadb](./img/img04.png)
![mariadb](./img/img04_1.png)
![mariadb](./img/img04_2.png)
![mariadb](./img/img04_3.png)
![mariadb](./img/img04_4.png)
![mariadb](./img/img04_5.png)
![mariadb](./img/img04_6.png)
![mariadb](./img/img05.png)


Para arrancar/parar/reiniciar/ver estado del servicio utilizaremos los siguientes comandos:

```bash
# sudo systemctl start mysqld
# sudo systemctl stop mysqld
# sudo systemctl restart mysqld
sudo systemctl status mysqld
```
![mariadb](./img/img06.png)


2. Establecer la seguridad del servidor de bases de datos:
```bash
sudo mysql_secure_installation
```
>Contraseña : kevin


Si queremos crear una nueva contraseña (o si es la primera vez que lo ejecutamos) damos a
intro y continuamos.

Cuando nos pregunte si deseamos cambiar la contraseña de root, le contestamos ‘y’, e
introducimos la nueva contraseña (recuerda que debe cumplir las directrices de seguridad:
longitud, mayúsculas, minúsculas, números y caracteres especiales)

Eliminamos la posibilidad de usar usuarios anónimos y de conectarse de forma remota, y
borramos las bases de datos de prueba. Le confirmamos que recargue la tabla de privilegios.

Ya tenemos instalado todo lo necesario para instalar cualquier aplicación basada en PHP +
MySQL.

![secureInstalation](./img/img07.png)
![secureInstalation](./img/img08.png)
![secureInstalation](./img/img09.png)
![secureInstalation](./img/img10.png)
![secureInstalation](./img/img11.png)
![secureInstalation](./img/img12.png)
![secureInstalation](./img/img13.png)
![secureInstalation](./img/img14.png)





# 3. Instalación de Wordpress
Para instalar Wordpress, como para la mayoría de aplicaciones web, necesitaremos seguir los
siguientes pasos:
* Crear un directorio en Apache donde pondremos los ficheros de instalación, asociado a
un ‘virtualhost’ que gestionará las peticiones a nuestra aplicación.

* Crear un usuario y una base de datos para que la aplicación pueda desplegar todas las
tablas necesarias durante el proceso de despliegue.

## 3.1. Creación del virtualhost para nuestro sitio web
Vamos a crear un espacio para nuestro gestor de contenidos o sitio web y crearemos un
virtualhost en Apache para gestionarlo. Suponemos que vamos a trabajar con un dominio
ficticio **tuApellido-wp.com**.

Creamos nuestro sitio en el directorio de configuración de Apache:

```bash
sudo nano /etc/apache2/sites-available/tuApellido-wp.com.conf
```
![wordpress](./img/img15.png)

El contenido del fichero será:
```bash
<VirtualHost *:80>
    ServerAdmin tuApellido@misitio.com
    ServerName tuApellido-wp.com
    ServerAlias www.tuApellido-wp.com
    DocumentRoot /var/www/tuApellido-wp.com
</VirtualHost>
```
Guardamos y salimos.
![wordpress](./img/img16.png)


Si activamos ahora el sitio, puede que nos devuelva un error, ya que aún no existe la carpeta
wordpress. Podemos hacer la activación una vez que hayamos descomprimido los ficheros de
instalación de wordpress.

## 3.2. Descarga y despliegue de los ficheros de instalación.
Para descargar wordpress en nuestro servidor vamos a utilizar **wget**, por lo que debemos saber
la url de descarga del paquete de Wordpress para poder usar esta utilidad.

Si nos fijamos en el botón de descarga de la página de Wordpress y seleccionamos con el
botón derecho del ratón la opción ‘copiar dirección de enlace’, obtendremos la url de
descarga: https://es.wordpress.org/latest-es_ES.zip

Descargamos los ficheros de wordpress. Con la opción -P nos creará la carpeta.

```bash
sudo wget https://es.wordpress.org/latest-es_ES.zip -P /var/www
```
![wordpress](./img/img17.png)


Descomprimimos el paquete descargado usando la herramienta unzip. Usamos la opción -d
para indicar la carpeta destino:
```bash
sudo unzip /var/www/latest-es_ES.zip -d /var/www
```
![wordpress](./img/img18.png)
![wordpress](./img/img19.png)
![wordpress](./img/img20.png)


Te creará una carpeta llamada **wordpress** que cambiaremos su nombre nuestra carpeta
pública **tuApellido.com**:

```bash
sudo mv /var/www/wordpress /var/www/tuApellido-wp.com
```
![wordpress](./img/img21.png)


Para que Apache tenga el control de las carpetas durante la instalación, hay que hacerlo
propietario de todas las carpetas de Wordpress:

```bash
sudo chown -R www-data:www-data /var/www/tuApellido-wp.com
```
![wordpress](./img/img22.png)

## 3.3. Creación de la base de datos y el usuario para la instalación
Vamos a crear una base de datos llamada ‘wordpress’ y un usuario llamado ‘wordpress’ en
MariaDB.

Accedemos a la consola de MariaDB:

```bash
mysql -u root -p
```

```bash
MariaDB[ ]> CREATE DATABASE IF NOT EXISTS wordpress CHARACTER SET = ‘utf8mb4’;
MariaDB[ ]> CREATE USER IF NOT EXISTS ‘wordpress’@’localhost’ IDENTIFIED BY
‘W0rdpress22#’;
MariaDB[ ]> GRANT ALL PRIVILEGES ON wordpress.* to ‘wordpress’@’localhost’;
MariaDB[ ]> FLUSH PRIVILEGES;
MariaDB[ ]> QUIT;
```
![mysql](./img/img23.png)


## 3.4. Activación del sitio web.
Una vez que tenemos todos los ficheros en sus carpetas y hemos creado la base de datos para
la instalación de Wordpress, vamos a activar el sitio web. Para ello utilizaremos el comando
a2ensite de Apache:

```bash
sudo a2ensite tuApellido-wp.com.conf
sudo systemctl reload apache2
```
![activacion](./img/img24.png)
![activacion](./img/img25.png)
![activacion](./img/img26.png)

## 3.5. Desplegando la aplicación desde el navegador
Si todo ha ido bien, al teclear nuestro dominio en el navegador, nos aparecerá la pantalla de
bienvenida del instalador de Wordpress:
![wordpress](./img/img27.png)


Hacemos clic en ‘Vamos a ello!’ para iniciar el proceso, rellenando los datos que nos pide:
![wordpress](./img/img28.png)


Si ha conseguido conectar con la base de datos y no ha habido ninguna incidencia, nos
mostrará un mensaje de confirmación para iniciar la instalación:
![wordpress](./img/img29.png)


Ahora nos solicitará información de personalización de nuestro sitio web y la creación de un
usuario administrador del CMS:
![wordpress](./img/img30.png)


Una vez hecho esto, le damos a ‘Instalar Wordpress’ y el proceso se realizará de forma
automática. Al finalizar nos mostrará un mensaje de confirmación:
![wordpress](./img/img31.png)


Ya podemos entrar en el panel del CMS pulsando en el botón ‘Acceder’.
![wordpress](./img/img32.png)
![wordpress](./img/img33.png)
![wordpress](./img/img34.png)

