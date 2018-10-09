PPPoE
=====

PPP over Ethernet.

- Va dentro de Ethernet.

- Se le agrega un encabezado PPPoE antes del encabezado PPP, aunque el
  encabezado PPP se recorta dejando solamente el campo del protocolo
  encapsulado, o sea que de PPP no queda nada basicamente.

- Cada extremo debe saber la MAC y número de sesión del otro, entonces hay un
  protocolo de descubrimiento.

- Puedo estar en estado de descubrimiento o de sesión.

Se usa para hacer un enlace punto a punto en redes LAN (que usan Ethernet) pero
el uso más común es en las líneas de ADSL.

Como el par de cobre es punto a punto esperaría que usen PPP directamente. Eso
se llama PPPoA (PPP over ATM), ATM es el protocolo que se usaba en ADSL,
entonces el orden es: ADSL - ATM - PPP - IP.

Al usar PPPoE en ADSL (que más común), el orden es: ADSL - ATM - Ethenet - PPPoE
- PPP - IP.

Estados
-------

- Descubrimiento: Hay 4 mensajes:

  - PADI (PPPoE Active Discovery Initiation): El cliente pregunta por
    servidores.

  - PADO (PPPoE Active Discovery Offer): Un servidor ofrece.

  - PADR (PPPoE Active Discovery Request): El cliente acepta una propuesta.

  - PADS (PPPoE Active Discovery Session-confirmation): El servidor confirma.

- Sesión: Se envía el encabezado PPP en los datos junto con el paquete IP y todo
  lo demás. Los paquetes son unicast.

Apenas termina el establecimiento de PPPoE, se comienza a transmitir sobre PPP,
específicamente los protocolos LCP (IPCP), PAP y CHAP.

Capturas
--------

Sacadas de `esta captura`__.

__ https://wiki.wireshark.org/SampleCaptures#Point-To-Point_over_Ethernet

PPPoE Active Discovery Initiation::

  Frame 1: 24 bytes on wire (192 bits), 24 bytes captured (192 bits)
  Ethernet II, Src: 20:28:18:a0:a9:d2 (20:28:18:a0:a9:d2),
      Dst: Broadcast (ff:ff:ff:ff:ff:ff)
  PPP-over-Ethernet Discovery
    0001 .... = Version: 1
    .... 0001 = Type: 1
    Code: Active Discovery Initiation (PADI) (0x09)
    Session ID: 0x0000
    Payload Length: 4
    PPPoE Tags

PPPoE Active Discovery Offer::

  Frame 2: 60 bytes on wire (480 bits), 60 bytes captured (480 bits)
  Ethernet II, Src: Unispher_a4:10:be (00:90:1a:a4:10:be),
      Dst: 20:28:18:a0:a9:d2 (20:28:18:a0:a9:d2)
  PPP-over-Ethernet Discovery
    0001 .... = Version: 1
    .... 0001 = Type: 1
    Code: Active Discovery Offer (PADO) (0x07)
    Session ID: 0x0000
    Payload Length: 35
    PPPoE Tags
      AC-Name: r-al121
      AC-Cookie: bebcb53c10b32769a8661c36a45d8720

PPPoE Active Discovery Request::

  Frame 3: 44 bytes on wire (352 bits), 44 bytes captured (352 bits)
  Ethernet II, Src: 20:28:18:a0:a9:d2 (20:28:18:a0:a9:d2),
      Dst: Unispher_a4:10:be (00:90:1a:a4:10:be)
  PPP-over-Ethernet Discovery
    0001 .... = Version: 1
    .... 0001 = Type: 1
    Code: Active Discovery Request (PADR) (0x19)
    Session ID: 0x0000
    Payload Length: 24
    PPPoE Tags
      AC-Cookie: bebcb53c10b32769a8661c36a45d8720

PPPoE Active Discovery Session-confirmation::

  Frame 4: 60 bytes on wire (480 bits), 60 bytes captured (480 bits)
  Ethernet II, Src: Unispher_a4:10:be (00:90:1a:a4:10:be),
      Dst: 20:28:18:a0:a9:d2 (20:28:18:a0:a9:d2)
  PPP-over-Ethernet Discovery
    0001 .... = Version: 1
    .... 0001 = Type: 1
    Code: Active Discovery Session-confirmation (PADS) (0x65)
    Session ID: 0x18b2
    Payload Length: 4
    PPPoE Tags

Lista completa con los demás mensajes::

  MACclient  Broadcast  PPPoED      Active Discovery Initiation (PADI)
  MACserver  MACclient  PPPoED      Active Discovery Offer (PADO) AC-Name='serv'
  MACclient  MACserver  PPPoED      Active Discovery Request (PADR)
  MACserver  MACclient  PPPoED      Active Discovery Session-confirmation (PADS)
  MACclient  MACserver  PPP LCP     Configuration Request
  MACserver  MACclient  PPP LCP     Configuration Request
  MACserver  MACclient  PPP LCP     Configuration Ack
  MACclient  MACserver  PPP LCP     Configuration Ack
  MACclient  MACserver  PPP LCP     Echo Request
  MACclient  MACserver  PPP PAP     Authenticate-Request (Peer-ID='usr', Password='pwd')
  MACserver  MACclient  PPP LCP     Echo Reply
  MACserver  MACclient  PPP PAP     Authenticate-Ack (Message='')
  MACclient  MACserver  PPP IPCP    Configuration Request
  MACclient  MACserver  PPP IPV6CP  Configuration Request
  MACserver  MACclient  PPP IPCP    Configuration Nak
  MACclient  MACserver  PPP IPCP    Configuration Request
  MACserver  MACclient  PPP LCP     Protocol Reject
  MACserver  MACclient  PPP IPCP    Configuration Ack
  MACserver  MACclient  PPP IPCP    Configuration Request
  MACclient  MACserver  PPP IPCP    Configuration Ack
  MACclient  MACserver  PPP LCP     Echo Request
  MACserver  MACclient  PPP LCP     Echo Reply
  MACclient  MACserver  PPP LCP     Echo Request
  MACserver  MACclient  PPP LCP     Echo Reply
  MACclient  MACserver  PPP LCP     Echo Request
  MACserver  MACclient  PPP LCP     Echo Reply
  MACclient  MACserver  PPP LCP     Echo Request
  MACserver  MACclient  PPP LCP     Echo Reply

Header
------

- Version (4 bits): Siempre es 1.

- Type (4 bits): Siempre es 1.

- Code (8 bits):

  - 9: PADI.

  - 7: PADO.

  - 25: PADR.

  - 101: PADS.

  - 0: Estado de sesión, se envían paquetes IP.

- Session ID (16 bits): Identifica la sesión PPPoE.

- Length (16 bits): Largo de los datos en bytes.

- Datos (variable).
