HDLC
====

High-Level Data Link Control

- Es un protocolo de capa 2 para WAN. Se usa en por ejemplo Serial.

- Provee control de flujo y detección de errores.

- Es Punto - Multipunto.

- Está cayendo en desuso por PPP.

Control de flujo
----------------

Es una técnica para que el emisor no sobrecargue al receptor al enviarle más
datos de los que pueda procesar. Vemos los protocolos ARQ, que según tengo
entendido no son específicos a ningún protocolo de capa de enlace.

- Tiempos:

  - Tiempo de transmisión: Tiempo que se tarda en inyectar datos al medio.

  - Tiempo de propagación: Lo que tardan los datos en moverse por el medio.

Métodos
~~~~~~~

- On-Off:

  - No se usa. Creo que no es un protocolo ARQ.

  - La fuente envía mensajes sin esperar ACKs.

  - Cuando se está por llenar el buffer del receptor, éste envía una señal de
    stop que sería RNR (Receptor Not Ready).

  - Cuando vuelve a estar listo envía RR (Receptor Ready).

- Stop and wait ARQ:

  - No se usa.

  - La fuente espera que le respondan un ACK antes de mandar la siguiente
    trama.

  - El destino detiene el flujo al no responder un ACK.

  - Es ineficiente

- Ventanas deslizantes (Go Back N ARQ):

  - Al conectar define tamaño de ventana W.

  - Se transmiten W tramas numeradas sin esperar confirmación.

  - El receptor tiene un buffer de tamaño W.

  - El ACK dice cuál es la trama que espero que llegue. Se llama RR (Receptor
    Ready)

  - El receptor puede dar un RNR (Receptor Not Ready).

  - En full-duplex, al mandar información al mismo tiempo envía los RR.

  - Hay un timeout.

  - Si hay errores pido todas las tramas desde que ocurrio el error, REJ.

  - Si hay una trama que no llegó, el receptor se da cuenta porque se salteó un
    número, entonces pido todas las tramas desde que ocurrió el error.

  - Si la trama que no llegó es justo la última, el receptor nunca nunca envía
    el ACK, entonces en el transmisor salta el timeout y pide un ACK al receptor
    para saber si se cayó la conexión y si llegó el último mensaje.

  - Puede pasar que se pierda un ACK se pierda pero que el siguiente ACK llegue,
    en ese caso confirma todas las anteriores.

- Selective Repeat ARQ/Selective Reject ARQ:

  - No lo vimos.

Detección de errores
--------------------

- Se usa CRC.

- Se debe decidir un polinomio ``P`` de largo ``k``, el polinomio debe ser el
  mismo en el emisor y en el receptor.

- Para cada secuencia de datos genera ``k-1`` bits de CRC. Para generar el CRC
  se agregan ``k-1`` ceros al final de los datos y se divide por el polinomio
  ``P``. El resto es el CRC, que se coloca en el lugar de los ``k-1`` ceros que
  se agregaron.

- Al recibir se dividen los datos y el CRC concatenado por el mismo polinomio
  ``P``, si el resto da cero esta bien.

Tramas
------

- Tramas de información (I-frames): Transporte de datos, puede incluir
  información de control flujo y errores.

- Tramas de supervisión (S-frames): Control de flujo y errores.

- Tramas no numeradas (U-frames): Control de enlace, al inicio de la conexión
  generalmente.

Header
------

- Flag (8 bits): Delimitador de trama ``01111110``, *bit stuffing* si en los
  datos está este patrón.

- Address (8 bits o múltiplo de 8 bits): Identifica la estación secundaria, si
  son todos unos se trata de broadcast. Si el primer bit es un cero, significa
  que la dirección continúa en los siguientes 8 bits, se puede extender
  múltiples veces. Por culpa de ese bit, las direcciones tienen un múltiplo de 7
  bits.

- Control (8 o 16 bits): Identifica tipo de trama.

- Tipo (16 bits): Sólo presente en *Cisco HDLC*, indica el protocolo
  encapsulado.

- Information (variable): Datos (IP). Sólo presente en tramas de información.

- FCS (16 o 32 bits): CRC.

- Flag (8 bits): El mismo delimitador de trama.

Control
~~~~~~~

Los tamaños en bits dependen del tipo de trama y de si se usa control de 8 o 16
bits, cuando se usan 16 bits las secuencias son de 6 bits en vez de 3 bits:

- Información (8 o 16 bits):

  - ``0`` (1 bit).

  - N(S) (3 o 6 bits): Secuencia de envío.

  - P/F (1 bit): Poll/Final bit.

  - N(R) (3 o 6 bits): Secuencia de recepción.

- Supervisión (8 o 16 bits):

  - ``10`` (2 bits).

  - S (2 bits): Opción de supervisión.

  - Padding (0 o 4 bits): Padding en el caso que se use control de 16 bits.

  - P/F (1 bit): Poll/Final bit.

  - N(R) (3 o 6 bits): Secuencia de recepción.

- No numeradas (8 bits):

  - ``11`` (2 bits).

  - S (2 bits): Opción no numerada

  - P/F (1 bit): Poll/Final bit.

  - M (3 bits): Opción no numerada.

El significado del Poll/Final bit depende del contexto:

- En tramas de órdenes solicita respuesta.

- En tramas de respuesta indica respuesta a una solicitud o final de
  transmisión.

Fases de operación
------------------

- Inicialización.

- Transferencia de datos.

- Desconexión.

Bit stuffing
------------

Se necesita porque el delimitador de trama tiene 6 unos seguidos, y se necesita
un sistema para impedir que esa secuencia ocurra en los datos.

Al enviar, si hay 5 unos seguidos se inserta un cero.

Al recibir, si recibe 5 unos seguidos mira el sexto bit:

- Si es un cero, se trata de datos, entonces borra el cero.

- Si es uno miro el séptimo bit:

  - Si es un cero se trata de un delimitador.

  - Si es un uno hubo algún error.
