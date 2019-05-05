Multimedia
==========

Codecs
------

Audio
~~~~~

- G.711: Para VoIP. No comprime, 64kbps. También se le suele llamar PCM?.

- G.728: Para VoIP. 18kbps.

- G.729: Para VoIP. 8kbps.

Video
~~~~~

Se usa compresión espacial o intraframe para comprimir cada frame y a veces
también compresión temporal o interframe para aprovechar la redundancia que hay
entre fotogramas consegutivos.

Tipos de frames;

- I (Intra): Compresión espacial.

- P (Predictive): Informa las diferencias respecto al frame I o P anterior.

- B (Bidireccional): Informa las diferencias respecto a los frames I o P
  anterior y posterior.

Algunos codecs:

.. todo:: Entender relacion MPEG con MP3, MP4, H264, H263, AAC, etc.

- MPEG (H.222/H.262)

- VP9: Para video de alta calidad.

- H.263: Para videoconferencias.

- H.264: Para video de alta calidad.



RTP
---

Real-time Transport Protocol.

- Permite enviar audio y video.

- Normalmente va sobre UDP.

- Confía en RTCP para control, sincronización de múltiples streams, calidad de
  servicio, etc.

- En general el stream se inicia con un protocolo de señalización como RTSP, SIP
  o H.323.

- Se suele utilizar también para VoIP, en tal caso se usa junto al protocolo
  SIP.

- Cosas que incluye el header:

  - Timestamps para indicar a qué tiempo se corresponde el audio/video que lleva
    en los datos.

  - Números de secuencia, para reordenar los paquetes en el caso de jitter y
    para la pérdida de paquetes.

  - Descripción del formato de los datos que envía.

RTCP
----

RTP Control Protocol.

- Provee información y estadísticas para RTP.

- Normalmente va sobre UDP.

- Envía periódicamente estadísticas de cantidad de paquetes, pérdida de
  paquetes, jitter, RTT, etc. Las aplicaciones usan esta información para
  cambiar parámetros del stream, limitar el flujo, cambiar el codec, etc.

RTSP
----

Real-time Streaming Protocol.

- Permite a las aplicaciones que utilizan RTP, controlar el stream.

- Va sobre TCP.

- Es similar a HTTP pero a diferencia de HTTP que es *stateless*, RTSP mantiene
  estado.

- Permite por ejemplo poner play, pausa, establecer la multimedia a reproducir,
  establecer puertos a utilizar para RTP y RTCP, etc.

SRTP
----

Secure Real-time Transport Protocol.

- Es la versión segura de RCP, creo que no usa TLS.

SRTCP
-----

Secure RTCP.

- Es la versión segura de RTCP, creo que no usa TLS.
