HTTP
====

Hypertext Transfer Protocol.

.. todo:: Hacer

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

Proxy HTTP
----------

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
