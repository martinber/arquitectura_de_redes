PPP
===

Point to Point Protocol.

- De WAN. Sucesor y variante de HDLC. Normalmente se usa en en cables seriales,
  líneas de telefono, etc. Para ADSL se usa PPPoE o PPPoA.

- Hace lo mismo que HDLC agregando autenticación, y en un ambiente orientado a
  la difusión.

Ventajas con HDLC:

- Asignación dimámica de IP.

- Soporta varios protocolos de capa 3.

- Tiene mecanismo de control de red (NCP).

- Autenticación.

- Cifrado.

- Compresión de datos.

Header
------

Es un caso particular del Cisco HDLC.

- Flag (8 bits): Delimitador de trama ``01111110``, *bit stuffing* si en los
  datos está este patrón.

- Address (8 bits): Son siempre ``11111111``.

- Control (8 bits): Siempre ``0x03`` que significa *unnumbered data*.

- Protocol (16 bits): Protocolo encapsulado. Viene a ser el campo Type en Cisco
  HDLC pero ahora es estándar.

  - ``0x0021`` para IP.

  - ``0xC021`` para LCP.

  - ``0x80XX`` para varios tipos de NCP.

- Information (variable): Datos (IP).

- FCS (16 bits): CRC.

- Flag (8 bits): El mismo delimitador de trama.

Estados
-------

.. image:: ./estados_ppp.png
   :width: 70%

Parecido a HDLC, los estados son:

- Inicialización.

- Determinación de calidad de enlace (opcional).

- Transferencia de datos.

- Desconexión.

Control de enlace
-----------------

Tiene un protocolo de control de enlace: LCP (Link Control Protocol).

- Controla la identidad del dispositivo cliente, aceptándolo o rechazándolo.
  Creo que a esta parte la hace PAP (más inseguro, contraseñas en texto plano) o
  CHAP (más seguro).

- Determina el tamaño aceptable de paquete.

- Busca errores de configuración.

Control de red
--------------

Tiene un mecanismo de control de red, que es medio de capa 3: NCP (Network
Control Protocol). Para cada protocolo de red usado, hay un NCP correspondiente.

En el caso de IP, se usa IPCP (Internet Protocol Control Protocol), le va a dar
la IP al router, no se usa DHCP porque es para LAN.
