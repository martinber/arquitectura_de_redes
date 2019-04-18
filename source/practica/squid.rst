Squid
=====

Acá hay algunas notas sobre Squid pero no mucho más.

El archivo de configuración es ``/etc/squid/squid.conf``. Está bueno leer los
primeros comentarios de ese archivo porque explica un poco la estructura.

El archivo es MUY largo, está dividido en secciones. Para navegarlo yo busco con
*Ctrl-F* el string ``# -----------``. Las secciones que usamos son:

- ``NETWORK OPTIONS``.

- ``MEMORY CACHE OPTIONS``.

- ``DISK CACHE OPTIONS``.

- ``OPTIONS FOR TROUBLESHOOTING``.

- ``ACCESS CONTROLS``.

Cada opción de configuración tiene un nombre o *KEY*, por lo tanto para buscar
de opción en opción usar ``#  KEY``, o para buscar una opción específica buscar
por ejemplo ``KEY: request_timeout``.

Para iniciar/parar el servidor::

  service squid status
  service squid start
  service squid stop
  service squid restart
  service squid reload

Configuración general
---------------------

Estas son las opciones más importantes que encontré.

Network options
~~~~~~~~~~~~~~~

- ``http_port``: Puerto en donde escucha el servidor, además se puede
  especificar una IP determinada para que sólo escuche en una interfaz.

- ``icp_port``: Puerto que usa para comunicarse con otros cachés vecinos. Por
  defecto está deshabilitado.

Memory cache options
~~~~~~~~~~~~~~~~~~~~

- ``cache_mem``: Memoria RAM reservada para cachés.

- ``maximum_object_size_in_memory``: Limite de tamaño de objeto a almacenar en
  RAM.

Disk cache options
~~~~~~~~~~~~~~~~~~

- ``cache_dir``: Ubicación de los cachés y máximo tamaño permitido. Por defecto
  no tiene nada por lo tanto los cachés en disco están deshabilitados, aunque
  tiene una línea comentada con valores estándar::

    cache_dir ufs /var/spool/squid 100 16 256

  ``ufs`` es uno de los formatos que se pueden usar, después se especifica el
  directorio (debe existir previamente ya que no será creado). Luego el primer
  número es el espacio en disco a usar en MB, el segundo es el número de
  directorios de primer nivel a usar, y el tercero el número de directorios de
  segundo nivel a usar.

Options for troubleshooting
~~~~~~~~~~~~~~~~~~~~~~~~~~~

- ``cache_log``: Archivo de log, por defecto es ``/var/log/squid/cache.log``.

Access controls
~~~~~~~~~~~~~~~

Primero se deben establecer los Access Control Lists (ACLs) que son simplemente
listas de puertos, IPs, protocolos, etc. Luego se admite o niega el acceso a
ciertas listas.

- ``acl``: Establece una lista de puertos, IPs, protocolos, horarios, etc. Ver
  los comentarios del archivo para aprender como se usa porque es muy extenso.

- ``http_access``: Permite (*allow*) o impide (*deny*) el acceso dependiendo de
  las ACLs definidas, la última línea debería siempre ser ``http_access deny
  all``. Por ejemplo::

    http_access deny acl1 acl2...
    http_access allow acl1 acl2...
    http_access allow !acl
    http_access deny all

  De todas formas ya hay varias reglas por defecto, lo recomendado es agregar
  las que uno necesite luego de la línea::

    # INSERT YOUR OWN RULE(S) HERE TO ALLOW ACCESS FROM YOUR CLIENTS

Casos
-----

Proxy transparente o inline
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Se coloca al proxy como gateway de la red a la cual se quiere servir. Se debe
activar el forwarding IP::

  sudo bash -c "echo 1 > /proc/sys/net/ipv4/ip_forward"

Tengo entendido que se puede configurar a Squid en el puerto 80, o se puede
dejarlo en cualquier puerto y agregar una regla iptables para redirigir el
tráfico HTTP::

  iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 80 -j REDIRECT --to-port 3128

En ese caso la interfaz de entrada es ``eth0``.
