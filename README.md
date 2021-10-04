# Instalación de WordPress 
> Ya tengo instalado mi servidor local (LAMP) Debian, en el Subsistema de Windows para Linux (WSL2),
> si mi sistema fuera GNU/Linux, omito los pasos de windows.

Tengo que poner el link de las instrucciones: cómo instale el server local.

<br/>  

---

<br/>

**Preparo mi directorio.**
```sh
:~$ mkdir -p projects/practica-wordpress
```

**Agrego un nuevo host**

En Debian
```sh
:~$ su -

:~# vim /etc/hosts

127.0.0.1   practica-wordpress.local
::1         practica-wordpress.local
```

En windows: block de notas como administrador y accedo al archivo hosts 
`c: > Windows > System32 > drivers > etc > hosts`

```
127.0.0.1   practica-wordpress.local
::1         practica-wordpress.local
```

**Configuro un nuevo VirtualHosts**

```sh
:~# vim /etc/apache2/sites-available/practica-wordpress.conf

<VirtualHost *:80>
	ServerName practica-wordpress.local
	DocumentRoot /home/jose/projects/practica-wordpress/www
	ErrorLog /home/jose/projects/practica-wordpress/logs/errors.log
	CustomLog /home/jose/projects/practica-wordpress/logs/access.log combined
</VirtualHost>

<Directory /home/jose/projects/practica-wordpress/www>
	Options -Indexes -MultiViews
	AllowOverride All
</Directory>
```

**Habilité mi nuevo VirtualHost en Apache**

```sh
:~# a2ensite practica-wordpress.conf
:~# service apache2 restart
```

**La regla de este comando es así:**

`a2`<acción><elemento><nombre>

- Donde `a2` es la abreviación de apache2
- <acción> pude ser habilitar `enable=en` o deshabilitar `disable=dis`
- <elemento> un sitio web `site` o un módulo de apache `module=mod`
- <nombre> el de un virtualhost (solo si el elemento es `site`) 
o el de un módulo (solo si el elemento es `mod`)

`a2`   = apache2
`en`   = enabled
`dis`  = disabled
`site` = nombre del archivo .conf
`mod`  = modulo

De esta forma los comandos válidos son:

| `a2ensite` | `a2dissite` | `a2enmod` | `a2dismod` |
|------------|-------------|-----------|------------|

### Descargar WordPress

[Ir al sitio de WordPress](https://es-mx.wordpress.org/download/).

Lo descargo, descomprimo y pongo en mi carpeta de mi entorno local `www`




### Genero la base de datos que se enlazará a mi sitio de WordPress

[Instrucciones para crear la base de datos en WordPress](https://es-mx.wordpress.org/support/article/creating-database-for-wordpress/)

```sh
:~# mysql -u root -p
#Pongo el password y doy enter
```

```sql
MariaDB [(none)]> CREATE DATABASE practica-wordpress;

MariaDB [(none)]> GRANT ALL PRIVILEGES ON practica-wordpress.* TO "jose"@"localhost" 
IDENTIFIED BY "domi";

MariaDB [(none)]> FLUSH PRIVILEGES;

MariaDB [(none)]> EXIT
```

**Borrar la base de datos**

```sql
DROP DATABASE base_de_datos;
```

**Modifico el archivo wp-config-sample.php**

Tengo que renombrarlo por wp-config.php y modificar estas líneas de código:

```php

// Esto es para que me deje actualizar e instalar plugins,
// pero no estoy seguro de utilizarlo en producción.
define('FS_METHOD','direct');

/** The name of the database for WordPress */
define( 'DB_NAME', 'practica-wordpress' );

/** MySQL database username */
define( 'DB_USER', 'jose' );

/** MySQL database password */
define( 'DB_PASSWORD', 'domi' );
```


**Copiar este código que se genera siempre de manera random**

[Aquí están las Keys que pegaré](http://api.wordpress.org/secret-key/1.1/salt)

Copeo y pego en mi archivo. Ejemplo:

```php
define('AUTH_KEY',         'r70*R?}_M3+RF`A%[:>^}|0I!v35Am+ugG opMm-c35%:*Ca/');
define('SECURE_AUTH_KEY',  'Yb/6XF{?J#Vl?em[l2H(X_oPMX.|sP@kAuyce)!+9_?phHiae');
define('LOGGED_IN_KEY',    '/QlCGejlOS%Jn>n?N9bZ,qMmze>TZE:.A/DDpFlkSLRetjw~M');
define('NONCE_KEY',        '`He_*k^{/J+sK@wu(Q!o4>Q{J;<W4+XrIa/b*=[&jBK-GO8?3');
define('AUTH_SALT',        'd:uNIAf<P-iz+]1FFj6#d.c9-V>$$1Li#,Vq#&LT=2X)-050F');
define('SECURE_AUTH_SALT', '$VTTH-H5Dum?dFsc|T.)%4SL[2ZUE^t].a_jpMh|2I%3jbpU-');
define('LOGGED_IN_SALT',   'K98Dg-y3P#3HjK}qJ@plp8I*`zYa;9ZO%|!n)lI%d p{6j|G#');
define('NONCE_SALT',       'L1jWaoXBKi6K=8oT.%Hu-vbJ{PK5bC9Tf0$K5;1t~;Ru@o[F|');
```


**Cambio el prefijo wp_**

```php
$table_prefix = 'wp_practica-wordpress';
```

> Tengo duda con estos permisos para producción.
> En local si no tengo estos así, no me permite cambiar idioma, 
> borrar o instalar plugins.

**Y cambié los permisos a mi carpeta de wordpressl**

A la carpeta wp-content le puse estos permisos
```sh
:~$ chmod -R 777
```

A los demás directorios les puse permiso
```sh
:~$ chmod -R 755
```

Y a los archivos
```sh
:~$ chmod -R 644
```

### Instalando WordPress

Entro a mi localhost: 
[practica-wordpress.local](http://practica-wordpress.local/wp-admin/install.php)

- Idioma Español
- Titulo del sitio: Dragon Negro
- Usuario: jose
- Password: domi
[x] Confirmar password

[x] No indexar el sitio 
Por ahora no queremos que los motores de busqueda indexen el sitio. 
Ya que lo pongamos en producción entonces lo desmarco.

Una vez instalado entro con mi usuario y password.