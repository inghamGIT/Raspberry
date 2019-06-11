# Prácticas en Raspberry Pi

## Instalación de un stack LAMP en Raspberry Pi

Este documento relata los pasos seguidos para la instalación de una infraestructura LAMP (Linux, Apache, MySQL, PHP) en una Raspberry Pi, con el fin de poder convertirla en un servidor web. 

## Instalación de Linux (Raspbian)

Como primer paso procederemos a la instalación y configuración de un sistema oprativo para nuestra Raspberry. En este caso hemos seleccionado Linux, en su versión especial para Raspberry conocida como Raspbian, que descargaremos de la página web <https://www.raspberrypi.org/downloads/raspbian/>. Para la instalación tenemos que configurar la imagen con algún tipo de software, y utilizaremos Rufus, un programa muy sencillo para grabar imágenes a dispositivos que podemos descargar aqui: <https://rufus.ie/es_ES.html>.

Una vez descargada la imagen y ejecutado Rufus (no requiere instalación) procedemos a escribir la misma en una tarjeta microSD, que actuará como memoria de nuestra Raspberry Pi:

<img src=Rufus.png>

EL proceso es muy sencillo, simplemente seleccionamos la imagen en el botón seleccionar (arriba a la derecha), y en dispositivo seleccionaremos el lector de tarjetas donde tengamos insertada la microSD. Una vez hecho esto, hacemos click en empezar, y en unos cinco minutos habremos instalado raspbian.

## Configuración de Raspbian

Una vez instalado el SO en la pantalla de bienvenida nos pedirá un usuario y una contraseña. Las usadas en este documento son "pi" como usuario y "raspberry200" como contraseña. Deberemos también asegurarnos de que la Raspberry está correctamente conectada a internet antes de continuar.

El siguiente paso es establecer una IP fija para nuestro futuro servidor web. Para ello deberemos editar el archivo dhcpcd.conf ubicado en /etc: **_sudo nano /etc/dhcpd.conf_**

<img src=dhcpd.png>

En el archivo de configuración modificaremos los datos de la interfaz wlan0 (o eth0 en caso de conexión por cable) con las líneas siguientes (muchas de estas líneas ya estarán en el archivo, así que simplemente las descomentaremos quitando el # inicial):

**_interface wlan0_**

**_static ip_address=10.101.3.200/24_**

**_static routers=10.101.3.1_**

**_static domain_name_servers=10.101.3.1 8.8.8.8_**

Tras acabar la edición cerramos el nano y guardamos (ctrl+x, s), y reiniciamos la raspberry. Luego comprobamos desde el terminal que todo esté bien configurado tecleando **_ifconfig_**:

<img src=ifconfig.png>

Si en el apartado inet la línea lleva la dirección IP que escribimos en dhcpd.conf, significa que el proceso ha sido exitoso.

## Instalación del resto de programas necesarios

Para instalar el resto de software necesario para un servidor LAMP funcional procederemos con las siguientes instrucciones desde el terminal:

**_sudo apt update_**

**_sudo apt upgrade_**

**_sudo apt install apache2 mysql-server php php-mysql libapache2-mod-php php-xml php-mbstring_**

Si no da ningún problema, tenemos todo el software listo para poder configurarlo.

## Configuración de Apache

Apache tras la instalación debería estar correctamente configurado, comprobaremos esto modificando el archivo index.html localizado en /var/www/html. Este archivo es el que aparece en el navegador cuando introducimos la dirección IP de la raspberry en el navegador (en este caso 10.101.3.200):

<img src=apache.png>

## Configuración de MySQL

A continuación procederemos a configurar MySQL para que el resto de servicios puedan disponer de una BBDD. El primer paso es configurar el superusuario de MySQL:

**_sudo mysql -u root -p raspberry200_**

También crearemos un usuario adicional llamado pi, para que sea el que utilice los servicios:

**_mysql> CREATE USER 'pi'@'localhost' IDENTIFIED BY 'raspberry200';_**

**_mysql> quit;_**

## Configuración de PHP

PHP debería venir configurado nada más haber sido instalado. Lo comprobaremos creando un pequeño programita que nos informe del estado de los servicios PHP. Este programita irá ubicado en /var/www/html (la misma carpeta del index de apache) y lo llamaremos phpinfo.php:

<img src=php.png>

Tras escribir el archivo, probamos a ejecutarlo desde el navegador web (10.101.3.200/phpinfo.php) y el resultado debería ser el siguiente:

<img src=phpinfo.png>

Ya para terminar, escribiremos un HOLAMUNDO en php para asegurarnos de que todo ha sido instalado correctamente (yo le llamé firstpage.php, localizado en /var/www/html):

<img src=phpholamundo.png>