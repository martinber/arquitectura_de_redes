Apache2
=======

Para iniciar/parar el servidor::

  service apache2 status
  service apache2 start
  service apache2 stop
  service apache2 restart
  service apache2 reload

Configuración general
---------------------

Conozco solamente la estructura de archivos de configuración que usa Debian,
puede ser un poco distinto a otras distribuciones:

- ``/etc/apache2/apache2.conf``: Es el archivo de configuración general. No se
  debería tocar en lo posible.

- ``/etc/apache2/ports.conf``: Está incluido desde el archivo de configuración
  general, permite configurar los puertos desde donde escuchar.

- ``/etc/apache2/envvars``: Tiene variables de entorno que se pueden usar en la
  configuración.

- ``/etc/apache2/magic``: Patrones para mod_mime_magic. No se que significa.

- ``/etc/apache2/sites-available/*.conf``: Contiene un archivo para cada sitio
  web que está disponible, estos son los archivos que vamos a configurar
  nosotros. De por sí estos archivos no son usados hasta que no sean
  habilitados.

- ``/etc/apache2/sites-enabled/*.conf``: Contiene enlaces simbólicos a cada
  archivo de configuración presente en ``/etc/apache2/sites-available`` que
  queremos habilitar. Todo lo que esté acá sí será leído por Apache.

- ``/etc/apache2/conf-available/*.conf``: En esta carpeta se pueden poner
  archivos de configuración que cumplen una función específica. Por defecto no
  son leídos.

- ``/etc/apache2/conf-enabled/*.conf``: Contiene symlinks a las configuraciones
  habilitadas presentes en ``/etc/apache2/conf-available/``.

- ``/etc/apache2/mods-available/*.conf``: Módulos disponibles.

- ``/etc/apache2/mods-enabled/*.conf``: Módulos habilitados.

Para hacer symlinks de una configuración desde un *-available* a un *-enabled*
no se suele usar el comando ``ln -s`` sino unos comandos específicos:

- ``a2ensite`` y ``a2dissite``.

- ``a2enconf`` y ``a2disconf``.

- ``a2enmod`` y ``a2dismod``.

Al ejecutar el comando se le debe dar como argumento el nombre del archivo de
configuración sin incluir la extensión ``.conf``.

Todo lo que expliqué hasta ahora son los archivos de configuración, los cuales
están presentes en ``/etc/apache2``. Los sitios web en sí (es decir, los
archivos HTML, CSS, etc.) están ubicados en ``/var/www``.

Sitio web estático
------------------

Voy a hacer 2 sitios, ``sitio1.com`` y ``sitio2.com``. Primero hay que
deshabilitar los sitios habilitados, para ver los sitios habilitados::

  ls /etc/apache2/sites-enabled/

En mi caso el único habilitado era ``000-default.conf``, deshabilitar uno a
uno::

  sudo a2dissite 000-default

Ahora crear configuración de los dos sitios en ``/etc/apache2/sites-available``
copiando la configuración de un sitio ya existente::

  cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/sitio1.com.conf
  cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/sitio2.com.conf

Después hay que configurar cada uno, este es un ejemplo de configuración para
``sitio1.com.conf``::

  <VirtualHost *:80>
      ServerName sitio1.com
      ServerAlias www.sitio1.com

      DocumentRoot /var/www/sitio1
      DocumentIndex index.html

      ErrorLog ${APACHE_LOG_DIR}/error.log
      CustomLog ${APACHE_LOG_DIR}/access.log combined
  </VirtualHost>

La configuración indica que:

- El sitio responde peticiones hacia ``sitio1.com`` o ``www.sitio1.com``.

- Sirve los archivos presentes en ``/var/www/sitio/``.

- Por defecto sirve el archivo ``/var/www/sitio1/index.html``. Vendría a ser la
  página principal.

- Los logs se guardan en ``/var/log/apache2/error.log`` y
  ``/var/log/apache2/access.log``.

Finalmente hay que poner los archivos del sitio web (HTML, CSS, JS, etc.) en el
directorio especificado en la configuración. Debería quedar al menos esta
estructura de archivos en ``/var/www``::

  /var/www
  ├── sitio1
  │   └── index.html
  └── sitio2
      └── index.html

Habilitar los dos sitios, verificar que la configuración es correcta y reiniciar
el servidor::

  sudo a2ensite sitio1.com
  sudo a2ensite sitio2.com
  sudo apache2ctl configtest
  sudo service apache2 reload

Si no se tiene un DNS para probar a acceder a los dos sitios, se puede modificar
``/etc/hosts`` en la PC que realiza las peticiones. Si la IP del servidor web es
``172.17.1.1`` quedaría::

  172.17.1.1	www.sitio1.com sitio1.com
  172.17.1.1	www.sitio2.com sitio2.com

Problemas que tuve
------------------

- Por alguna razón, luego de deshabilitar todos los sitios web y después de
  reiniciar el servidor, la página web por defecto ubicada en ``/var/www/html``
  seguía siendo servida. Leí por ahí que a veces se soluciona reiniciando la PC
  pero no me funcionó. La respuesta en la que confío es en `esta`__:
  ``DocumentRoot`` tiene un valor que es usado por defecto, incluso cuando no
  hay sitios web habilitados. La solución es siempre tener un sitio web
  habilitado.

- Siempre reniego con los cachés de los navegadores, usar *Ctrl-F5* para
  recargar sin caché.

__ https://askubuntu.com/a/846654
