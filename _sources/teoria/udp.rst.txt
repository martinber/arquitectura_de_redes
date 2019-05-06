UDP
===

User Datagram Protocol.

- No orientado a la conexión, funciones:

  - Multiplexación.

  - Control de errores, pero no retransmisión.

- Se define un socket que es un conjunto de protocolo, dirección IP y puerto.
  Los puertos del 0 al 1023 están reservados para servicios bien conocidos.

- Cada mensaje se denomina datagrama.

Header
~~~~~~

- Source port (16 bits).

- Destination port (16 bits).

- Length (16 bits): Longitud en bytes del header y de los datos.

- Checksum (16 bits): Checksum del header, de los datos y de un pseudo header
  que contiene información de IP. Se puede no utilizar y dejar en todos ceros,
  pero en IPv6 es obligatorio.

Funciones
~~~~~~~~~

El sistema operativo define ciertas funciones para trabajar con sockets:

- ``socket``: Crea un socket, recibe el tipo y protocolo.

- ``bind``: Asocia el socket a una dirección IP y puerto.

- ``listen``: Inicia la escucha en el socket.

- ``sendto``: Enviar datagrama a cierto socket.

- ``recvfrom``: Recibir datos.

- ``close``: Cerrar socket.
