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
    cuales son administrados localmente.

  - Secundario (Esclavo): Trabaja con transferencia de zona, que significa tomar
    las zonas de un primario y almacenarlas localmente.

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
