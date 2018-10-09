DHCP
====

Dynamic Host Configuration Protocol.

- Viene por UDP

  - Puerto 68 en cliente

  - Puerto 67 en servidor

- Es la evolución del protocolo BOOTP.

Header
------

- Message type (OP) (1 byte): Indica petición (1) o respuesta (2).

- Hardware type (HTYPE) (1 byte): Protocolo de enlace, si es Ethernet es 1.

- Hardware address length (HLEN) (1 byte): Longitud de la dirección de enlace en
  bytes, si es Ethernet es 6.

- Hops (HOPS) (1 byte): Empieza en cero, cada gateway incrementa el salto, si
  llega a 3 se supone que hubo un bucle.

- Transaction ID (XID) (4 bytes): Número aleatorio que identifica a esta
  transacción (DHCPDISCOVER, DHCPOFFER, DHCPREQUEST y DHCPACK).

- Seconds elapsed (SECS) (2 bytes): Fijado por el cliente, segundos desde que el
  cliente arrancó.

- BOOTP flags (FLAGS) (2 bytes): Solamente se usa el primer bit para indicar si
  el cliente quiere que el servidor responda como broadcast (1) o unicast (0).
  Se usa siempre unicast salvo en clientes que no puedan recibir unicast
  antes de inicializar su stack TCP/IP.

- Client IP (CIADDR) (4 bytes): Generalmente es ``0.0.0.0`` salvo por ejemplo
  cuando se hace una renovación.

- Your IP (YIADDR) (4 bytes): Fijado por el servidor si CIADDR era ``0.0.0.0``.

- Server IP (SIADDR) (4 bytes): IP del servidor, fijada por el servidor.

- Relay IP (GIADDR) (4 bytes): Fijado por el relay si es que se está usando uno.

- Client MAC (CHADDR) (16 bytes): Fijada por el cliente, como sobra espacio
  tiene padding de ceros.

- Server hostname (SNAME) (64 bytes): Es opcional, pero si no se usa queda el
  espacio vacío con ceros.

- Boot filename (FILE) (128 bytes): En DHCPDISCOVER no tiene nada, en DHCPOFFER
  el servidor fija un nombre de archivo.

- Opciones (OPTIONS) (variable): Ver abajo

Opciones
~~~~~~~~

Hay muchísimas, pongo las mas usadas:

- Generales, se usan siempre:

  - DHCP Message type (53): Indica el tipo de mensaje, por ejemplo:

    - DHCPDISCOVER: 1

    - DHCPOFFER: 2

    - DHCPREQUEST: 3

    - DHCPACK: 5

  - Endmark (255): Indica fin de opciones.

- Usadas en DHCPDISCOVER y DHCPREQUEST.

  - Parameter request list (55): Lista de parámetros que requiere el cliente.
    Son varios números que se corresponden a las opciones que explico abajo.

- Usadas en DHCPOFFER y DHCPACK.

  - Subnet mask (1): Máscara de red a usar por el cliente

  - Router (3): Gateway a usar por el cliente.

  - IP address lease time (51): Tiempo de lease para la IP dada.

  - DNS servers (8): Servidores DNS para el cliente.

  - DHCP server identifier (54): IP del servidor DHCP.

- Usadas en DHCPREQUEST:

  - Requested IP address (50): IP que el cliente está solicitando.

Funcionamiento
--------------

Los datagramas más importantes son DHCPDISCOVER, DHCPOFFER, DHCPREQUEST y
DHCPACK:

- Descubrimiento (DHCPDISCOVER): Cliente pregunta que DHCPs hay.

  - Origen:

    - MAC: MAC Cliente.

    - IP: ``0.0.0.0``.

    - Puerto: 68.

  - Destino:

    - MAC: ``FF:FF:FF:FF:FF:FF``.

    - IP: ``255.255.255.255``.

    - Puerto: 67

  - DHCP:

    - YIADDR: ``0.0.0.0``.

    - DHCP message type: 1 (Discover).

    - Lista de parámetros requeridos.

- Oferta (DHCPOFFER): Servidores DHCP reservan una IP y la ofrecen, indicando
  además el tiempo de lease (arrendamiento).

  - Origen:

    - MAC: MAC Servidor.

    - IP: IP Servidor.

    - Puerto: 67.

  - Destino:

    - MAC: MAC Cliente

    - IP: IP propuesta para Cliente.

    - Puerto: 68

  - DHCP:

    - YIADDR: IP propuesta para cliente.

    - DHCP message type: 2 (Offer).

    - Tiempo de *lease*, máscara de red, gateway, DNS.

- Solicitud (DHCPREQUEST): Cliente pide a un servidor DHCP esa IP, es broadcast
  para avisarle a los potenciales otros servidores que no los elegi.

  - Origen:

    - MAC: MAC Cliente.

    - IP: ``0.0.0.0``.

    - Puerto: 68.

  - Destino:

    - MAC: ``FF:FF:FF:FF:FF:FF``.

    - IP: ``255.255.255.255``.

    - Puerto: 67

  - DHCP:

    - YIADDR: ``0.0.0.0``.

    - DHCP message type: 3 (Request).

    - Lista de parámetros requeridos.

    - IP requerida por el cliente.

- ACK (DHCPACK): Servidor confirma.

  - Origen:

    - MAC: MAC Servidor.

    - IP: IP Servidor.

    - Puerto: 67.

  - Destino:

    - MAC: MAC Cliente

    - IP: IP Cliente.

    - Puerto: 68

  - DHCP:

    - YIADDR: IP propuesta para cliente.

    - DHCP message type: 5 (ACK).

    - Tiempo de *lease*, máscara de red, gateway, DNS.

Estados
-------

.. image:: ./estados_dhcp.png
   :width: 60%

- Initialization

- Selection: Espera todas las ofertas de los servidores

- Request: Pide a un servidor y espera respuesta.

- Bound: Una vez que le llegó la confirmación del servidor. En este momento la
  PC trabaja normalmente.

- Renew: Envía un DHCPREQUEST para renovar (al 50% del tiempo) y espera
  respuesta.

- Rebind: Envía un DHCPREQUEST para renovar (al finalizar el tiempo) y espera
  respuesta.

DHCP relaying
-------------

Cuando hay varias subredes, puede haber un servidor DHCP por subred o puede
haber un servidor que sirva a varias redes. El problema es que en este caso el
servidor y el cliente están en redes distintas, el cliente no tiene una IP
ruteable y tampoco sabe la IP del servidor DHCP.

Para permitir a los clientes comunicarse con los servidores, se instalan *DHCP
relay agents*. Estos agentes de retransmisión cuando reciben un broadcast DHCP
reenvían el mensaje a uno o varios servidores DHCP presentes en otras redes
mediante unicast.

Hay un campo específico en la trama llamado ``GIADDR``, en donde el gateway
coloca su IP, de esta forma el servidor DHCP sabe que debe responder a esa IP en
particular y que debe reservarle una IP al cliente en esa red.

Para la comunicación entre relay y servidor generalmente se usa el puerto 67
para origen y destino.

Capturas
--------

Discover, UDP 68 -> 67::

  Frame 55: 342 bytes on wire (2736 bits), 342 bytes captured (2736 bits) on
      interface 0
  Ethernet II, Src: HonHaiPr_13:7f:55 (08:3e:8e:13:7f:55), Dst: Broadcast
      (ff:ff:ff:ff:ff:ff)
  Internet Protocol Version 4, Src: 0.0.0.0, Dst: 255.255.255.255
    0100 .... = Version: 4
    .... 0101 = Header Length: 20 bytes (5)
    Differentiated Services Field: 0x10 (DSCP: Unknown, ECN: Not-ECT)
    Total Length: 328
    Identification: 0x0000 (0)
    Flags: 0x00
    Fragment offset: 0
    Time to live: 128
    Protocol: UDP (17)
    Header checksum: 0x3996 [validation disabled]
    [Header checksum status: Unverified]
    Source: 0.0.0.0
    Destination: 255.255.255.255
    [Source GeoIP: Unknown]
    [Destination GeoIP: Unknown]
  User Datagram Protocol, Src Port: 68, Dst Port: 67
    Source Port: 68
    Destination Port: 67
    Length: 308
    Checksum: 0xbf55 [unverified]
    [Checksum Status: Unverified]
    [Stream index: 0]
  Bootstrap Protocol (Discover)
    Message type: Boot Request (1)
    Hardware type: Ethernet (0x01)
    Hardware address length: 6
    Hops: 0
    Transaction ID: 0xa1368e3d
    Seconds elapsed: 0
    Bootp flags: 0x0000 (Unicast)
      0... .... .... .... = Broadcast flag: Unicast
      .000 0000 0000 0000 = Reserved flags: 0x0000
    Client IP address: 0.0.0.0
    Your (client) IP address: 0.0.0.0
    Next server IP address: 0.0.0.0
    Relay agent IP address: 0.0.0.0
    Client MAC address: HonHaiPr_13:7f:55 (08:3e:8e:13:7f:55)
    Client hardware address padding: 00000000000000000000
    Server host name not given
    Boot file name not given
    Magic cookie: DHCP
    Option: (53) DHCP Message Type (Discover)
      Length: 1
      DHCP: Discover (1)
    Option: (12) Host Name
      Length: 16
      Host Name: mbernardi-laptop
    Option: (55) Parameter Request List
      Length: 13
      Parameter Request List Item: (1) Subnet Mask
      Parameter Request List Item: (28) Broadcast Address
      Parameter Request List Item: (2) Time Offset
      Parameter Request List Item: (3) Router
      Parameter Request List Item: (15) Domain Name
      Parameter Request List Item: (6) Domain Name Server
      Parameter Request List Item: (119) Domain Search
      Parameter Request List Item: (12) Host Name
      Parameter Request List Item: (44) NetBIOS over TCP/IP Name Server
      Parameter Request List Item: (47) NetBIOS over TCP/IP Scope
      Parameter Request List Item: (26) Interface MTU
      Parameter Request List Item: (121) Classless Static Route
      Parameter Request List Item: (42) Network Time Protocol Servers
    Option: (255) End
      Option End: 255
    Padding: 0000000000000000000000000000000000000000000000

Offer, UDP 67 -> 68::

  Frame 56: 590 bytes on wire (4720 bits), 590 bytes captured (4720 bits) on
      interface 0
  Ethernet II, Src: Tp-LinkT_c7:77:b4 (70:4f:57:c7:77:b4), Dst:
      HonHaiPr_13:7f:55 (08:3e:8e:13:7f:55)
  Internet Protocol Version 4, Src: 192.168.2.1, Dst: 192.168.2.101
    0100 .... = Version: 4
    .... 0101 = Header Length: 20 bytes (5)
    Differentiated Services Field: 0x00 (DSCP: CS0, ECN: Not-ECT)
    Total Length: 576
    Identification: 0xb762 (46946)
    Flags: 0x00
    Fragment offset: 0
    Time to live: 64
    Protocol: UDP (17)
    Header checksum: 0x3b94 [validation disabled]
    [Header checksum status: Unverified]
    Source: 192.168.2.1
    Destination: 192.168.2.101
    [Source GeoIP: Unknown]
    [Destination GeoIP: Unknown]
  User Datagram Protocol, Src Port: 67, Dst Port: 68
    Source Port: 67
    Destination Port: 68
    Length: 556
    Checksum: 0xcb8a [unverified]
    [Checksum Status: Unverified]
    [Stream index: 1]
  Bootstrap Protocol (Offer)
    Message type: Boot Reply (2)
    Hardware type: Ethernet (0x01)
    Hardware address length: 6
    Hops: 0
    Transaction ID: 0xa1368e3d
    Seconds elapsed: 0
    Bootp flags: 0x0000 (Unicast)
      0... .... .... .... = Broadcast flag: Unicast
      .000 0000 0000 0000 = Reserved flags: 0x0000
    Client IP address: 0.0.0.0
    Your (client) IP address: 192.168.2.101
    Next server IP address: 0.0.0.0
    Relay agent IP address: 0.0.0.0
    Client MAC address: HonHaiPr_13:7f:55 (08:3e:8e:13:7f:55)
    Client hardware address padding: 00000000000000000000
    Server host name not given
    Boot file name not given
    Magic cookie: DHCP
    Option: (53) DHCP Message Type (Offer)
      Length: 1
      DHCP: Offer (2)
    Option: (54) DHCP Server Identifier
      Length: 4
      DHCP Server Identifier: 192.168.2.1
    Option: (51) IP Address Lease Time
      Length: 4
      IP Address Lease Time: (7200s) 2 hours
    Option: (1) Subnet Mask
      Length: 4
      Subnet Mask: 255.255.255.0
    Option: (3) Router
      Length: 4
      Router: 192.168.2.1
    Option: (6) Domain Name Server
      Length: 8
      Domain Name Server: 200.7.141.6
      Domain Name Server: 200.7.141.7
    Option: (255) End
      Option End: 255
    Padding: 000000000000000000000000000000000000000000000000...

Request, 68 -> 67::

  Frame 57: 342 bytes on wire (2736 bits), 342 bytes captured (2736 bits) on
      interface 0
  Ethernet II, Src: HonHaiPr_13:7f:55 (08:3e:8e:13:7f:55), Dst: Broadcast
      (ff:ff:ff:ff:ff:ff)
  Internet Protocol Version 4, Src: 0.0.0.0, Dst: 255.255.255.255
    0100 .... = Version: 4
    .... 0101 = Header Length: 20 bytes (5)
    Differentiated Services Field: 0x10 (DSCP: Unknown, ECN: Not-ECT)
    Total Length: 328
    Identification: 0x0000 (0)
    Flags: 0x00
    Fragment offset: 0
    Time to live: 128
    Protocol: UDP (17)
    Header checksum: 0x3996 [validation disabled]
    [Header checksum status: Unverified]
    Source: 0.0.0.0
    Destination: 255.255.255.255
    [Source GeoIP: Unknown]
    [Destination GeoIP: Unknown]
  User Datagram Protocol, Src Port: 68, Dst Port: 67
    Source Port: 68
    Destination Port: 67
    Length: 308
    Checksum: 0xfd67 [unverified]
    [Checksum Status: Unverified]
    [Stream index: 0]
  Bootstrap Protocol (Request)
    Message type: Boot Request (1)
    Hardware type: Ethernet (0x01)
    Hardware address length: 6
    Hops: 0
    Transaction ID: 0xa1368e3d
    Seconds elapsed: 0
    Bootp flags: 0x0000 (Unicast)
      0... .... .... .... = Broadcast flag: Unicast
      .000 0000 0000 0000 = Reserved flags: 0x0000
    Client IP address: 0.0.0.0
    Your (client) IP address: 0.0.0.0
    Next server IP address: 0.0.0.0
    Relay agent IP address: 0.0.0.0
    Client MAC address: HonHaiPr_13:7f:55 (08:3e:8e:13:7f:55)
    Client hardware address padding: 00000000000000000000
    Server host name not given
    Boot file name not given
    Magic cookie: DHCP
    Option: (53) DHCP Message Type (Request)
      Length: 1
      DHCP: Request (3)
    Option: (54) DHCP Server Identifier
      Length: 4
      DHCP Server Identifier: 192.168.2.1
    Option: (50) Requested IP Address
      Length: 4
      Requested IP Address: 192.168.2.101
    Option: (12) Host Name
      Length: 16
      Host Name: mbernardi-laptop
    Option: (55) Parameter Request List
      Length: 13
      Parameter Request List Item: (1) Subnet Mask
      Parameter Request List Item: (28) Broadcast Address
      Parameter Request List Item: (2) Time Offset
      Parameter Request List Item: (3) Router
      Parameter Request List Item: (15) Domain Name
      Parameter Request List Item: (6) Domain Name Server
      Parameter Request List Item: (119) Domain Search
      Parameter Request List Item: (12) Host Name
      Parameter Request List Item: (44) NetBIOS over TCP/IP Name Server
      Parameter Request List Item: (47) NetBIOS over TCP/IP Scope
      Parameter Request List Item: (26) Interface MTU
      Parameter Request List Item: (121) Classless Static Route
      Parameter Request List Item: (42) Network Time Protocol Servers
    Option: (255) End
      Option End: 255
    Padding: 0000000000000000000000

ACK, UDP 67 -> 68::

  Frame 58: 590 bytes on wire (4720 bits), 590 bytes captured (4720 bits) on
      interface 0
  Ethernet II, Src: Tp-LinkT_c7:77:b4 (70:4f:57:c7:77:b4), Dst:
      HonHaiPr_13:7f:55 (08:3e:8e:13:7f:55)
  Internet Protocol Version 4, Src: 192.168.2.1, Dst: 192.168.2.101
    0100 .... = Version: 4
    .... 0101 = Header Length: 20 bytes (5)
    Differentiated Services Field: 0x00 (DSCP: CS0, ECN: Not-ECT)
    Total Length: 576
    Identification: 0xb762 (46946)
    Flags: 0x00
    Fragment offset: 0
    Time to live: 64
    Protocol: UDP (17)
    Header checksum: 0x3b94 [validation disabled]
    [Header checksum status: Unverified]
    Source: 192.168.2.1
    Destination: 192.168.2.101
    [Source GeoIP: Unknown]
    [Destination GeoIP: Unknown]
  User Datagram Protocol, Src Port: 67, Dst Port: 68
    Source Port: 67
    Destination Port: 68
    Length: 556
    Checksum: 0xc88a [unverified]
    [Checksum Status: Unverified]
    [Stream index: 1]
  Bootstrap Protocol (ACK)
    Message type: Boot Reply (2)
    Hardware type: Ethernet (0x01)
    Hardware address length: 6
    Hops: 0
    Transaction ID: 0xa1368e3d
    Seconds elapsed: 0
    Bootp flags: 0x0000 (Unicast)
      0... .... .... .... = Broadcast flag: Unicast
      .000 0000 0000 0000 = Reserved flags: 0x0000
    Client IP address: 0.0.0.0
    Your (client) IP address: 192.168.2.101
    Next server IP address: 0.0.0.0
    Relay agent IP address: 0.0.0.0
    Client MAC address: HonHaiPr_13:7f:55 (08:3e:8e:13:7f:55)
    Client hardware address padding: 00000000000000000000
    Server host name not given
    Boot file name not given
    Magic cookie: DHCP
    Option: (53) DHCP Message Type (ACK)
      Length: 1
      DHCP: ACK (5)
    Option: (54) DHCP Server Identifier
      Length: 4
      DHCP Server Identifier: 192.168.2.1
    Option: (51) IP Address Lease Time
      Length: 4
      IP Address Lease Time: (7200s) 2 hours
    Option: (1) Subnet Mask
      Length: 4
      Subnet Mask: 255.255.255.0
    Option: (3) Router
      Length: 4
      Router: 192.168.2.1
    Option: (6) Domain Name Server
      Length: 8
      Domain Name Server: 200.7.141.6
      Domain Name Server: 200.7.141.7
    Option: (255) End
      Option End: 255
    Padding: 000000000000000000000000000000000000000000000000...
