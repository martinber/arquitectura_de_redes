VOIP
====

Acá hay información sobre VoIP en sí, señalización, etc. Ver también sobre
transmisión de audio en :doc:`multimedia`.

- En comparación con la telefonía tradicional permite menores costos, calidad
  similar, utilizar redes IP que son muy extendidas, etc. Como desventaja puede
  haber pérdida de paquetes, jitter, degradación de calidad, altos retardos,
  etc.

- La señalización permite por ejemplo:

  - Informar al dispositivo llamado para hacer sonar.

  - Informar al dispositivo que llama sobre la conexión.

  - Evitar llamadas a dispositivos ocupados.

  - Registrar la llamada, incluyendo la duración.

- Algunos protocolos de señalización: SIP, MGCP, SCCP, H.323, IAX, etc.

- Algunos sistemas propietarios:

  - Skype.

  - Cisco Skinny (SCCP).

- Algunos sistemas abiertos:

  - SIP.

  - H.323.

  - IAX.

Partes involucradas
-------------------

- Los teléfonos IP son similares a los tradicionales pero tienen entrada
  Ethernet y dirección IP.

- Los softphones son software para llamadas que se puede instalar en ordenadores
  (estilo Skype).

- Foreign Exchange Station (FXS): Es una interfaz (puerto) que provee energía,
  tono de marcado y genera tensión de llamada (para el ring). Son los puertos
  que proveen los PSTN a los clientes, es donde se
  conectan los teléfonos tradicionales.

- Foreign Exchange Office (FXO): Es una interfaz (puerto) que señalizan cuando
  la línea está colgada o cortada, son los puertos que tienen los teléfonos
  tradicionales.

- Public Switched Telephone Network (PSTN): Red Telefonía Básica (RTB), se
  refiere a teléfonos tradicionales en lugar de VoIP.

- Private Branch Exchange (PBX): Es una central telefónica privada que conecta a
  todos los teléfonos internos y generalmente está conectada a unas pocas líneas
  telefónicas externas. Por lo tanto de un lado tiene FXO para conectarse a por
  ejemplo la PSTN y en el caso del teléfono tradicional tiene FXS para los
  teléfonos internos.

- Gateway (GWY): Permiten interconectar y transformar llamadas tradicionales en
  IP y viceversa. Se clasifican dependiendo de si tienen puertos FXS o FXO:

  - Gateway FXS: Tienen puertos FXS que permite conectar teléfonos tradicionales
    a una central IP.

  - Gateway FXO: Tienen puertos FXO que permite conectar la central IP a la
    PSTN.

SIP
---

Session Initiation Protocol.

El protocolo tienen muchas aplicaciones además de VoIP pero explico todo lo
relacionado a llamadas de voz.

- Son mensajes de control que se envían fuera de banda.

- A diferencia del flujo de audio, SIP usa un puerto conocido.

- Es basado en texto, similar a HTTP o SMTP.

- Trabaja con URIs de la forma ``usuario@host`` para describir a los diferentes
  dispositivos. Antes de la arroba se escribe el número de teléfono o nombre de
  usuario, luego se coloca el nombre de dominio o dirección IP.

- Usa el protocolo SDP (Session Description Protocol) encapsulado dentro del
  datagrama SIP para negociar el tipo de multimedia y los parámetros a utilizar.

- Se puede usar sobre UDP, TCP, SCTP o TLS. Generalmente usa puerto 5060, o 5061
  en el caso de usar TLS.

- Permite señalizar y configurar llamadas VoIP punto a punto, sesiones
  multimedia multicast, videollamadas permitiendo agregar y quitar usuarios en
  vivo, etc.

- Tipos de agentes:

  - User Agent (UA): Es un punto que maneja las sesiones SIP. Se subdivide en
    User Agent Client (UAC) y User Agent Server (UAS), todos los dispositivos
    cumplen las funciones de cliente y de servidor en momentos distintos.

  - Proxy Server: Es un servidor que además es UA, permite rutear peticiones
    primero determinando si el usuario puede realizar la llamada. Actúa de
    intermediario entre agentes SIP, una vez establecida la conexión los agentes
    se comunican directamente. Generalmente contienen un registrar.

  - Redirection Server: Genera respuestas de redirección del tipo ``3XX`` para
    encaminar las peticiones al siguiente servidor.

  - Registrar: Es un dispositivo que acepta requests REGISTER almacenando la
    dirección y otros parámetros del UA. Luego permite a UAs a localizar otros
    UAs para realizar llamadas, algo así como un DNS.

  - Gateway: Permite conectar redes SIP a otras redes, por ejemplo PSTN como
    expliqué arriba.

Peticiones
~~~~~~~~~~

Estas son las más usadas en VoIP:

- REGISTER: Solicita registrar un UA para que los demás UAs lo puedan encontrar.

- INVITE: Inicia o modifica una llamada, por ejemplo al poner una llamada en
  espera.

- ACK: Confirma una solicitud INVITE.

- BYE: Corta una llamada.

- OPTION: Solicita informacion sobre las capacidades del servidor.

- CANCEL: Cancela una solicitud, por ejemplo para cortar una llamada mientras
  todavía ésta está sonando.

Respuestas
~~~~~~~~~~

- ``1XX``: La operación se ha iniciado correctamente.

  - ``100``: Intentando.

  - ``180``: Sonando.

- ``2XX``: La operación se ha completado correctamente.

  - ``200``: Comando OK.

- ``3XX``: Para redirección, debe hacerse la petición a otro destino.

  - ``301``: Movido permanentemente, indica hacia dónde.

  - ``302``: Movido temporalmente, indica hacia dónde.

- ``4XX``: Falla general.

  - ``400``: Error de sintaxis.

  - ``401``: Sin autorización.

  - ``404``: No encontrado.

  - ``486``: Ocupado.

- ``5XX``: Comando válido pero el servidor no pudo completar la operación.

  - ``500``: Error interno.

  - ``501``: Comando no implementado.

- ``6XX``: No se puede completar la petición en ningún servidor.

  - ``600``: Todos los destinos ocupados.

  - ``603``: Llamada rechazada.

SDP
---

Session Description Protocol.

- Va dentro de peticiones SIP, por ejemplo en peticiones INVITE o respuestas de
  OK.

- Negocia el tipo de multimedia, formato y demás propiedades

Campos
~~~~~~

Copiado desde RFC 4566::

  Session description
     v=  (protocol version)
     o=  (originator and session identifier)
     s=  (session name)
     i=* (session information)
     u=* (URI of description)
     e=* (email address)
     p=* (phone number)
     c=* (connection information -- not required if included in
          all media)
     b=* (zero or more bandwidth information lines)
     One or more time descriptions ("t=" and "r=" lines; see below)
     z=* (time zone adjustments)
     k=* (encryption key)
     a=* (zero or more session attribute lines)
     Zero or more media descriptions

  Time description
     t=  (time the session is active)
     r=* (zero or more repeat times)

  Media description, if present
     m=  (media name and transport address)
     i=* (media title)
     c=* (connection information -- optional if included at
          session level)
     b=* (zero or more bandwidth information lines)
     k=* (encryption key)
     a=* (zero or more media attribute lines)

Ejemplo copiado desde RFC 4566::

  v=0
  o=jdoe 2890844526 2890842807 IN IP4 10.47.16.5
  s=SDP Seminar
  i=A Seminar on the session description protocol
  u=http://www.example.com/seminars/sdp.pdf
  e=j.doe@example.com (Jane Doe)
  c=IN IP4 224.2.17.12/127
  t=2873397496 2873404696
  a=recvonly
  m=audio 49170 RTP/AVP 0
  m=video 51372 RTP/AVP 99
  a=rtpmap:99 h263-1998/90000

IAX
---

Es un protocolo abierto para señalización que es una alternativa propia de
Asterisk a SIP. A diferencia de SIP:

- Está basado en mensajes binarios en lugar de mensajes de texto.

- La senalización y los datos viajan juntos lo que evita problemas de NAT.
  Utiliza un solo puerto para todas las comunicaciones. SIP necesita un puerto
  para señalización y dos por cada llamada.

- No es un protocolo tan estandarizado como SIP.

- En SIP es posible transmitir la señalización por el servidor mientras que el
  audio puede viajar punto a punto. En IAX todo debe pasar por el servidor lo
  que requiere de más recursos en el servidor.
