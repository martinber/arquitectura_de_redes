ICMPv6
======

Internet Control Message Protocol

- Va sobre IP.

- Se usa para diagnóstico y control de IP.

Header
------

- Type (1 byte): Tipo de mensaje.

- Code (1 byte): Es como un subtipo.

- Checksum (2 bytes): Checksum de todo el header pero también de un
  pseudo-header IPv6 para hacer checksum además a las direcciones IP.

- Resto del header (4 bytes): Los contenidos dependen del tipo de mensaje.

- Datos: Son cualquier cosa.

Ping
----

Header
~~~~~~

- Type: 128 en request, 129 en reply.

- Code: 0 en ambos.

- Resto del header:

  - Identifier (2 bytes): Identifica el proceso que envía los pings.

  - Sequence number (2 bytes): Número de ping enviado.

- Datos: Puede ser cualquier cosa, suele llevar timestamp.

NDP
---

Neighbor Discovery Protocol.

- Permite encontrar vecinos y gateways, también cumple funciones similares a
  ARP.

- Define cinco mensajes ICMPv6:

  - Router Solicitation (133): Mensajes enviados por hosts para solicitar
    mensajes de Router Advertisement y así encontrar a Routers/Gateways. Se
    envía a la multicast ``FF02::02`` que representa a todos los routers en
    link-local.

  - Router Advertisement (134): Mensajes enviados periódicamente por routers
    para anunciar sus servicios y varios parámetros, permite aplicar SLAAC. Se
    envía a la multicast ``FF02::01`` que representa a todos los nodos en
    link-local.

  - Neighbor Solicitation (135): Permite a los dispositivos determinar la
    dirección MAC de un vecino. Se envía a la multicast Solicited-Node del
    destino.

  - Neighbor Advertisement (136): Son las respuestas a Neighbor Solicitation. Se
    envía a la IP unicast del que solicitó, o hacia la multicast ``FF02::01``
    que representa a todos los nodos en Link-Local.

Otros usos
----------

Hay muchos más, pongo los más comunes junto a sus valores (Type, Code):

- Router Solicitation (133, 0).

- Router Advertisement (134, 0).

- Neighbor Solicitation (135, 0).

- Neighbor Advertisement (136, 0).

- Cuando se termina el TTL se envía un "Time Exceeded" (3, 0).

- No hay ruta a destino (1, 0).

- Dirección inalcanzable (3, 3).

- Puerto de destino inalcanzable (3, 4).

Capturas
--------

Echo Request::

  Frame 42: 118 bytes on wire (944 bits), 118 bytes captured (944 bits) on interface 0
  Ethernet II, Src: QuantaCo_43:f2:eb (08:9e:01:43:f2:eb), Dst: Cisco_bf:79:46 (00:15:62:bf:79:46)
  Internet Protocol Version 6, Src: 2001:beba:baba:10:a9e:1ff:fe43:f2eb, Dst: 2001:beba:baba:10::2
      0110 .... = Version: 6
      .... 0000 0000 .... .... .... .... .... = Traffic class: 0x00 (DSCP: CS0, ECN: Not-ECT)
      .... .... .... 0011 0011 0101 1010 0010 = Flow label: 0x335a2
      Payload length: 64
      Next header: ICMPv6 (58)
      Hop limit: 64
      Source: 2001:beba:baba:10:a9e:1ff:fe43:f2eb
      [Source SA MAC: QuantaCo_43:f2:eb (08:9e:01:43:f2:eb)]
      Destination: 2001:beba:baba:10::2
  Internet Control Message Protocol v6
      Type: Echo (ping) request (128)
      Code: 0
      Checksum: 0xeab2 [correct]
      Identifier: 0x615b
      Sequence: 2
      Data (56 bytes)

Echo Reply::

  Frame 43: 118 bytes on wire (944 bits), 118 bytes captured (944 bits) on interface 0
  Ethernet II, Src: Cisco_bf:79:46 (00:15:62:bf:79:46), Dst: QuantaCo_43:f2:eb (08:9e:01:43:f2:eb)
  Internet Protocol Version 6, Src: 2001:beba:baba:10::2, Dst: 2001:beba:baba:10:a9e:1ff:fe43:f2eb
      0110 .... = Version: 6
      .... 0000 0000 .... .... .... .... .... = Traffic class: 0x00 (DSCP: CS0, ECN: Not-ECT)
      .... .... .... 0011 0011 0101 1010 0010 = Flow label: 0x335a2
      Payload length: 64
      Next header: ICMPv6 (58)
      Hop limit: 64
      Source: 2001:beba:baba:10::2
      Destination: 2001:beba:baba:10:a9e:1ff:fe43:f2eb
      [Destination SA MAC: QuantaCo_43:f2:eb (08:9e:01:43:f2:eb)]
  Internet Control Message Protocol v6
      Type: Echo (ping) reply (129)
      Code: 0
      Checksum: 0xe9b2 [correct]
      Identifier: 0x615b
      Sequence: 2
      [Response Time: 0.574 ms]
      Data (56 bytes)

Neighbor Solicitation::

  Frame 37: 86 bytes on wire (688 bits), 86 bytes captured (688 bits) on interface 0
  Ethernet II, Src: QuantaCo_43:f2:eb (08:9e:01:43:f2:eb), Dst: IPv6mcast_ff:00:00:02 (33:33:ff:00:00:02)
  Internet Protocol Version 6, Src: 2001:beba:baba:10:a9e:1ff:fe43:f2eb, Dst: ff02::1:ff00:2
      0110 .... = Version: 6
      .... 0000 0000 .... .... .... .... .... = Traffic class: 0x00 (DSCP: CS0, ECN: Not-ECT)
      .... .... .... 0000 0000 0000 0000 0000 = Flow label: 0x00000
      Payload length: 32
      Next header: ICMPv6 (58)
      Hop limit: 255
      Source: 2001:beba:baba:10:a9e:1ff:fe43:f2eb
      [Source SA MAC: QuantaCo_43:f2:eb (08:9e:01:43:f2:eb)]
      Destination: ff02::1:ff00:2
      [Source GeoIP: Unknown]
      [Destination GeoIP: Unknown]
  Internet Control Message Protocol v6
      Type: Neighbor Solicitation (135)
      Code: 0
      Checksum: 0x4bf4 [correct]
      [Checksum Status: Good]
      Reserved: 00000000
      Target Address: 2001:beba:baba:10::2
      ICMPv6 Option (Source link-layer address : 08:9e:01:43:f2:eb)

Neighbor Advertisement::

  Frame 38: 86 bytes on wire (688 bits), 86 bytes captured (688 bits) on interface 0
  Ethernet II, Src: Cisco_bf:79:46 (00:15:62:bf:79:46), Dst: QuantaCo_43:f2:eb (08:9e:01:43:f2:eb)
  Internet Protocol Version 6, Src: 2001:beba:baba:10::2, Dst: 2001:beba:baba:10:a9e:1ff:fe43:f2eb
      0110 .... = Version: 6
      .... 1110 0000 .... .... .... .... .... = Traffic class: 0xe0 (DSCP: CS7, ECN: Not-ECT)
      .... .... .... 0000 0000 0000 0000 0000 = Flow label: 0x00000
      Payload length: 32
      Next header: ICMPv6 (58)
      Hop limit: 255
      Source: 2001:beba:baba:10::2
      Destination: 2001:beba:baba:10:a9e:1ff:fe43:f2eb
      [Destination SA MAC: QuantaCo_43:f2:eb (08:9e:01:43:f2:eb)]
      [Source GeoIP: Unknown]
      [Destination GeoIP: Unknown]
  Internet Control Message Protocol v6
      Type: Neighbor Advertisement (136)
      Code: 0
      Checksum: 0xef23 [correct]
      [Checksum Status: Good]
      Flags: 0xe0000000
      Target Address: 2001:beba:baba:10::2
      ICMPv6 Option (Target link-layer address : 00:15:62:bf:79:46)

Router Solicitation::

  Frame 8: 70 bytes on wire (560 bits), 70 bytes captured (560 bits) on interface 0
  Ethernet II, Src: ce:96:8b:90:e5:bb (ce:96:8b:90:e5:bb), Dst: IPv6mcast_02 (33:33:00:00:00:02)
  Internet Protocol Version 6, Src: fe80::cc96:8bff:fe90:e5bb, Dst: ff02::2
      0110 .... = Version: 6
      .... 0000 0000 .... .... .... .... .... = Traffic class: 0x00 (DSCP: CS0, ECN: Not-ECT)
      .... .... .... 0000 0000 0000 0000 0000 = Flow label: 0x00000
      Payload length: 16
      Next header: ICMPv6 (58)
      Hop limit: 255
      Source: fe80::cc96:8bff:fe90:e5bb
      Destination: ff02::2
      [Source GeoIP: Unknown]
      [Destination GeoIP: Unknown]
  Internet Control Message Protocol v6
      Type: Router Solicitation (133)
      Code: 0
      Checksum: 0xff67 [correct]
      [Checksum Status: Good]
      Reserved: 00000000
      ICMPv6 Option (Source link-layer address : ce:96:8b:90:e5:bb)

Router Advertisement::

  Frame 32: 118 bytes on wire (944 bits), 118 bytes captured (944 bits) on interface 0
  Ethernet II, Src: Cisco_bf:79:46 (00:15:62:bf:79:46), Dst: IPv6mcast_01 (33:33:00:00:00:01)
  Internet Protocol Version 6, Src: fe80::215:62ff:febf:7946, Dst: ff02::1
      0110 .... = Version: 6
      .... 1110 0000 .... .... .... .... .... = Traffic class: 0xe0 (DSCP: CS7, ECN: Not-ECT)
      .... .... .... 0000 0000 0000 0000 0000 = Flow label: 0x00000
      Payload length: 64
      Next header: ICMPv6 (58)
      Hop limit: 255
      Source: fe80::215:62ff:febf:7946
      [Source SA MAC: Cisco_bf:79:46 (00:15:62:bf:79:46)]
      Destination: ff02::1
      [Source GeoIP: Unknown]
      [Destination GeoIP: Unknown]
  Internet Control Message Protocol v6
      Type: Router Advertisement (134)
      Code: 0
      Checksum: 0xcae8 [correct]
      [Checksum Status: Good]
      Cur hop limit: 64
      Flags: 0x00
      Router lifetime (s): 1800
      Reachable time (ms): 0
      Retrans timer (ms): 0
      ICMPv6 Option (Source link-layer address : 00:15:62:bf:79:46)
      ICMPv6 Option (MTU : 1500)
      ICMPv6 Option (Prefix information : 2001:beba:baba:10::/64)
