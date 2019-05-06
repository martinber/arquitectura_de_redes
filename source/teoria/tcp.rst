TCP
===

Transmission Control Protocol.

- Orientado a la conexión, funciones:

  - Multiplexación.

  - Control de flujo.

  - Control de congestión: Mediante el mecanismo llamado Explicit Congestion
    Notification (ECN).

  - Control de errores.

  - Retransmisión de segmentos perdidos o erróneos.

- Define un cliente que inicia la conexión y un servidor que escucha y espera
  conexiones.

- Se define un socket que es un conjunto de protocolo, dirección IP y puerto.
  Los puertos del 0 al 1023 están reservados para servicios bien conocidos.

- Se define una conexión que es un conjunto de dos sockets, uno local y otro de
  destino.

- Puede realizarse más de una conexión desde un socket hacia sockets distintos
  pero no se puede establecer una doble conexión entre los mismos dos sockets.

- Cada mensaje se denomina segmento.

- En las conexiones sin estado como HTTP se suele usar RST en lugar de FIN.

- Tipos de servidores:

  - Iterativo: Atiende y corta la conexión rápidamente.

  - Concurrente: Duplica el socket y sigue atendiendo a más clientes.

- Para hacer un firewall que impida conectarse a un host en la LAN hay que
  bloquear los paquetes entrantes con SYN = 1 y ACK = 0.

- Usa un saludo de tres vías para la conexión y desconexión, ver captura al
  final.

Header
~~~~~~

- Source port (16 bits).

- Destination port (16 bits).

- Sequence number (32 bits): Número de secuencia, se incrementa en uno or cada
  byte enviado.

- Acknowledgment number (32 bits): Si la flag ACK está en 1, este campo indica
  el próximo número de secuencia que se espera.

- Data offset (4 bits): Indica el tamaño del header TCP en palabras de 32 bits.
  El campo puede valer entre 5 y 15.

- Reserved (3 bits): Deben ser cero.

- Flags (9 bits): Contiene 9 flags.

  - NS (ECN-nonce): Experimental.

  - CWR (Congestion Window Reduced): Indica que se recibió un segmento con el
    flag ECE y que se ha respondido en el mecanismo de control de congestión.

  - ECE (ECN-echo): Si existe flag SYN, indica que este dispositivo soporta ECN,
    caso contrario indica que ECN en el header IP anuncia congestión.

  - URG (Urgent): Por ejemplo usado en Telnet al hacer Ctrl-C.

  - ACK (Acknowledgment): Indica que se está utilizando el campo Acknowledgment
    Number.

  - PSH (Push): Solicita enviar los datos en el buffer a la aplicación que
    recibe.

  - RST (Reset): Termina la conexión TCP instantáneamente.

  - SYN (Synchronize): Enviado en el primer paquete de cada lado, indica
    sincronizar los números de secuencia.

  - FIN: Termina la conexión.

- Window Size (16 bits): Tamaño de la ventana de recepción para el control de
  flujo. Utiliza por defecto bytes como unidad pero se puede cambiar. No es la
  ventana de congestión.

- Checksum (16 bits): Checksum del header, datos, y de un pseudo header que
  consisite de las direcciones IP, el número de protocolo de TCP (0x0006) y de
  la longitud del header más los datos.

- Urgent pointer (16 bits): Si está la flag URG, este campo lleba un offset
  desde el número de secuencia indicando el último byte urgente.

- Opciones (0 a 320 bits): Cada opción consiste de el tipo (8 bits), y en el
  caso que la opción tenga datos lleva el tamaño (8 bits) y los datos
  (variable). Las opciones que normalmente se usan son:

  - 0 (8 bits): Fin de lista de opciones.

  - 1 (8 bits): Sin operación.

  - 2,4,SS (32 bits): Maximum Segment Size, máximo tamaño de segmento
    especificado en bytes que el dispositivo puede aceptar, se busca que no
    supere el MTU para que IP no fragmente los segmentos.

  - 3,3,S (24 bits): Escala de ventana, se usa para cambiar la unidad del campo
    Window Size y permitir tamaños de ventana que no entran en 16 bits. No se
    aplica a la ventana de congestión.

  - 5,N,BBBB,EEEE,... (80 bits o más): Selective Acknowledgment (SACK), se
    especifica una lista de 1 a 4 bloques de datos siendo selectivamente
    especificados en el ACK. Se especifican como dos punteros de 32 bits, uno
    para el comienzo y otro para el final.

  - 8,10,TTTT,EEEE (80 bits): Timestamp y eco del último timestamp recibido,
    al igual que el número de secuencia permite determinar el orden de los
    segmentos.

Capturas
~~~~~~~~

Resumen de Wireshark de conexión hacia un servidor escuchando en el puerto 1234::

  60684 → 1234 [SYN]      Seq=0 Win=43690 Len=0 MSS=65495 SACK_PERM=1 TSval=1769821607 TSecr=0 WS=1024
  1234 → 60684 [SYN, ACK] Seq=0 Ack=1 Win=43690 Len=0 MSS=65495 SACK_PERM=1 TSval=1769821608 TSecr=1769821607 WS=1024
  60684 → 1234 [ACK]      Seq=1 Ack=1 Win=44032 Len=0 TSval=1769821608 TSecr=1769821608
  60684 → 1234 [PSH, ACK] Seq=1 Ack=1 Win=44032 Len=4 TSval=1769822630 TSecr=1769821608
  1234 → 60684 [ACK]      Seq=1 Ack=5 Win=44032 Len=0 TSval=1769822630 TSecr=1769822630
  60684 → 1234 [PSH, ACK] Seq=5 Ack=1 Win=44032 Len=4 TSval=1769822908 TSecr=1769822630
  1234 → 60684 [ACK]      Seq=1 Ack=9 Win=44032 Len=0 TSval=1769822908 TSecr=1769822908
  60684 → 1234 [PSH, ACK] Seq=9 Ack=1 Win=44032 Len=2 TSval=1769823673 TSecr=1769822908
  1234 → 60684 [ACK]      Seq=1 Ack=11 Win=44032 Len=0 TSval=1769823673 TSecr=1769823673
  60684 → 1234 [FIN, ACK] Seq=11 Ack=1 Win=44032 Len=0 TSval=1769824716 TSecr=1769823673
  1234 → 60684 [FIN, ACK] Seq=1 Ack=12 Win=44032 Len=0 TSval=1769824716 TSecr=1769824716
  60684 → 1234 [ACK]      Seq=12 Ack=2 Win=44032 Len=0 TSval=1769824716 TSecr=1769824716

Funciones
~~~~~~~~~

El sistema operativo define ciertas funciones para trabajar con sockets:

- ``socket``: Crea un socket, recibe el tipo y protocolo.

- ``bind``: Asocia el socket a una dirección IP y puerto.

- ``listen``: Inicia la escucha en el socket, usado por el servidor.

- ``connect``: Inicia la conexión a un servidor, usado por el cliente.

- ``accept``: Aceptar conexión entrante, usado por el servidor.

- ``send``, ``write``: Enviar datos.

- ``receive``, ``read``: Recibir datos.

- ``close``: Cerrar conexión.

Control de flujo
~~~~~~~~~~~~~~~~

- Se utiliza para impedir que un host envíe datos demasiado rápido de tal forma
  que el dispositivo receptor no llega a procesarlos.

- Utiliza una ventana deslizante, cuyo tamaño es informado al otro host en el
  campo Window Size.

Control de congestión
~~~~~~~~~~~~~~~~~~~~~

- Se utiliza para impedir congestionar la red con demasiados segmentos.

- Se utilizan algoritmos específicos, que generalmente contienen una segunda
  ventana deslizante que no es informada en los segmentos. Este tamaño de
  ventana es interno a cada host y se calcula a partir de los ACKs, el RTT, etc.

- Algunos algoritmos usados son Reno, NewReno, Tahoe, Cubic, etc.

- Funciona bien cuando el Bandwidth-delay Product (BDP) es bajo. Se calcula como
  la capacidad de la red (en bits por segundo) y el RTT (en segundos).
