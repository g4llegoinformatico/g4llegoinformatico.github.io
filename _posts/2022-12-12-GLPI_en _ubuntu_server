---
layout: single
title: Instalar GLPI en Ubuntu Server
excerpt: "Proceso de instalación de GLPI en un servidor Ubuntu Linux"
date: 2022-12-12
classes: wide
header:
  teaser: /assets/images/glpi_ubuntu/portada.jpg
  teaser_home_page: true
categories:
  - Sistemas
tags:
  - Linux
  - GLPI
---

<p align="center">
<img src="/assets/images/glpi_ubuntu/glpi.jpg">
</p>

## Pasos previos

Primero actualizar el servidor Ubuntu 22.04

```
sudo apt-get update
sudo apt-get upgrade
```

Antes de instalar GLPI debemos conocer cuales son los requisitos mínimos de la version de GLPI.

En nuestro caso instalaremos la versión 10.0.1 por lo que debemos utilizar php 7.4, 8.0 o 8.1.

Primero instalaremos los repositorios PHP de Ondrej, quien ha sido un mantenedor de PHP para Debian durante más de una década y es ampliamente utilizado entre los servidores y usuarios de Ubuntu.

```
sudo apt install software-properties-common apt-transport-https -y
sudo add-apt-repository ppa:ondrej/php -y
sudo apt update
sudo apt upgrade
```

**Instalar Apache2**

```
  sudo apt-get -y install apache2 libapache2-mod-php8.0
```

**Instalar PHP 8.0 para Apache**

```
sudo apt install php8.0 php8.0-common libapache2-mod-php8.0 php8.0-cli
sudo systemctl restart apache2
```

A continuación instalaremos PHP-FPM (un acrónimo de FastCGI Process Manager) es una alternativa PHP muy popular (Procesador de hipertexto) Implementación de FastCGI.

```
sudo apt install php8.0-fpm php8.0-common libapache2-mod-fcgid php8.0-cli
sudo systemctl restart apache2
sudo a2enmod proxy_fcgi setenvif && sudo a2enconf php8.0-fpm
sudo systemctl restart apache2
```

Instalar los módulos de php necesarios.

```
sudo apt-get -y install php8.0 php8.0-{curl,gd,imagick,intl,apcu,memcache,imap,mysql,ldap,tidy,xmlrpc,pspell,gettext,mbstring,fpm,iconv,xml,xsl,bz2,zip}
```

Revisar la versión PHP instalada

```
php --version
```

**Instalación de MariaDB**

```
sudo apt install mariadb-server
sudo mysql_secure_installation
```

Continuación seguimos el proceso de instalación de MariaDB, definiendo la contraseña de root y ciertos parámetros.

```
NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user. If you've just installed MariaDB, and
haven't set the root password yet, you should just press enter here.

Enter current password for root (enter for none): 
OK, successfully used password, moving on...

Setting the root password or using the unix_socket ensures that nobody
can log into the MariaDB root user without the proper authorisation.

You already have your root account protected, so you can safely answer 'n'.

Switch to unix_socket authentication [Y/n] y
Enabled successfully!
Reloading privilege tables..
 ... Success!


You already have your root account protected, so you can safely answer 'n'.

Change the root password? [Y/n] y
New password: 
Re-enter new password: 
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] y
 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] y
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
```

Ingresamos a mysql y creamos la BD

```
mysql -u root -p
CREATE DATABASE glpi;
GRANT ALL PRIVILEGES ON glpi.* TO 'glpi'@'localhost' IDENTIFIED BY 'StrongDBPassword';
FLUSH PRIVILEGES;
EXIT;
```

## Instalar GLPI 10.0.1

Descargamos la última versión que por hoy es la 10.0.1

```
wget https://github.com/glpi-project/glpi/releases/download/10.0.1/glpi-10.0.1.tgz
```

Desempaquetamos en la ruta /var/www/glpi

```
sudo mv glpi-10.0.1.tgz /var/www/
cd /var/www/
sudo tar -zxvf glpi-10.0.1.tgz
```

Como resultado tendremos una carpeta llamada glpi donde encontraremos la instalación de GLPI.

A continuación debemos configurar APACHE2 para que funcione en nuestro navegador.
Creamos una copia de seguridad del archivo 000-default.conf

```
cd /etc/apache2/sites-available
sudo mv 000-default.conf 000-default.conf.bkp
```

Creamos un documento con el nombre glpi.conf con el siguiente contenido:

```
<VirtualHost *:80>
        ServerAdmin ccastrol@canaltic.pe
        DocumentRoot /var/www/glpi/
        ServerName glpi.canaltic.pe
        ServerAlias glpi.canaltic.pe

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```

Una vez teniendo nuestro archivo glpi.conf deberá ser necesario crear un enlace simbólico a la carpeta site-enable.

```
sudo ln -s /etc/apache2/sites-available/glpi.conf /etc/apache2/sites-enabled/glpi.conf
```

Modificar los permisos y derechos sobre el directorio que hemos creado en el /var/www/glpi/

```
sudo chown -R www-data:www-data /var/www/glpi
sudo chmod -R 755 /var/www/glpi
```

Reiniciar Apache2

```
sudo systemctl restart apache2.service
```


## Ingresar a la WEB de GLPI

Modificaremos nuestro archivo hosts de nuestro equipo cliente para que resuelva la dirección del servidor.

```
sudo vim /etc/hosts
___________________________________________________
<server_ip> <dominio>
```

Ingresar en el navegador, escribir la dirección ip del servidor o el dominio y seguir las indicaciones.
