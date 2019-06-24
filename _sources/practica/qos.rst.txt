QOS
===

Netflow
ELK: Elastic search, ???, Kibana?

Mikrotik
========

Se hace el marcado de las conexiones, que funciona como un tunel, en donde los
paquetes sin importar el origen o el destino. Luego de marcar la conexión se
marcan los paquetes que puede ser diferenciado o no, por ejemplo se puede marcar
los paquetes tcp con una etiqueta y los de udp con otra. En Winbox ir a
``ip--->Firewall--->Mangle`` en el + se agrega la regla que quieras. Para
conexión se pone en Src. Address la ip o rango de ip desde donde viene y en
``Action`` se pone ``mark connection`` y abajo el nombre correspondiente.

Luego se agrega para marcar los paquetes en la misma tabla mangle, pero no
necesita de poner ninguna ip de origen o destino, sino que hay que ponerle en
``Connection Mark`` la marca de conexión que creaste antes. Luego en ``Action``
pones mark packet y su nombre, recordando de tener destildada la opción de
``Passthrough`` para que cuando ejecute la acción de marcado de paquete salga de
una y no siga reccorriendo todas las reglas de la tabla. Lo óptimo es poner a
continuación de la regla de marcado de conexión la de marcado de paquete.

Para hacer las colas ir a ``Queues--->Queue Tree`` agregar en el +. En Parent
podes elegir si lo hacés en una interfaz determinada o se le pone global si
querés que se aplique en todas. En el caso que hagas un arbol para la interfaz
eth0, cuando se hace la de global aparte, no incluye a esta interfaz. Se debe
poner:

- Name: Nombre de la cola, por ejemplo Download.

- Parent: donde se va a realizar la cola, puede ser la interfaz o si es una rama
  se pone por ej Download.

- Packet Mark: la marca que tiene el paquete, se creó en la parte de mangle.

- Queue Type: el tipo de cola, default es una pfifo.

- Priority: valor de prioridad, 1 es el más alto y 8 el más bajo.

- Limit at: indica el ancho de banda asegurado.

- Max limit: el ancho de banda máximo que puede utilizar, que proviene de cuando
  el resto de las colas no están usando la totalidad del ancho de banda
  otorgado.

Linux
-----

``tc -s qdisc ls dev xxx``: Monitorea las colas asociadas a una interfaz.

Existen dos tipos de  disciplinas de colas:

Sin clase
~~~~~~~~~

PFIFO
`````

``tc qdisc add dev xxx root pfifo``

Token Bucket Filter (TBF)
`````````````````````````

``tc qdisc add dev eth0 root tbf rate xkbit latency xms burst 1540``

Donde:

- rate: Velocidad de la interfaz
- latency: Período máximo de tiempo que puede pasar un paquete en el tbf
- burst: Tamaño del bucket en bytes

Stochastic Fairness Queueing (SFQ)
``````````````````````````````````

``tc qdisc add dev xxx root sfq perturb 10``

``Perturb X`` reconfigura la cola cada X segundos y es opcional.

Con clase
~~~~~~~~~

- A diferencia de las sin clases, tienen nivel de profundidad, se agrupan
  distintos tipos de paquetes (clases) para luego darles prioridades.
- Para identificar clases se usa la notación **major:minor**, donde major hace
  referencia a la raíz de una clase y minor se refiere a una que desciende de
  root.
- Se necesitan usar filtro para tratar los paquetes de distintas clases.

PRIO
````

``tc qdisc add dev xxx root handle 1: prio``

Crea por defecto clases 1:1 1:2 y 1:3

::

  tc qdisc add dev xxx parent 1:1 handle 10: sfq
  tc qdisc add dev xxx parent 1:2 handle 20: tbf rate xkbit buffer y limit z
  tc qdisc add dev xxx parent 1:3 handle 30: pfifo

CBQ
```

Es la más complicada, justamente por eso no se usa.

::

  tc qdisc add dev xxx root handle 1:0 cbq bandwidth xMbit avpkt 1000 cell 8
  tc class add dev xxx parent 1:0 classid 1:1 cbq bandwidth xMbit rate yMbit
  weight zMbit prio 8 allot 1514 cell 8 maxburst 20 avpkt 1000 bounded

HTB
```

Funciona igual que CBQ pero es mucho menos complicado. Tiene los mismos
parámetros que TBF.

Marcado de paquetes
~~~~~~~~~~~~~~~~~~~

- fw: Clasifica el tráfico basándose en marcas realizadas a los paquetes.
  ``tc filter add dev xxx protocol ip parent 1:0 prio 1 handle X fw flowid 1:Y``:
  Adhiere un filtro a la interfaz xxx para el protocolo ip, donde la disciplina
  de cola (qdisc) padre es la 1:0. Se especifica que se usa el filtro fw y que
  los paquetes marcados con X los envíe a la cola Y.
- u32: Análisis de los campos de la cabecera de los protocolos.
  ``tc filter add dev xxx protocol ip parent 1:0 prio 1 u32 match ip dport 80
  0xffff flowid 1:X``: Adhiere un filtro a la interfaz xxx para el protocolo ip,
  donde la qdisc padre es la 1:0. Se usa u32 y se marca que los paquetes que
  tengan el puerto 80 como destino deben ser enviados a la cola X.

Ejemplo hecho en clase
~~~~~~~~~~~~~~~~~~~~~~

- Primero creamos los filtros asociados a una interfaz, protocolo, clase, y le
  decimos la marca que van a contener los paquetes (handle #). Los paquetes con
  este numero van a ser redirigidos al flowid asignado.

::

  tc filter add dev enp2s0 protocol ip parent 1:0 prio 1 handle 1010 (0x3f2) fw flowid 1:10
  tc filter add... handle 1020 fw flowid 1:20

- Luego con iptables mangle marcamos los paquetes por IP de destino con el
  numero que le asignamos a "handle" en el comando anterior.

::

  iptables -t mangle -A  FORWARD -d [IPDest] -j MARK --set-mark 1010 (0x3f2)
  iptables -t mangle... --set-mark 1020

- Luego creamos el arbol de colas con clases, sirve ayudarse en este paso con
  el programa tcviz para graficar la arquitectura de la cola asociada a la
  interfaz.

::

  tc qdisc add dev enp2s0 parent root handle 1: htb default 1:10
  tc class add dev enp2s0 parent 1: classid 1:10 htb rate 7Mbit
  tc class add dev enp2s0 parent 1: classid 1:20 htb rate 3Mbit
  tc qdisc add dev enp2s0 parent 1:10 sfq perturb 10

Finalmente podemos generar una imagen con tcviz para ver la estructura final:

``./tcviz.py enp2s0 | dot -Tpng > tc.png``

NETEM
~~~~~

Para el uso de netem en linux hay varios casos:

::

  tc qdisc add dev eth0 root netem delay 100ms
  tc qdisc add dev eth0 root netem delay 100ms 50ms
  tc qdisc add dev eth0 root netem loss 0.1%
  tc qdisc add dev eth0 root netem duplicate 1%
  tc qdisc add dev eth0 root netem corrupt 0.1%

Donde:

- delay X: Agrega solamente retardo
- delay X Y: Agrega X de retardo e Y de jitter
- loss X%: Porcentaje de périda de paquetes
- duplicate X%: Porcentaje de paquetes duplicados
- corrupt X%: Corrompe los paquetes agregándoles bits erróneos

Recordar que en vez de ``add`` se debe usar ``change`` para modificar, o
``delete`` para borrar reglas.
