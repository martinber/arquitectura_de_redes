BIND
====

.. todo:: Hacer

- Esta guía parte a partir de la instalación del paquete ``bind9`` en Debian,
  versión 9.10.3.

- `Documentación sobre la configuración`__.

- `Documentación de Red Hat sobre BIND`__.

__ https://www-uxsup.csx.cam.ac.uk/pub/doc/redhat/redhat7.3/rhl-rg-en-7.3/s1-bind-configuration.html

__ https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide/sec-bind

General
-------

Inicialmente existen los siguientes archivos en ``/etc/bind/``:

- ``bind.keys``.
- ``db.0``.
- ``db.127``.
- ``db.255``.
- ``db.empty``.
- ``db.local``.
- ``db.root``.
- ``named.conf``.
- ``named.conf.default-zones``.
- ``named.conf.local``.
- ``named.conf.options``.
- ``rndc.key``.
- ``zones.rfc1918``.

El archivo de configuración principal es ``/etc/bind/named.conf``, en Debian se
recomienda no editarlo ya que ese archivo lo unico que hace es importar la
configuración presente en:

Los archivos que empiezan en ``db`` contienen los registros DNS, por lo tanto
estos archivos son los que más nos importan.

- ``named.conf.default-zones``: Tiene configuradas los servidores root y las
  zonas por defecto, ya sean forward, reverse o de broadcast. Esto es
  obligatorio según la RFC 1912 sección 4.1. En mi caso define las zonas::

    // prime the server with knowledge of the root servers

    zone "." {
      type hint;
      file "/etc/bind/db.root";
    };

    // be authoritative for the localhost forward and reverse zones, and for
    // broadcast zones as per RFC 1912

    zone "localhost" {
      type master;
      file "/etc/bind/db.local";
    };

    zone "127.in-addr.arpa" {
      type master;
      file "/etc/bind/db.127";
    };

    zone "0.in-addr.arpa" {
      type master;
      file "/etc/bind/db.0";
    };

    zone "255.in-addr.arpa" {
      type master;
      file "/etc/bind/db.255";
    };

- ``named.conf.local``: No tiene nada, acá más adelante debemos poner nuestros
  dominios.

- ``named.conf.options``: Tiene las opciones del servidor, en mi caso tenía::

    options {
      directory "/var/cache/bind";

      // If there is a firewall between you and nameservers you want
      // to talk to, you may need to fix the firewall to allow multiple
      // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

      // If your ISP provided one or more IP addresses for stable
      // nameservers, you probably want to use them as forwarders.
      // Uncomment the following block, and insert the addresses replacing
      // the all-0's placeholder.

      // forwarders {
      // 	0.0.0.0;
      // };

      //========================================================================
      // If BIND logs error messages about the root key being expired,
      // you will need to update your keys.  See https://www.isc.org/bind-keys
      //========================================================================
      dnssec-validation auto;

      auth-nxdomain no;    # conform to RFC1035
      listen-on-v6 { any; };
    };

Cache
-----

Lo único que hay que hacer es agregar los servidores a consultar en
``/etc/bind/named.conf.options``, partiendo de el archivo mostrado en la sección
anterior, y agregando a ``8.8.8.8`` y ``8.8.4.4`` quedaría::

  options {
    directory "/var/cache/bind";

    // If there is a firewall between you and nameservers you want
    // to talk to, you may need to fix the firewall to allow multiple
    // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

    // If your ISP provided one or more IP addresses for stable
    // nameservers, you probably want to use them as forwarders.
    // Uncomment the following block, and insert the addresses replacing
    // the all-0's placeholder.

    forwarders {
      8.8.8.8;
      4.4.4.4;
    };

    //========================================================================
    // If BIND logs error messages about the root key being expired,
    // you will need to update your keys.  See https://www.isc.org/bind-keys
    //========================================================================
    dnssec-validation auto;

    auth-nxdomain no;    # conform to RFC1035
    listen-on-v6 { any; };
  };

.. note::
  Hay que tener cuidado de que no haya servidores DNS configurados en la
  máquina, a mi me pasó que BIND terminó preguntando a servidores listados en
  ``/etc/resolv.conf`` pero que no estaban en ``/etc/bind/named.conf.options``.

Para comprobar los archivos usar ``sudo named-checkconf``, después reiniciar el
servidor con ``sudo service bind9 restart``.

Primario
--------

Se debe indicar el dominio a administrar en ``/etc/bind/named.conf.local``,
se debe indicar ahí cuál va a ser el archivo que va ser la base de datos de
registros, en este caso voy a administrar ``midominio.test``, por lo tanto la
convención es poner los registros en ``/etc/bind/db.midominio.test``.

El archivo ``/etc/bind/named.conf.local`` quedaría::

  zone "midominio.test" {
    type master;
    file "/etc/bind/db.midominio.test";
    allow-transfer {
      10.0.0.11;
    };
  };

El archivo ``/etc/bind/db.midominio.test`` va a llevar los registros, que
explico ahora. ``allow-transfer`` especifica los servidores secundarios.

Archivo de registros
~~~~~~~~~~~~~~~~~~~~

El archivo de registros tiene columnas de datos separados por espacios. Cada
línea es un registro y los datos que lleva depende del tipo de registro.

Dentro de este archivo, hay dos directivas importantes:

- ``$ORIGIN``: Es reemplazado por el nombre de dominio que estamos configurando,
  es el escrito en ``/etc/bind/named.conf.local``.

- ``$TTL``: Permite establecer el TTL por defecto de esta zona.

Algo que hay que saber es que todos los nombres que se escriban y no terminen en
``.``, serán relativos al dominio, es decir, se le agregará ``$ORIGIN``. Por
ejemplo al escribir ``host.midominio.test`` se vuelve
``host.midominio.test.midominio.test``. Si se omite un nombre, se usará
``$ORIGIN``. Por último, si se escribe ``@`` también será reemplazado por
``$ORIGIN``.

- **A**: Asigna una IP4 a un nombre::

    {nombre} IN A {IPv4}

- **A**: Asigna una IPv6 a un nombre::

    {nombre} IN AAAA {IPv6}

- **CNAME**: Asigna un alias a un nombre::

    {alias} IN CNAME {nombre_real}

- **MX**: Especifica a qué host irán los mails que llegan a esta zona, se
  prefieren los registros con preferencia menor::

    {zona} IN MX {preferencia} {nombre_host}

- **NS**: Anuncia servidor autoritativo para cierta zona, ya sea primario o
  secundario::

    {zona} IN NS {nombre_host}

- **SOA**: Anuncia información general, los tiempos van en segundos::

    {zona} IN SOA {servidor_primario} {mail_hostmaster} (
                  {serial}
                  {time_to_refresh}
                  {time_to_retry}
                  {time_to_expire}
                  {ttl_minimo} )

  - ``{servidor_primario}``: Servidor primario de la zona.

  - ``{mail_hostmaster}``: Mail del administrador, el primer punto se transforma
    en ``@``, entonces se deben escapar los puntos. Por ej:
    ``el\.admin.midominio.test`` se vuelve ``el.admin@midominio.test``.

  - ``{serial}``: Es un número que se debe incrementar cada vez que se cambia
    algún registro para indicar cambios a los demás servidores.

  - ``{time_to_refresh}``: Indica el tiempo que debe esperar el servidor
    secundario para refrescar.

  - ``{time_to_retry}``: Indica el tiempo que debe esperar el servidor
    secundario para reintentar un refresco fallido.

  - ``{time_to_expire}``: Si no se produce un refresco en ese tiempo, el
    servidor secundario deja de responder como autoritativo.

  - ``{ttl_minimo}``: Especifica el TTL mínimo de los registros en los cachés.

El primer registro debe ser el *SOA*, los comentarios se hacen con ``;``.

El archivo ``/etc/bind/db.midominio.test`` quedaría por ejemplo::

  ; Especificar como primario a ns1.midominio.test y como administrador
  ; a admin@midominio.test
  @ IN SOA ns1 admin (
           1 ; serial
           10800 ; time_to_refresh: 3 horas
           2600 ; time_to_retry: 1 hora
           604800 ; time_to_expire: 1 semana
           86400 ) ; ttl_minimo: 1 dia

  ; Configurar como autoritativo a ns1.midominio.test y ns2.midominio.test
  @ IN NS ns1
  @ IN NS ns2

  ; Especificar IP de ns1.midominio.test y ns2.midominio.test
  ns1 IN A 10.0.0.10
  ns2 IN A 10.0.0.11

  ; Especificar IP de host1.midominio.test y host2.midominio.test
  host1 IN A 10.0.0.100
  host2 IN A 10.0.0.101

Para comprobar los archivos usar ``sudo named-checkconf`` y ``sudo
named-checkzone /etc/bind/db.midominio.test``, después reiniciar el
servidor con ``sudo service bind9 restart``.

Si se quiere delegar un subdominio, hay que simplemente agregar un record *NS*::

  subdominio IN NS ns1.subdominio
  subdominio IN NS ns2.subdominio
  ns1,subdominio IN A 20.0.0.10
  ns2,subdominio IN A 20.0.0.11

.. todo::

  - Agregar DNS reverso.

  - Aprender bien la diferencia entre zona, dominio y nombre y arreglar esto.

Secundario
----------

Se debe indicar el dominio a administrar en ``/etc/bind/named.conf.local``, se
debe indicar ahí cuál va a ser el archivo que va ser la base de datos de
registros, en este caso voy a administrar ``midominio.test``, por lo tanto la
convención es poner los registros en ``/etc/bind/db.midominio.test``.

El archivo ``/etc/bind/named.conf.local`` quedaría::

  zone "midominio.test" {
    type slave;
    file "/etc/bind/db.midominio.test";
    masters {
      10.0.0.10;
    };
  };

El archivo ``/etc/bind/db.midominio.test`` va a llevar los registros traídos del
servidor maestro indicado en ``masters``.
