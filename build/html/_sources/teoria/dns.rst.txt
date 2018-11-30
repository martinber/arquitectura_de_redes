DNS
===

Domain Name System.

- Traduce entre nombres de dominio e IPs gracias a los servidores de nombres de
  dominio (*name server*).

- Puerto 53.

- UDP para resoluciones.

- TCP para transferencias de zona.

- El dominio superior es el *root* o ``.``. Los dominios que le siguen, como
  ``com``, ``ar``, ``net``, etc. son *Top Level Domains* (TLDs).

- Los programas utilizados del lado del cliente se denominan *resolvers*.

- Los servidores DNS generalmente se identifican por nombres de dominio en lugar
  de IPs.

- Un *Fully Qualified Domain Name* (FQDN) es simplemente un nombre de dominio
  completo, especifica todos los niveles, incluyendo al menos un *Top Level
  Domain* y un *Second Level Domain*.

.. todo::

  - Aprender bien la diferencia entre zona, dominio y nombre y arreglar esto.

Header
------

.. todo:: Hacer

Jerarquía
---------

- Se delega la autoridad sobre un dominio a un servidor autoritativo, éste
  servidor puede delegar la autoridad sobre un subdominio a otro servidor.

- Cada dominio tiene al menos un servidor autoritativo que informa sobre ese
  dominio y sobre los servidores de los subdominios.

.. image:: ./jerarquia_dns.svg

.. todo:: Hacer

  Quien tiene autoridad sobre la zona

Tipos de servidores
-------------------

- Solo Cache: No es autoridad para ninguna zona. No posee base de datos (DB).
  Almacena las consultas.

- Autoritativos: Responden con datos que están almacenados localmente, a
  diferencia de los servidores no autoritativos que realizan consultas y
  mantienen un caché.

  - Primario (Maestro): Posee una base de datos con todos los registros, los
    cuales son administrados localmente. Solamente hay un solo servidor
    primario.

  - Secundario (Esclavo): Trabaja con transferencia de zona, que significa tomar
    las zonas de un primario y almacenarlas localmente. Pueden haber múltiples
    servidores secundarios.

Los servidores raíz (*root name servers*) responden por el dominio raíz y
responden con los servidores de los TLDs.

.. todo:: Tipos de registros

Tipos de consultas
------------------

- Iterativas: El servidor responde con información parcial ya que generalmente
  no conoce la respuesta completa, deriva al cliente a otro servidor con más
  información.

- Recursivas: El servidor asume la responsabilidad de proporcionar la respuesta,
  si la desconoce, se encarga de consultar otros servidores hasta obtenerla para
  finalmente proporcionar dicha respuesta al cliente.

Glue records
------------

En respuestas iterativas, el siguiente servidor de nombres a consultar se
identifica por nombre de dominio y no por IP, por lo tanto puede que haga falta
hacer otra consulta para resolverlo.

Por ejemplo supongamos que el servidor autoritativo de ``example.com`` es
``ns1.example.com``, que delega el subdominio ``a.example.com`` a
``ns1.a.example.com``.

Si se desea resolver ``host.a.example.com`` primero se consulta al
servidor autoritativo ``ns1.example.com``, que te lleva a consultar a
``ns1.a.example.com``. El problema es que si no te indica la IP de éste servidor
se crea una dependencia circular ya que se debe preguntar por la dirección de
ese servidor.

Por lo tanto el servidor que provee la delegación debe también indicar una o más
direcciones IP para el servidor mencionado en la delegación, esto se llama
*glue*.

Cacheo
------

- Si no se usaran cachés, todas las consultas empezarían en un servidor raíz lo
  que sería muy ineficiente.

- Almacenan registros por un tiempo determinado en el *Time to live* de cada
  registro. Valores típicos son del orden de los 5 días.

- Normalmente también tienen el algoritmo recursivo.

- El cacheo negativo es cuando se cachea la inexistencia de un cierto nombre de
  dominio.

.. todo:: Ver bien cuando se usan servidores de cacheo y autoritativos.

DNS reverso
-----------

- Es la consulta de nombres de dominio para cuando la dirección IP es conocida.

- Se usa el TLD ``arpa``. Para IPv4 se usa ``in-addr.arpa``, para IPv6 se usa
  ``ip6.arpa``. Las direcciones se ponen al revés.

- Se hace una consulta DNS usando un nombre de dominio que contiene en realidad
  la dirección IP, de atrás para adelante.

- Al hacer la consulta se van explorando los servidores de nombres autoritativos
  partiendo desde ``in-addr.arpa`` hacia abajo. Como los bloques de direcciones
  IP son asignados a organizaciones, cada organización tiene un servidor de
  nombre de dominio configurado para hacer DNS reverso a su bloque. En el caso
  en el cual la organización transfiere una subred a otra organización, de forma
  paralela se hace la delegación del bloque correspondiente para que funcione el
  DNS reverso.

- En el caso de IPv4, cada dominio abarca un octeto, por ejemplo para
  ``10.20.30.40`` se hace una consulta por ``40.30.20.10.in-addr.arpa``.

- Como originalmente las direcciones eran clase A, B o C (``/8``, ``/16`` y
  ``/24``). Asignar un octeto a cada dominio tenía sentido, pero al incorporar
  CIDR comenzaron a asignarse bloques de direcciones que no están alineados en
  los octetos. Por ejemplo hay dos ``/25`` en un mismo nombre de dominio
  reverso que abarca a una ``/24``. Por lo tanto la RFC 2317 estableció el
  truquito a usar para delegar lo que sería algo así como medio nombre de
  dominio.

- En el caso de IPv6 los dominios se corresponden a 4 bits, por ejemplo para
  ``2001:db8::567:89ab`` se hace una consulta por
  ``b.a.9.8.7.6.5.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.8.b.d.0.1.0.0.2.ip6.arpa``.

.. todo:: Hacer

Configuración de un servidor
----------------------------

Zonas obligatorias
~~~~~~~~~~~~~~~~~~

La RFC 1912 especifica que ciertas zonas son obligatorias, y por lo tanto ya
vienen configuradas por defecto en BIND9::

  4.1 Boot file setup

     Certain zones should always be present in nameserver configurations:

             primary         localhost               localhost
             primary         0.0.127.in-addr.arpa    127.0
             primary         255.in-addr.arpa        255
             primary         0.in-addr.arpa          0

     These are set up to either provide nameservice for "special"
     addresses, or to help eliminate accidental queries for broadcast or
     local address to be sent off to the root nameservers.  All of these
     files will contain NS and SOA records just like the other zone files
     you maintain, the exception being that you can probably make the SOA
     timers very long, since this data will never change.

     The "localhost" address is a "special" address which always refers to
     the local host.  It should contain the following line:

             localhost.      IN      A       127.0.0.1

     The "127.0" file should contain the line:

             1    PTR     localhost.

     There has been some extensive discussion about whether or not to
     append the local domain to it.  The conclusion is that "localhost."
     would be the best solution.  The reasons given include:

        "localhost" by itself is used and expected to work in some
        systems.

        Translating 127.0.0.1 into "localhost.dom.ain" can cause some
        software to connect back to the loopback interface when it didn't
        want to because "localhost" is not equal to "localhost.dom.ain".

     The "255" and "0" files should not contain any additional data beyond
     the NS and SOA records.

     Note that future BIND versions may include all or some of this data
     automatically without additional configuration.

Capturas
--------

Consulta común, de tipo *A*::

  Frame 19: 79 bytes on wire (632 bits), 79 bytes captured (632 bits) on interface 0
  Ethernet II, Src: f2:91:c2:b9:f1:de (f2:91:c2:b9:f1:de),
      Dst: a6:98:12:70:82:73 (a6:98:12:70:82:73)
  Internet Protocol Version 4, Src: 10.0.0.101, Dst: 10.0.0.11
  User Datagram Protocol, Src Port: 34228, Dst Port: 53
  Domain Name System (query)
    Transaction ID: 0xa59b
    Flags: 0x0100 Standard query
    Questions: 1
    Answer RRs: 0
    Authority RRs: 0
    Additional RRs: 0
    Queries
      host1.bernardi.test: type A, class IN
    [Response In: 20]

Respuesta fallida de servidor secundario a esa consulta, debido a que no estaba
disponible el servidor primario para hacer transferencia de zona::

  Frame 18: 79 bytes on wire (632 bits), 79 bytes captured (632 bits) on interface 0
  Ethernet II, Src: a6:98:12:70:82:73 (a6:98:12:70:82:73),
      Dst: f2:91:c2:b9:f1:de (f2:91:c2:b9:f1:de)
  Internet Protocol Version 4, Src: 10.0.0.11, Dst: 10.0.0.101
  User Datagram Protocol, Src Port: 53, Dst Port: 34987
  Domain Name System (response)
    Transaction ID: 0xa59b
    Flags: 0x8182 Standard query response, Server failure
    Questions: 1
    Answer RRs: 0
    Authority RRs: 0
    Additional RRs: 0
    Queries
      host1.bernardi.test: type A, class IN
    [Request In: 15]
    [Time: 0.000965000 seconds]

Respuesta correcta de consulta similar (sólo difiere en el ID de transacción)::

  Frame 44: 163 bytes on wire (1304 bits), 163 bytes captured (1304 bits) on interface 0
  Ethernet II, Src: a6:98:12:70:82:73 (a6:98:12:70:82:73),
      Dst: f2:91:c2:b9:f1:de (f2:91:c2:b9:f1:de)
  Internet Protocol Version 4, Src: 10.0.0.11, Dst: 10.0.0.101
  User Datagram Protocol, Src Port: 53, Dst Port: 55484
  Domain Name System (response)
    Transaction ID: 0xaa1c
    Flags: 0x8580 Standard query response, No error
    Questions: 1
    Answer RRs: 1
    Authority RRs: 2
    Additional RRs: 2
    Queries
      host1.bernardi.test: type A, class IN
    Answers
      host1.bernardi.test: type A, class IN, addr 10.0.0.100
    Authoritative nameservers
      bernardi.test: type NS, class IN, ns ns1.bernardi.test
      bernardi.test: type NS, class IN, ns ns2.bernardi.test
    Additional records
      ns1.bernardi.test: type A, class IN, addr 10.0.0.10
      ns2.bernardi.test: type A, class IN, addr 10.0.0.11
    [Request In: 43]
    [Time: 0.000530000 seconds]

Transferencia de zona
~~~~~~~~~~~~~~~~~~~~~

Resumen de transferencia de zona de ``ns1.bernardi.test`` (primario) a
``ns2.bernardi.test`` (secundario)::

  10.0.0.11  ->  10.0.0.10  TCP
    33169 → 53 [SYN] Seq=0 Len=0 MSS=1460 SACK_PERM=1

  10.0.0.10  ->  10.0.0.11  TCP
    53 → 33169 [SYN, ACK] Seq=0 Ack=1 Len=0 MSS=1460 SACK_PERM=1

  10.0.0.11  ->  10.0.0.10  TCP
    33169 → 53 [ACK] Seq=1 Ack=1 WLen=0

  10.0.0.11  ->  10.0.0.10  DNS
    Standard query 0xb81d AXFR bernardi.test

  10.0.0.10  ->  10.0.0.11  TCP
    53 → 33169 [ACK] Seq=1 Ack=34 Len=0

  10.0.0.10  ->  10.0.0.11  DNS
    Standard query response 0xb81d
    AXFR bernardi.test SOA ns1.bernardi.test NS ns1.bernardi.test NS ns2.bernardi.test
    A 10.0.0.100 A 10.0.0.101 A 10.0.0.10 A 10.0.0.11 SOA ns1.bernardi.test

  10.0.0.11  ->  10.0.0.10  TCP
    r3169 → 53 [ACK] Seq=34 Ack=224 Len=0

  10.0.0.11  ->  10.0.0.10  TCP
    33169 → 53 [FIN, ACK] Seq=34 Ack=224 Len=0

  10.0.0.10  ->  10.0.0.11  TCP
    53 → 33169 [FIN, ACK] Seq=224 Ack=35 Len=0

  10.0.0.11  ->  10.0.0.10  TCP
    33169 → 53 [ACK] Seq=35 Ack=225 Len=0

Petición de transferencia::

  Frame 32: 99 bytes on wire (792 bits), 99 bytes captured (792 bits) on interface 0
  Ethernet II, Src: a6:98:12:70:82:73 (a6:98:12:70:82:73),
      Dst: e2:87:2e:a9:0a:f7 (e2:87:2e:a9:0a:f7)
  Internet Protocol Version 4, Src: 10.0.0.11, Dst: 10.0.0.10
  Transmission Control Protocol, Src Port: 33169, Dst Port: 53, Seq: 1, Ack: 1, Len: 33
  Domain Name System (query)
    Length: 31
    Transaction ID: 0xb81d
    Flags: 0x0000 Standard query
    Questions: 1
    Answer RRs: 0
    Authority RRs: 0
    Additional RRs: 0
    Queries
      bernardi.test: type AXFR, class IN
    [Response In: 34]

Transferencia::

  Frame 34: 289 bytes on wire (2312 bits), 289 bytes captured (2312 bits) on interface 0
  Ethernet II, Src: e2:87:2e:a9:0a:f7 (e2:87:2e:a9:0a:f7),
      Dst: a6:98:12:70:82:73 (a6:98:12:70:82:73)
  Internet Protocol Version 4, Src: 10.0.0.10, Dst: 10.0.0.11
  Transmission Control Protocol, Src Port: 53, Dst Port: 33169, Seq: 1, Ack: 34, Len: 223
  Domain Name System (response)
    Length: 221
    Transaction ID: 0xb81d
    Flags: 0x8480 Standard query response, No error
    Questions: 1
    Answer RRs: 8
    Authority RRs: 0
    Additional RRs: 0
    Queries
      bernardi.test: type AXFR, class IN
    Answers
      bernardi.test: type SOA, class IN, mname ns1.bernardi.test
      bernardi.test: type NS, class IN, ns ns1.bernardi.test
      bernardi.test: type NS, class IN, ns ns2.bernardi.test
      host1.bernardi.test: type A, class IN, addr 10.0.0.100
      host2.bernardi.test: type A, class IN, addr 10.0.0.101
      ns1.bernardi.test: type A, class IN, addr 10.0.0.10
      ns2.bernardi.test: type A, class IN, addr 10.0.0.11
      bernardi.test: type SOA, class IN, mname ns1.bernardi.test
    [Request In: 32]
    [Time: 0.000762000 seconds]
