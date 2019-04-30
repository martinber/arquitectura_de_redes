IPv6
====

Internet Protocol version 6.

- Tiene direcciones de 128 bits.

- Hay un encabezado obligatorio de tamaño fijo y luego encabezados opcionales.

Header
------

- Version (4 bits): Siempre es 6.

- Traffic Class (8 bits): Incluye los campos DS y ECN.

  - Differentiated Services (DS) (6 bits): Tipo de servicio, para QoS pero no
    se usa.

  - Explicit Congestion Notification (ECN) (2 bits): Se usa poco, indica que hay
    congestión en la red.

- Flow Label (20 bits): Para QoS.

- Payload Length (16 bits): Largo de los datos, incluyendo los encabezados de
  extensión.

- Next Header (8 bits): Tipo del próximo header, es el número de protocolo
  encapsulado, en el caso que se usen encabezados de extensión lleva el código
  que lo identifica.

- Hop Limit (8 bits): Este valor disminuye salto a salto y el paquete es
  descartado si llega a cero.

- Source address (128 bits): IP de origen.

- Destination address (128 bits): IP de destino.

Tipos de direcciones
--------------------

- Según destino:

  - Unicast: Se entrega a una única interfaz.

  - Multicast: Se entrega a múltiples interfaces.

  - Anycast: Se entrega a cualquiera de las interfaces del conjunto.

  - No existe broadcast.

- Según rango:

  - Link-local: ``fe80::/16``.

  - Globalmente ruteable: ``2000::/3``.

  - Global: ``2001::/16``.

  - Documentación: ``2001:db8::/32``.

  - Teredo: ``2001:0::/32``.

  - 6to4: ``2002::/16``.

  - Mapeada IPv4: ``::ffff:xxxx/112``.

  - Compatible IPv4: ``::xxxx/112`` (Obsoleto).

- Direcciones especiales:

  - Loopback: ``::1/128``.

  - No especificada: ``::/128``.

SLAAC
-----

.. todo:: Ver que multucast usa

Stateless Address Autoconfiguration.

- Permite a los hosts obtener direcciones IPv6 ruteables por medio de Router
  Advertisement y Router Solicitation (pertenecientes a ICMPv6).

- Si es necesario, se puede usar DHCPv6 en lugar de SLAAC.

- Primero el host obtiene su dirección link-local y envía un Router
  Solicitation. Luego el router responde con un Router Advertisement, indicando
  el prefijo de red que usará el host.

.. todo:: Agregar sobre como obtener parte de host: 64 bits EUI-64, a partir de
   MAC, aleatorio, dhcp o manual

Migración desde IPv4
--------------------

El problema más común es que el núcleo de internet es IPv4, entonces hay que
buscar la forma de enviar tráfico IPv6 a través de una red IPv4. Las soluciones
que hay son:

- Túneles manuales: Se deben configurar manualmente en ambos extremos.

- Túneles automáticos: Por ejemplo :doc:`6to4`.

- Traslación: Es una extensión de NAT para cambiar headers y direcciones.

Fragmentación
-------------

Se usa un header opcional para la fragmentación.

A diferencia de IPv4, sólo se puede hacer en los extremos. Los hosts intentan
enviar los mensajes con un determinado tamaño, si se devuelve un *ICMP packet
too big* se vuelve a intentar con el MTU dado en ese mensaje.

Enviando ping con un tamaño de 4000 usando ``ping6 fe80::2273:55ff:fe06:2451 -I
wlan0 -s 4000``::

  fe80::39af:... fe80::2273:... IPv6     IPv6 fragment (off=0 more=y ident=0x79a0649e nxt=58)
  fe80::39af:... fe80::2273:... IPv6     IPv6 fragment (off=1448 more=y ident=0x79a0649e nxt=58)
  fe80::39af:... fe80::2273:... ICMPv6   Echo (ping) request id=0x3eeb, seq=4, hop limit=64 (reply in 50)

Se partió en 3 fragmentos, header de cada fragmento::

  Internet Protocol Version 6, Src: fe80::39af:14fa:830b:c073, Dst: fe80::2273:55ff:fe06:2451
      0110 .... = Version: 6
      .... 0000 0000 .... .... .... .... .... = Traffic class: 0x00 (DSCP: CS0, ECN: Not-ECT)
      .... .... .... 1000 0010 0011 1011 0111 = Flow label: 0x823b7
      Payload length: 1456
      Next header: Fragment Header for IPv6 (44)
      Hop limit: 64
      Source: fe80::39af:14fa:830b:c073
      Destination: fe80::2273:55ff:fe06:2451
      [Destination SA MAC: ArrisGro_06:24:51 (20:73:55:06:24:51)]
      [Source GeoIP: Unknown]
      [Destination GeoIP: Unknown]
      Fragment Header for IPv6
          Next header: ICMPv6 (58)
          Reserved octet: 0x00
          0000 0000 0000 0... = Offset: 0 (0 bytes)
          .... .... .... .00. = Reserved bits: 0
          .... .... .... ...1 = More Fragments: Yes
          Identification: 0x79a0649e
      Reassembled IPv6 in frame: 47

  Frame 46: 1510 bytes on wire (12080 bits), 1510 bytes captured (12080 bits) on interface 0
  Ethernet II, Src: HonHaiPr_13:7f:55 (08:3e:8e:13:7f:55), Dst: ArrisGro_06:24:51 (20:73:55:06:24:51)
  Internet Protocol Version 6, Src: fe80::39af:14fa:830b:c073, Dst: fe80::2273:55ff:fe06:2451
      0110 .... = Version: 6
      .... 0000 0000 .... .... .... .... .... = Traffic class: 0x00 (DSCP: CS0, ECN: Not-ECT)
      .... .... .... 1000 0010 0011 1011 0111 = Flow label: 0x823b7
      Payload length: 1456
      Next header: Fragment Header for IPv6 (44)
      Hop limit: 64
      Source: fe80::39af:14fa:830b:c073
      Destination: fe80::2273:55ff:fe06:2451
      [Destination SA MAC: ArrisGro_06:24:51 (20:73:55:06:24:51)]
      [Source GeoIP: Unknown]
      [Destination GeoIP: Unknown]
      Fragment Header for IPv6
          Next header: ICMPv6 (58)
          Reserved octet: 0x00
          0000 0101 1010 1... = Offset: 181 (1448 bytes)
          .... .... .... .00. = Reserved bits: 0
          .... .... .... ...1 = More Fragments: Yes
          Identification: 0x79a0649e
      Reassembled IPv6 in frame: 47

  Frame 47: 1174 bytes on wire (9392 bits), 1174 bytes captured (9392 bits) on interface 0
  Ethernet II, Src: HonHaiPr_13:7f:55 (08:3e:8e:13:7f:55), Dst: ArrisGro_06:24:51 (20:73:55:06:24:51)
  Internet Protocol Version 6, Src: fe80::39af:14fa:830b:c073, Dst: fe80::2273:55ff:fe06:2451
      0110 .... = Version: 6
      .... 0000 0000 .... .... .... .... .... = Traffic class: 0x00 (DSCP: CS0, ECN: Not-ECT)
      .... .... .... 1000 0010 0011 1011 0111 = Flow label: 0x823b7
      Payload length: 1120
      Next header: Fragment Header for IPv6 (44)
      Hop limit: 64
      Source: fe80::39af:14fa:830b:c073
      Destination: fe80::2273:55ff:fe06:2451
      [Destination SA MAC: ArrisGro_06:24:51 (20:73:55:06:24:51)]
      [Source GeoIP: Unknown]
      [Destination GeoIP: Unknown]
      Fragment Header for IPv6
          Next header: ICMPv6 (58)
          Reserved octet: 0x00
          0000 1011 0101 0... = Offset: 362 (2896 bytes)
          .... .... .... .00. = Reserved bits: 0
          .... .... .... ...0 = More Fragments: No
          Identification: 0x79a0649e
