HTTP
====

Hypertext Transfer Protocol.

.. todo:: Hacer, ver que onda con los MIME

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

Requests
--------

.. todo:: Ver si es lo mismo que HTTP methods.

- GET: Solicita un recurso determinado.

- HEAD: Igual que HEAD, pero solicita sólo el encabezado, dejando atrás el
  cuerpo de la respuesta.

- POST: Solicita al servidor ubicar los datos presentes en esta solicitud en una
  determinada ubicación, ya sea modificando o creando un recurso nuevo.

.. todo:: Ver diferencia POST y PUT.

- PUT:

- DELETE: Solicita el borrado de un determinado recurso.

- TRACE: Solicita al servidor que responda con todos los datos que recibe, una
  especie de *echo*. Se usa para observar si servidores intermedios hacen
  modificaciones en la petición.

- OPTIONS: Solicita que el servidor responda con los métodos HTTP que soporta en
  determinada URL.

.. todo:: CONNECT, no entend, ver si es importante.

- CONNECT:

- PATCH: Produce determinadas modificaciones en un recurso.

- Entre otros...

Algunas solicitudes como GET, HEAD, OPTIONS y TRACE son métodos seguros, ya que
no realizan modificaciones en el servidor, a diferencia de por ejemplo PUT,
POST, DELETE, etc.

Códigos de estado
-----------------

.. todo:: Agregar mas.

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
  Proxy* para bomprar a los servidores proxy que interceptan las comunicaciones
  entre el cliente y el servidor web, por lo tanto no es necesaria la
  configuración de los clientes. Se necesita habilitar el forwarding IP en el
  proxy.

- Accelerator proxy: Se coloca en frente de uno o unos pocos servidores web y se
  apunta al DNS para que las consultas de los clientes vayan al proxy. De esta
  forma el proxy permite mejorar el rendimiento de un servidor web lento gracias
  a los cachés.

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

.. todo:: Leer un poco más sobre CAs.

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
