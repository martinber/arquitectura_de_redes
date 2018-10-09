ICMPv4
======

Internet Control Message Protocol

- Va sobre IP.

- Se usa para diagnóstico y control de IP.

Header
------

- Type (1 byte): Tipo de mensaje.

- Code (1 byte): Es como un subtipo.

- Checksum (2 bytes): Checksum de todo el header.

- Resto del header (4 bytes): Los contenidos dependen del tipo de mensaje.

- Datos: Son cualquier cosa.

Ping
----

- Type: 8 en request, 0 en reply.

- Code: 0.

- Resto del header:

  - Identifier (2 bytes): Identifica el proceso que envía los pings.

  - Sequence number (2 bytes): Número de ping enviado.

- Datos: Puede ser cualquier cosa, suele llevar timestamp.

Otros usos
----------

Hay muchos más, pongo los más comunes junto a sus valores (Type, Code):

- Cuando se termina el TTL se envía un "ICMP Time Exceeded" (11, 0).

- Red de destino inalcanzable (3, 0).

- Host de destino inalcanzable (3, 1).

- Protocolo de destino inalcanzable (3, 2).

- Puerto de destino inalcanzable (3, 3).

Redirección ICMP
----------------

.. todo:: Buscar sobre ICMP Redirect. Creo que un router informa a un host de
  una ruta más corta

Capturas
--------

Echo Request::

  Frame 11: 98 bytes on wire (784 bits), 98 bytes captured (784 bits) on
      interface 0
  Ethernet II, Src: HonHaiPr_13:7f:55 (08:3e:8e:13:7f:55), Dst:
      Tp-LinkT_22:9a:f2 (ec:08:6b:22:9a:f2)
  Internet Protocol Version 4, Src: 192.168.0.20, Dst: 8.8.8.8
    0100 .... = Version: 4
    .... 0101 = Header Length: 20 bytes (5)
    Differentiated Services Field: 0x00 (DSCP: CS0, ECN: Not-ECT)
    Total Length: 84
    Identification: 0xcdbe (52670)
    Flags: 0x02 (Don't Fragment)
    Fragment offset: 0
    Time to live: 64
    Protocol: ICMP (1)
    Header checksum: 0x9c1e [validation disabled]
    [Header checksum status: Unverified]
    Source: 192.168.0.20
    Destination: 8.8.8.8
    [Source GeoIP: Unknown]
    [Destination GeoIP: United States, AS15169 Google Inc., Mountain View, CA,
        37.386002, -122.083801]
  Internet Control Message Protocol
    Type: 8 (Echo (ping) request)
    Code: 0
    Checksum: 0x5b79 [correct]
    [Checksum Status: Good]
    Identifier (BE): 14306 (0x37e2)
    Identifier (LE): 57911 (0xe237)
    Sequence number (BE): 1 (0x0001)
    Sequence number (LE): 256 (0x0100)
    [Response frame: 12]
    Timestamp from icmp data: Jun 16, 2018 20:08:22.000000000 -03
    [Timestamp from icmp data (relative): 0.449866492 seconds]
    Data (48 bytes)
      Data: 13dd060000000000101112131415161718191a1b1c1d1e1f...
      [Length: 48]

Echo Reply::

  Frame 12: 98 bytes on wire (784 bits), 98 bytes captured (784 bits) on
      interface 0
  Ethernet II, Src: Tp-LinkT_22:9a:f2 (ec:08:6b:22:9a:f2), Dst:
      HonHaiPr_13:7f:55 (08:3e:8e:13:7f:55)
  Internet Protocol Version 4, Src: 8.8.8.8, Dst: 192.168.0.20
    0100 .... = Version: 4
    .... 0101 = Header Length: 20 bytes (5)
    Differentiated Services Field: 0x00 (DSCP: CS0, ECN: Not-ECT)
    Total Length: 84
    Identification: 0x2f2b (12075)
    Flags: 0x00
    Fragment offset: 0
    Time to live: 50
    Protocol: ICMP (1)
    Header checksum: 0x88b2 [validation disabled]
    [Header checksum status: Unverified]
    Source: 8.8.8.8
    Destination: 192.168.0.20
    [Source GeoIP: United States, AS15169 Google Inc., Mountain View, CA,
        37.386002, -122.083801]
    [Destination GeoIP: Unknown]
  Internet Control Message Protocol
    Type: 0 (Echo (ping) reply)
    Code: 0
    Checksum: 0x6379 [correct]
    [Checksum Status: Good]
    Identifier (BE): 14306 (0x37e2)
    Identifier (LE): 57911 (0xe237)
    Sequence number (BE): 1 (0x0001)
    Sequence number (LE): 256 (0x0100)
    [Request frame: 11]
    [Response time: 24.340 ms]
    Timestamp from icmp data: Jun 16, 2018 20:08:22.000000000 -03
    [Timestamp from icmp data (relative): 0.474206330 seconds]
    Data (48 bytes)
      Data: 13dd060000000000101112131415161718191a1b1c1d1e1f...
      [Length: 48]
