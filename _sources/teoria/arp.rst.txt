ARP
===

Address Resolution Protocol.

- Va sobre Ethernet.

- No debería ser de capa 3 porque no tiene ruteo, y no debería ser de capa 2
  porque no es Ethernet. Así que no está bien definido.

- Se usa para descubrir la MAC de algún host a partir de su IP.

- Los demás hosts aprovechan a escuchar ARP de otros para aprender sus MAC,
  aunque supongo que lo hacen solamente si el destino es Broadcast en Ethernet.

Header
------

- Hardware type (HTYPE) (2 bytes): Protocolo de enlace, siempre es Ethernet
  (``0x0001``).

- Protocol type (PTYPE) (2 bytes): Protocolo de red, siempre es IP (``0x0800``).

- Hardware address length (HLEN) (1 byte): Largo de dirección MAC, siempre es 6.

- Protocol address length (PLEN) (1 byte): Largo de dirección IP, siempre es 4.

- Operation (OPER) (2 bytes):

  - 1: Request

  - 2: Reply

  - 3: Request RARP

  - 4: Reply RARP

- Sender hardware address (SHA) (6 bytes): MAC del emisor.

- Protocol hardware address (SPA) (4 bytes): IP del emisor.

- Target hardware address (THA) (6 bytes): MAC del destino.

- Target hardware address (TPA) (4 bytes): IP del destino.

Funcionamiento
--------------

Por ejemplo si A quiere saber la MAC de B:

- Request:

  - Ethernet:

    - Source MAC: A.

    - Destination MAC: ``FF:FF:FF:FF:FF:FF``.

    - Type: ``0x0806`` (ARP).

  - ARP:

    - Opcode: 1 (Request).

    - Sender MAC: A.

    - Target MAC: ``00:00:00:00:00:00``.

    - Sender IP: A.

    - Target IP: B.

- Reply:

  - Ethernet:

    - Source MAC: B.

    - Destination MAC: A.

    - Type: ``0x0806`` (ARP).

  - ARP:

    - Opcode: 2 (Reply).

    - Sender MAC: B.

    - Target MAC: A.

    - Sender IP: B.

    - Target IP: A.

Capturas
--------

Request::

  Frame 75: 42 bytes on wire (336 bits), 42 bytes captured (336 bits) on interface 0
  Ethernet II, Src: LiteonTe_13:12:f4 (24:fd:52:13:12:f4),
      Dst: Broadcast (ff:ff:ff:ff:ff:ff)
    Destination: Broadcast (ff:ff:ff:ff:ff:ff)
    Source: LiteonTe_13:12:f4 (24:fd:52:13:12:f4)
    Type: ARP (0x0806)
  Address Resolution Protocol (request)
    Hardware type: Ethernet (1)
    Protocol type: IPv4 (0x0800)
    Hardware size: 6
    Protocol size: 4
    Opcode: request (1)
    Sender MAC address: LiteonTe_13:12:f4 (24:fd:52:13:12:f4)
    Sender IP address: 192.168.0.102
    Target MAC address: 00:00:00_00:00:00 (00:00:00:00:00:00)
    Target IP address: 192.168.0.200

Reply::

  Frame 1480: 42 bytes on wire (336 bits), 42 bytes captured (336 bits) on interface 0
  Ethernet II, Src: HonHaiPr_13:7f:55 (08:3e:8e:13:7f:55),
      Dst: Micro-St_9e:e5:e1 (d8:cb:8a:9e:e5:e1)
    Destination: Micro-St_9e:e5:e1 (d8:cb:8a:9e:e5:e1)
    Source: HonHaiPr_13:7f:55 (08:3e:8e:13:7f:55)
    Type: ARP (0x0806)
  Address Resolution Protocol (reply)
    Hardware type: Ethernet (1)
    Protocol type: IPv4 (0x0800)
    Hardware size: 6
    Protocol size: 4
    Opcode: reply (2)
    Sender MAC address: HonHaiPr_13:7f:55 (08:3e:8e:13:7f:55)
    Sender IP address: 192.168.0.20
    Target MAC address: Micro-St_9e:e5:e1 (d8:cb:8a:9e:e5:e1)
    Target IP address: 192.168.0.120

ARP gratuitous
--------------

A gratuitous ARP request is an AddressResolutionProtocol request packet where
the source and destination IP are both set to the IP of the machine issuing the
packet and the destination MAC is the broadcast address ff:ff:ff:ff:ff:ff.
Ordinarily, no reply packet will occur. A gratuitous ARP reply is a reply to
which no request has been made.

Para hacer ARP gratuitos::

  arping -A -c 100000 -I enp4s0 {mi IP}

Captura al usar ese comando::

  Frame 32: 60 bytes on wire (480 bits), 60 bytes captured (480 bits) on
      interface 0
  Ethernet II, Src: Dell_17:2b:b2 (10:7d:1a:17:2b:b2), Dst: Broadcast
      (ff:ff:ff:ff:ff:ff)
    Destination: Broadcast (ff:ff:ff:ff:ff:ff)
    Source: Dell_17:2b:b2 (10:7d:1a:17:2b:b2)
    Type: ARP (0x0806)
    Padding: 000000000000000000000000000000000000
  Address Resolution Protocol (reply/gratuitous ARP)
    Hardware type: Ethernet (1)
    Protocol type: IPv4 (0x0800)
    Hardware size: 6
    Protocol size: 4
    Opcode: reply (2)
    [Is gratuitous: True]
    Sender MAC address: Dell_17:2b:b2 (10:7d:1a:17:2b:b2)
    Sender IP address: 192.168.1.215
    Target MAC address: Dell_17:2b:b2 (10:7d:1a:17:2b:b2)
    Target IP address: 192.168.1.215

En la casa del nano capturé ARP gratuitous distintos, algunos reply y otros
request::

  Frame 4: 60 bytes on wire (480 bits), 60 bytes captured (480 bits) on
      interface 0
  Ethernet II, Src: Zhejiang_ad:9f:5f (4c:11:bf:ad:9f:5f), Dst: Broadcast
      (ff:ff:ff:ff:ff:ff)
  Address Resolution Protocol (request/gratuitous ARP)
    Hardware type: Ethernet (1)
    Protocol type: IPv4 (0x0800)
    Hardware size: 6
    Protocol size: 4
    Opcode: request (1)
    [Is gratuitous: True]
    Sender MAC address: Zhejiang_ad:9f:5f (4c:11:bf:ad:9f:5f)
    Sender IP address: 192.168.15.50
    Target MAC address: 00:00:00_00:00:00 (00:00:00:00:00:00)
    Target IP address: 192.168.15.50

  Frame 11: 60 bytes on wire (480 bits), 60 bytes captured (480 bits) on
      interface 0
  Ethernet II, Src: Zhejiang_ad:9f:5f (4c:11:bf:ad:9f:5f), Dst: Broadcast
      (ff:ff:ff:ff:ff:ff)
  Address Resolution Protocol (reply/gratuitous ARP)
    Hardware type: Ethernet (1)
    Protocol type: IPv4 (0x0800)
    Hardware size: 6
    Protocol size: 4
    Opcode: reply (2)
    [Is gratuitous: True]
    Sender MAC address: Zhejiang_ad:9f:5f (4c:11:bf:ad:9f:5f)
    Sender IP address: 192.168.15.50
    Target MAC address: 00:00:00_00:00:00 (00:00:00:00:00:00)
    Target IP address: 192.168.15.50

Hay algunos ARP gratuitous en donde es una request con la target MAC vacía con
ceros, pidiendole al que tenga la misma IP que yo que me responda con su MAC.
