HTTP
====

Hypertext Transfer Protocol.

- Utiliza MIME, ver :doc:`mail`.

- Para mí usa URLs, pero por ahí leo que usa URIs, no se:

  - URI (Universal Resource Identifier): Es un string de caracteres que
    identifica sin ambiguedades a un recurso. Abarca al conecpto de URN y URL.

  - URN (Universal Resource Name): Es una URI que identifica a un recurso por
    nombre pero no dice dónde está ubicado. Por ejemplo un nombre cualquiera.

  - URL (Universal Resource Locator): Es una URI que especifica la ubicación de
    un recurso, por ejemplo
    ``http://ejemplo.com:8080/carpeta/archivo.extension?query=value&query2=value2``.
    Especifica protocolo, nombre de servidor, puerto, ubicación de recurso y
    opcionalmente dar valores.

Versiones
---------

- HTTP/0.9: Obsoleta. Soporta sólo un comando, GET, no especifica el número de
  versión HTTP.

- HTTP/1.0 (mayo 1996): Especifica su versión en las comunicaciones, y todavía
  se usa ampliamente, sobre todo en servidores proxy.

- HTTP/1.1 (junio 1999): Las conexiones persistentes están activadas por defecto
  y funcionan bien con los proxies. También permite al cliente enviar múltiples
  peticiones a la vez (pipelining) lo que hace posible eliminar el tiempo de
  round-trip delay por cada petición.

- HTTP/2.0 (mayo 2015): Minimiza el número de peticiones ya que el servidor
  responde con datos que no fueron requeridos por el cliente en los casos en
  donde el servidor conoce lo que necesita el cliente. De esta forma no es
  necesario que el cliente analice la respuesta y haga otra petición.

- HTTP/3.0 (futuro): Se basará en el *draft* RFC que permite HTTP sobre QUIC
  (que vendría a ser un protocolo de transporte de baja latencia basado en UDP).

Requests
--------

Las peticiones contienen:

1. Una línea que contiene el método, su argumento y la versión, por ejemplo ``GET
   /index.html HTTP/1.1``.

2. Headers de request.

3. Una línea vacía.

4. Un cuerpo de mensaje (a veces es opcional)

Métodos
~~~~~~~

Pueden tener como propiedades:

- Safe: Por ejemplo GET y HEAD se consideran seguros porque normalmente no
  tienen efectos secundarios, sólo realizan lecturas de recursos. De todas
  formas el servidor puede producir efectos secundarios pero ya es cuestión
  suya.

- Idempotent: Por ejemplo GET, HEAD, PUT y DELETE, significa que producen el
  mismo resultado si son llamados una vez que si son llamados más de una vez.

- Cacheable: Significa que la respuesta es cacheable.

La lista de métodos más usados es:

- GET (Safe, Idempotent, Cacheable): Solicita un recurso determinado. Es posible
  hacer un *GET condicional* si se usa un header *If-Modified-Since*,
  *If-Match*, etc. Es posible hacer un *GET parcial* si se especifica un rango.

- HEAD (Safe, Idempotent, Puede ser cacheable): Igual que GET, pero solicita
  sólo el encabezado, dejando atrás el cuerpo de la respuesta.

- PUT (Idempotent): Solicita al servidor ubicar los datos presentes en esta
  solicitud en la ubicación determinada por la URI, ya sea modificando o creando
  un recurso nuevo.

  Si se crea el recurso debe responder 201 (Created). Si se modifica un recurso
  debe responder 200 (Ok) o 204 (No Content).

- POST (Cacheable): Similar a PUT pero la URI determina la ubicación del
  contenedor del recurso en vez de la ubicación del recurso en sí. Por ejemplo
  la URI puede describir a una carpeta, un formulario, una base de datos, un
  grupo de noticias, etc.

  Si el recurso creado no puede identificarse por una URI responde con un 200
  (OK) o 204 (No Content). Si se crea un recurso debe responder con 201
  (Created) y especificar la ubicación.

- DELETE (Idempotent): Solicita el borrado de un determinado recurso.

  Debe responder 200 (OK) o 204 (No Content) si se ha borrado, si está por ser
  borrado debe responder 202 (Accepted).

- TRACE (Safe, Idempotent): Solicita al servidor que responda con todos los
  datos que recibe en un 200 (OK), una especie de *echo*. Se usa para observar
  si servidores intermedios hacen modificaciones en la petición.

- OPTIONS (Idempotent): Solicita que el servidor responda con los métodos HTTP
  que soporta en determinada URI, si la URI es un ``*`` se aplica al servidor en
  general.

- CONNECT: Sólo se utiliza ante servidores proxy, solicita al proxy realizar un
  túnel TCP para tráfico de cualquier tipo, ver abajo sobre Proxy.

- PATCH: Produce determinadas modificaciones en un recurso.

Headers
~~~~~~~

.. todo:: HEaders

Más usados:

- Accept: Indica que sólo acepta recibir ciertos tipos de archivos.

- Accept-Charset: Tipos de codificación de caracteres aceptados.

- Accept-Encoding: Por ejemplo si acepta archivos comprimidos.

- Accept-Language: Lenguajes naturales aceptados (inglés, español, ...).

- Allow: Métodos válidos para ser usados en ese recurso.

Respuestas
----------

La respuesta consiste de:

1. Línea de estado, que contiene la versión, el código de estado y un mensaje.

2. Headers de respuesta.

3. Línea vacía.

4. Un cuerpo de mensaje (a veces es opcional)

La lísta de códigos de estado más importante es:

- Informational: 1XX

- Successful: 2XX

- Redirection: 3XX

- Client Error: 4XX

- Server Error: 5XX

HTTPS
-----

.. warning:: No tengo mucha idea sobre esto así que a lo mejor le pifio en algo.

.. todo:: Explicar en forma general que es HTTPS, TLS, SSL, etc.

Proxy
-----

Voy a hablar sobre proxy centrandome en el servidor Squid, pero hay más.

Tipos
~~~~~

- Elite Proxy: El proxy hace la request hacia el servidor destino normalmente,
  como si la petición se originara desde el proxy.

- Anonymous Proxy: Al hacer la petición, el proxy coloca el header *HTTP_VIA*
  para indicar que se está utilizando un proxy. Opcionalmente utiliza también el
  header *HTTP_X_FORWARDED_FOR* indicando la IP del proxy.

- Transparent Proxy: El proxy utiliza el header *HTTP_X_FORWARDED_FOR* colocando
  la IP del cliente, de esta forma el servidor de destino conoce la IP del
  cliente. También se envía *HTTP_VIA* indicando que se está utilizando un
  servidor proxy.

- Inline, Forced o Transparent Proxy: A veces se usa el término *Transparent
  Proxy* para nombrar a los servidores proxy que interceptan las comunicaciones
  entre el cliente y el servidor web, por lo tanto no es necesaria la
  configuración de los clientes. Se necesita habilitar el forwarding IP en el
  proxy.

- Accelerator proxy: Se coloca en frente de uno o unos pocos servidores web y se
  apunta al DNS para que las consultas de los clientes vayan al proxy. De esta
  forma el proxy permite mejorar el rendimiento de un servidor web lento gracias
  a los cachés.

- Túnel CONNECT: El cliente pide a un proxy redirigir una conexión TCP a un
  destino, sólo la petición inicial es HTTP, luego de eso es simplemente un
  túnel TCP. Se utiliza sobre todo para hacer proxy de HTTPS, ver abajo para más
  info.

.. todo:: Buscar sobre la aquitectura multinivel

Proxy HTTPS
~~~~~~~~~~~

.. warning:: No tengo mucha idea, esto es lo que leí por ahí. Pongo las
  referencias a todo lo que pueda.

Como la comunicación es punto a punto no se puede descifrar la información, pero
sí se puede hacer de túnel por donde pasarán las comunicaciones cifradas. Para
el proxy el tráfico no es más que una conexión TCP, y no puede obtener ningún
tipo de información. Esto se llama `túnel CONNECT`__.

__ https://en.wikipedia.org/wiki/HTTP_tunnel

Como el tráfico es simplemente una conexión TCP, el proxy no tiene forma de
saber si es realmente tráfico HTTPS o una conexión TCP cualquiera. Por eso Squid
usa la directiva ``deny CONNECT !SSL_Ports`` que impide túneles *CONNECT* en
puertos que no sean específicos de SSL (`ver acá`__).

__ https://wiki.squid-cache.org/Features/HTTPS

Si uno quiere realmente descifrar tráfico HTTPS tiene que hacer una conexión
cifrada entre el proxy y el servidor web y otra entre el proxy y el cliente. La
conexión entre el proxy y el servidor web no es un problema, ya que el servidor
web ve al proxy como un cliente más. El problema está en que el cliente no se
debe dar cuenta de que está conectándose al proxy en vez del servidor web, por
lo tanto el proxy debe crear un certificado temporal generado dinámicamente para
cada conexión en particular. Esto se llama ataque *Man-in-the-middle*.

Cuando uno se conecta a una página web con HTTPS, obtiene un certificado firmado
por un *Certificate Authority* (*CA*) que es una organización confiable, la
lista de organizaciones confiables es seleccionada por Google/Mozilla ya que
viene dentro del navegador. Cuando uno solicita un certificado, el *CA* se
asegura de que uno realmente tiene posesión del sitio por el cual uno solicita
el certificado, esta es la razón por la cual no es posible hacer ataques
*Man-in-the-middle* y por lo tanto proxies que descifren tráfico HTTPS.

Una alternativa es crear certificados que no están firmados por una *CA*, estos
certificados se llaman *self-signed* ya que están cifrados por nosotros mismos.
El problema con estos certificados es que los navegadores no confían en ellos y
al conectarse muestran una pantalla roja de advertencia. Para eliminar esto uno
debe configurar el navegador de cada cliente para que confíe en nuestros
certificados.

`Como dice la documentación de Squid`__ no es imposible de que un *CA* otorgue
permisos para generar certificados firmados a discreción, pero esto puede llevar
a que los navegadores dejen de confiar en estos *CAs* `como pasó`__ `con
Trustwave`__.

__ https://wiki.squid-cache.org/ConfigExamples/Intercept/SslBumpExplicit

__ https://bugzilla.mozilla.org/show_bug.cgi?id=724929

__ https://www.computerworld.com/article/2501291/internet/trustwave-admits-issuing-man-in-the-middle-digital-certificate--mozilla-debates-punishment.html

Capturas
--------

Captura al acceder a http://neverssl.com, que es uno de los pocos sitios
conocidos que quedan sin seguridad HTTPS::

  192.168.0.200  99.84.25.225    TCP     33386 → 80 [SYN] Seq=0 Win=29200 Len=0 MSS=1460 SACK_PERM=1 TSval=775244109 TSecr=0 WS=1024
  99.84.25.225   192.168.0.200   TCP     80 → 33386 [SYN, ACK] Seq=0 Ack=1 Win=28960 Len=0 MSS=1412 SACK_PERM=1 TSval=1721758292 TSecr=775244109 WS=256
  192.168.0.200  99.84.25.225    TCP     33386 → 80 [ACK] Seq=1 Ack=1 Win=29696 Len=0 TSval=775244180 TSecr=1721758292
  192.168.0.200  99.84.25.225    HTTP    GET / HTTP/1.1
  99.84.25.225   192.168.0.200   TCP     80 → 33386 [ACK] Seq=1 Ack=321 Win=30208 Len=0 TSval=1721758382 TSecr=775245004
  99.84.25.225   192.168.0.200   TCP     80 → 33386 [PSH, ACK] Seq=1 Ack=321 Win=30208 Len=428 TSval=1721758382 TSecr=775245004 [TCP segment of a reassembled PDU]
  192.168.0.200  99.84.25.225    TCP     33386 → 80 [ACK] Seq=321 Ack=429 Win=30720 Len=0 TSval=775245069 TSecr=1721758382
  99.84.25.225   192.168.0.200   TCP     80 → 33386 [PSH, ACK] Seq=429 Ack=321 Win=30208 Len=1215 TSval=1721758404 TSecr=775245069 [TCP segment of a reassembled PDU]
  192.168.0.200  99.84.25.225    TCP     33386 → 80 [ACK] Seq=321 Ack=1644 Win=33792 Len=0 TSval=775245292 TSecr=1721758404
  99.84.25.225   192.168.0.200   HTTP    HTTP/1.1 200 OK  (text/html)
  192.168.0.200  99.84.25.225    TCP     33386 → 80 [ACK] Seq=321 Ack=1649 Win=33792 Len=0 TSval=775245293 TSecr=1721758404
  192.168.0.200  99.84.25.225    HTTP    GET /favicon.ico HTTP/1.1
  99.84.25.225   192.168.0.200   HTTP    HTTP/1.1 200 OK  (PNG)
  192.168.0.200  99.84.25.225    TCP     33386 → 80 [ACK] Seq=573 Ack=2211 Win=35840 Len=0 TSval=775245439 TSecr=1721758419
  192.168.0.200  99.84.25.225    TCP     [TCP Keep-Alive] 33386 → 80 [ACK] Seq=572 Ack=2211 Win=35840 Len=0 TSval=775255443 TSecr=1721758419
  99.84.25.225   192.168.0.200   TCP     [TCP Keep-Alive ACK] 80 → 33386 [ACK] Seq=2211 Ack=573 Win=31232 Len=0 TSval=1721759425 TSecr=775245439
  192.168.0.200  99.84.25.225    TCP     [TCP Keep-Alive] 33386 → 80 [ACK] Seq=572 Ack=2211 Win=35840 Len=0 TSval=775265711 TSecr=1721759425
  99.84.25.225   192.168.0.200   TCP     [TCP Keep-Alive ACK] 80 → 33386 [ACK] Seq=2211 Ack=573 Win=31232 Len=0 TSval=1721760452 TSecr=775245439
  192.168.0.200  99.84.25.225    TCP     [TCP Keep-Alive] 33386 → 80 [ACK] Seq=572 Ack=2211 Win=35840 Len=0 TSval=775275951 TSecr=1721760452
  99.84.25.225   192.168.0.200   TCP     [TCP Keep-Alive ACK] 80 → 33386 [ACK] Seq=2211 Ack=573 Win=31232 Len=0 TSval=1721761476 TSecr=775245439
  192.168.0.200  99.84.25.225    TCP     [TCP Keep-Alive] 33386 → 80 [ACK] Seq=572 Ack=2211 Win=35840 Len=0 TSval=775286191 TSecr=1721761476
  99.84.25.225   192.168.0.200   TCP     [TCP Keep-Alive ACK] 80 → 33386 [ACK] Seq=2211 Ack=573 Win=31232 Len=0 TSval=1721762500 TSecr=775245439
  192.168.0.200  99.84.25.225    TCP     [TCP Keep-Alive] 33386 → 80 [ACK] Seq=572 Ack=2211 Win=35840 Len=0 TSval=775296431 TSecr=1721762500
  99.84.25.225   192.168.0.200   TCP     [TCP Keep-Alive ACK] 80 → 33386 [ACK] Seq=2211 Ack=573 Win=31232 Len=0 TSval=1721763524 TSecr=775245439
  192.168.0.200  99.84.25.225    TCP     33386 → 80 [FIN, ACK] Seq=573 Ack=2211 Win=35840 Len=0 TSval=775298329 TSecr=1721763524
  99.84.25.225   192.168.0.200   TCP     80 → 33386 [FIN, ACK] Seq=2211 Ack=574 Win=31232 Len=0 TSval=1721763714 TSecr=775298329
  192.168.0.200  99.84.25.225    TCP     33386 → 80 [ACK] Seq=574 Ack=2212 Win=35840 Len=0 TSval=775298392 TSecr=1721763714

Petición GET::

  GET / HTTP/1.1
  Host: neverssl.com
  User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:66.0) Gecko/20100101 Firefox/66.0
  Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
  Accept-Language: en-US,en;q=0.5
  Accept-Encoding: gzip, deflate
  DNT: 1
  Connection: keep-alive
  Upgrade-Insecure-Requests: 1

Headers de respuesta 200 OK, después vino el HTML comprimido en GZIP.

::

  HTTP/1.1 200 OK
  Content-Type: text/html
  Transfer-Encoding: chunked
  Connection: keep-alive
  Last-Modified: Thu, 14 Jun 2018 00:16:40 GMT
  Server: AmazonS3
  Content-Encoding: gzip
  Date: Mon, 06 May 2019 15:27:39 GMT
  Vary: Accept-Encoding
  Age: 37060
  X-Cache: Hit from cloudfront
  Via: 1.1 eb0748e1a1029699052c373701cab0cc.cloudfront.net (CloudFront)
  X-Amz-Cf-Id: _kvvdoHRj-r7lhjgq-UdFYtYDxEIv6gkK0Qc1x2yEm8XG7j0OuLEUg==

Para la request del favicon pasó algo similar.

APIs
~~~~

A partir de ahora uso curl porque permite ver los headers y hacer requests
específicas, como lo que viene va sobre TLS no lo puedo sniffear por Wireshark.

Algo común es acceder a lo que se llaman web APIs. Uno hace una request GET y
obtiene información en un formato legible por máquinas como JSON. Por ejemplo al
usar el comando::

  curl -v https://api.github.com/repos/martinber/arquitectura_de_redes/languages

Se hace una petición GET a GitHub solicitando la lista de lenguajes de
programación presentes en este repositorio, la opción ``-v`` muestra información
como el contenido de la request::

  GET /repos/martinber/arquitectura_de_redes/languages HTTP/1.1
  Host: api.github.com
  User-Agent: curl/7.64.0
  Accept: */*

La respuesta es::

  HTTP/1.1 200 OK
  Server: GitHub.com
  Date: Tue, 07 May 2019 02:09:09 GMT
  Content-Type: application/json; charset=utf-8
  Content-Length: 118
  Status: 200 OK
  X-RateLimit-Limit: 60
  X-RateLimit-Remaining: 54
  X-RateLimit-Reset: 1557198090
  Cache-Control: public, max-age=60, s-maxage=60
  Vary: Accept
  ETag: "b45e78b5e786c0695807dd4e59209ac5"
  Last-Modified: Mon, 06 May 2019 11:54:25 GMT
  X-GitHub-Media-Type: github.v3; format=json
  Access-Control-Expose-Headers: ETag, Link, Location, Retry-After, X-GitHub-OTP, X-RateLimit-Limit, X-RateLimit-Remaining, X-RateLimit-Reset, X-OAuth-Scopes, X-Accepted-OAuth-Scopes, X-Poll-Interval, X-GitHub-Media-Type
  Access-Control-Allow-Origin: *
  Strict-Transport-Security: max-age=31536000; includeSubdomains; preload
  X-Frame-Options: deny
  X-Content-Type-Options: nosniff
  X-XSS-Protection: 1; mode=block
  Referrer-Policy: origin-when-cross-origin, strict-origin-when-cross-origin
  Content-Security-Policy: default-src 'none'
  X-GitHub-Request-Id: A89A:531E:13B69A9:2AB299D:5CD0E8C5

  {
    "HTML": 823436,
    "JavaScript": 199061,
    "CSS": 15111,
    "Python": 5467,
    "Batchfile": 827,
    "Makefile": 620
  }

Con un PUT se puede por ejemplo poner estrella a un repositorio, en este caso la
API pide un header y una autenticación::

  curl -v -X PUT -H "Content-Length: 0" -u "martinber" https://api.github.com/user/starred/martinber/arquitectura_de_redes

Tener en cuenta que la contraseña va en BASE64 e insegura si no fuera porque
todo va sobre TLS. Otras peticiones PUT podrían llevar datos pero esta justo no
lleva nada::

  PUT /user/starred/martinber/arquitectura_de_redes HTTP/1.1
  Host: api.github.com
  Authorization: Basic bWFydGluYmVyOmVzdGFub2VzbWljb250cmFzZcOxYQ==
  User-Agent: curl/7.64.0
  Accept: */*
  Content-Length: 0

La respuesta es::

  HTTP/1.1 204 No Content
  Server: GitHub.com
  Date: Tue, 07 May 2019 02:23:39 GMT
  Content-Type: application/octet-stream
  Status: 204 No Content
  X-RateLimit-Limit: 5000
  X-RateLimit-Remaining: 4996
  X-RateLimit-Reset: 1557199419
  X-GitHub-Media-Type: github.v3; format=json
  Access-Control-Expose-Headers: ETag, Link, Location, Retry-After, X-GitHub-OTP, X-RateLimit-Limit, X-RateLimit-Remaining, X-RateLimit-Reset, X-OAuth-Scopes, X-Accepted-OAuth-Scopes, X-Poll-Interval, X-GitHub-Media-Type
  Access-Control-Allow-Origin: *
  Strict-Transport-Security: max-age=31536000; includeSubdomains; preload
  X-Frame-Options: deny
  X-Content-Type-Options: nosniff
  X-XSS-Protection: 1; mode=block
  Referrer-Policy: origin-when-cross-origin, strict-origin-when-cross-origin
  Content-Security-Policy: default-src 'none'
  X-GitHub-Request-Id: A9AA:2BDC:584874:F00459:5CD0EC2B

En este caso si se usa DELETE en vez de PUT hacia la misma URL, se elimina la
estrella.

Referencias
~~~~~~~~~~~

- https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html
