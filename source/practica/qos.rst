QOS
===

Netflow
ELK: Elastic search, ???, Kibana?

Mikrotik
========

Se hace el marcado de las conexiones, que funciona como un tunel, en donde los paquetes sin importar
el origen o el destino. Luego de marcar la conexión se marcan los paquetes que puede ser diferenciado
o no, por ejemplo se puede marcar los paquetes tcp con una etiqueta y los de udp con otra.
En Winbox ir a ``ip--->Firewall--->Mangle`` en el + se agrega la regla que quieras.
Para conexión se pone en Src. Address la ip o rango de ip desde donde viene y en ``Action`` se pone
``mark connection`` y abajo el nombre correspondiente. 

Luego se agrega para marcar los paquetes en la misma tabla mangle, pero no necesita de poner ninguna 
ip de origen o destino, sino que hay que ponerle en ``Connection Mark`` la marca de conexión que 
creaste antes. Luego en ``Action`` pones mark packet y su nombre, recordando de tener destildada la
opción de ``Passthrough`` para que cuando ejecute la acción de marcado de paquete salga de una y no
siga reccorriendo todas las reglas de la tabla. Lo óptimo es poner a continuación de la regla de 
marcado de conexión la de marcado de paquete.

Para hacer las colas ir a ``Queues--->Queue Tree`` agregar en el +. En Parent podes elegir si lo
hacés en una interfaz determinada o se le pone global si querés que se aplique en todas. En el caso
que hagas un arbol para la interfaz eth0, cuando se hace la de global aparte, no incluye a esta
interfaz. Se debe poner:

- Name: Nombre de la cola, por ejemplo Download
- Parent: donde se va a realizar la cola, puede ser la interfaz o si es una rama se pone por ej Download
- Packet Mark: la marca que tiene el paquete, se creó en la parte de mangle
- Queue Type: el tipo de cola, default es una pfifo.
- Priority: valor de prioridad, 1 es el más alto y 8 el más bajo
- Limit at: indica el ancho de banda asegurado
- Max limit: el ancho de banda máximo que puede utilizar, que proviene de cuando el resto de las colas
  no están usando la totalidad del ancho de banda otorgado
