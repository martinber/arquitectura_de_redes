QOS
===

.. todo:: Hacer. Ver http://web.opalsoft.net/qos/default.php?p=ds-21

QoS hace referencia al uso de tecnologías que permiten administrar datos y
paquetes para reducir la pérdida de paquetes, latencia y jitter en la red, como
así también para manejar ancho de banda.
Debido a esto es necesario que los routers deben priorizar tráficos sobre otros,
aplicando mecanismos de control del ancho de banda que limiten la tasa de datos
ya sea mediante direcciones IP, protocolos, puertos, entre otros.

Existen dos tipos de aplicaciones, que se diferencian según sus requisitos:

- Elásticas: son flexibles en requerimientos de ancho de banda y se adaptan a
a las condiciones de la red, por ejemplo TCP, mails, aplicaciones WEB,
transferencia de archivos.

- Inelásticas: no son tan flexibles, ya que necesitan de que se cumplan ciertos
parámetros mínimos de ancho de banda para que puedan funcionar correctamente,
por ejemplo VOIP, videoconferencia.

Se define a un fujo como una secuencia de datagramas o segmentos que son el
resultado de una acción del usuario y requiere la misma QoS. Es unidireccional
y la entidad mínima a la cual un router puede aplicarle QoS. Se identifica por
cinco parámetros puerto e IP de origen y destino y el protocolo (TCP o UDP).

- QoS (Quality of Service): se refiere a la priorización y clasificación del
  tráfico en general, puede ser mediante técnicas de policing, shaping, entre otras.

- CoS (Class of Service): es un campo en el header de Ethernet, mas precisamente
  en la parte de VLAN, en donde se puede diferencia los servicios para luego
  darles prioridad

- ToS (Type of Service): segundo byte en el header de IP donde se puede
  especificar algun tipo de prioridad. En la actualidad se utiliza DSCP
  (Differenciated Services Code Point) que son 5 bits de ese byte reservado.

IP QOS peermite calidad de servicio extremo a extremo, de capa 2 como VLAN no

La cola es punteros a memoria. Un buffer es la memoria.


- Es lo mismo QoS en IPv4 que en IPv6 basicamente, cambian un poco los headers
  nomas

- Solamente se puede controlar lo que sale para el tema de prioridades. El
  marcado es a la entrada.

Arquitectura
------------

- Grandes grupos: mecansimos de control de datos o control de caminos.

- Clasificación:

  - Por flujo: Secuencia de datagramas (IP origen y destino, puerto origen y
    destino, protocolo). Cada conexion TCP tiene tiene 2 flujos.

  - Stream.

  - Clase trafico.

  - Campo de IP.

  - MPLS.

  - Capa 2.

  - Inspección profunda.

- Policing: Su función es asegurar que un tráfico no supere una tasa máxima determinada,
  controlando el ancho de banda y descartando aquellos paquetes que hagan que se
  supere esta tasa.

- Shaping: También asegura que un tráfico no supere una tasa determinada pero a diferencia
  del policer, en vez de descartar paquetes que hagan exceder la tasa, los retrasa
  suavizando la transferencia.

- Marking: Es el marcado de paquetes mediante una etiqueta que le permite a los
  routers, mediante un acuerdo previo, para darle un tratamiento determinado. No
  hay reserva de recursos por flujo, ni protocolo de señalización, ni información
  de estado en los routers.. Para marcar usamos la tabla mangle de iptables.

- Token Bucket
  Mecanismo para limitar la tasa media de transferencia, permitiendo ráfagas hasta
  un tamaño máximo. Se puede dividir en:
  - CIR: Commited Information Rate - Tasa de Caudal Comprometido
  - CBS: Commited Burst Size – Tamaño de Ráfaga Comprometido
  - T: Time Interval

.. todo:: Random Early Detection, RED, WRED.



TOS
~~~

En IPv4:
- Type of Service (8 bits): Para hacer calidad de servicios.
    - Precedencia (3 bits): prioridad, con ocho niveles en total. Mayor tiene más
    prioridad
    - D (1 bit): delay (retardo mínimo)
    - T (1 bit): throughput (máximo rendimiento)
    - R (1 bit): reliability (máxima fiabilidad)
    - C (1 bit): cost (mínimo costo)
    - X (1 bit): bit reservado

DSCP
~~~~

Luego de ToS se redefinió el byte utilizado en ToS, dando origen a DSCP, tanto en IPV4
como en IPV6. Este nuevo campo consiste:

- Differenciated Service Code Point o DSCP (6 bits): indica el tratamiento
que debe recibir el paquete

- Currently unused (2 bits): actualmente se lo utiliza para control de congestion

Marca los paquetes con una etiqueta y acuerda con los routers un tratamiento
específico, sin reserva de recursos por flujo, ni protocolo de señalización, ni
información de estado de routers. Las garantías de calidad de servicio no son
tan estrictas como en IntServ, pero suelen ser suficientes.

Se denomina al comportamiento de reenvío asignado PHB (per hop behaviour). PHB
determina la procedencia del paquete marcado en relación con otro tráfico del
sistema con DiffServ, y luego decide si el sistema reenvía o descarta dicho
paquete. Cada router con DiffServ aplica el mismo PHB al paquete.
Cada PHB consta de dos componentes:
- Una definición formal de comportamiento requerido
- Un sistema de marca recomendado para clasificar los paquetes

De los 6 bits utilizados en este campo para clasificar tráfico, se encuentran
distintas clases:

- 111xxx: control de la red, precedencia 7
- 110xxx: control de la red, precedencia 6
- 101xxx: Expedited Forwarding, precedencia 5
- 100xxx: Assured Forwarding clase 4, precedencia 4
- 011xxx: Assured Forwarding clase 3, precedencia 3
- 010xxx: Assured Forwarding clase 2, precedencia 2
- 001xxx: Assured Forwarding clase 1, precedencia 1
- 000xxx: Best Effort, precedencia 0

Expedited Forwarding garantiza caudal, tasa de pérdida, jitter y retardo,
equivale a una línea dedicada. Es como un acuerdo de SLA
Assured Forwarding asegura un trato preferente pero sin dar garantías. Tiene 4
clases y en cada una hay tres probabilidades de descarte (alta, media y baja).
Best Effort no tiene garantías.

- PHB: Per Hop Behavior. Lo que hace cada router. Los routers en conjunto son un
  dominio DiffServ.

IntServ
-------

Se basa en la reserva previa de recursos en todo el trayecto. Para esto utiliza
el protocolo RSVP (Resource reSerVation Protocol), que garantiza la QoS
solicitada. De no haber recursos disponibles, se rechaza la petición, ejerciendo
control de admisión o CAC (Connection Admission Control)

IntServ se desarrolló antes que DiffServ, pero lo mas utilizado es este último.
Esto se debe a la capacidad de escalabilidad que tiene a diferencia de IntServ
que tiene un gran consumo de recursos.

SLA
---

Service Level Agreement.

- Contrato entre usuario y operador de red.

- ITU-T E.860 define el marco? una estructura generica.

- Parametros posibles:

  - Disponibilidad. por porcentaje, cuantas hhoras por dia, cuantas horas
    seguidas, etc.

  - Ancho de banda.

  - Perdida de paquetes, por porcentaje. 1 de cada 100 vs 10 de cada 1000.

  - RTT.

  - Jitter.

  - Etc.

- La IPTF estableció la IPPM que hace estandares de medicion.


Netflow
-------

- Netflow
- ELK: Elastic search, ???, Kibana?

Control de tráfico en Linux
---------------------------

Desde ``man tc``::

  Qdiscs

    qdisc is short for 'queueing discipline' and it is elementary to
    understanding traffic control. Whenever the kernel needs to send a packet to
    an interface, it is enqueued to the qdisc configured for that interface.
    Immediately afterwards, the kernel tries to get as many packets as possible
    from the qdisc, for giving them to the network adaptor driver.

    A simple QDISC is the 'pfifo' one, which does no processing at all and is a
    pure First In, First Out queue. It does however store traffic when the
    network interface can't handle it momentarily.

  Classes

    Some qdiscs can contain classes, which contain further qdiscs - traffic may
    then be enqueued in any of the inner qdiscs, which are within the classes.
    When the kernel tries to dequeue a packet from such a classful qdisc it can
    come from any of the classes. A qdisc may for example prioritize certain
    kinds of traffic by trying to dequeue from certain classes before others.

  Filters

    A filter is used by a classful qdisc to determine in which class a packet
    will be enqueued. Whenever traffic arrives at a class with subclasses, it
    needs to be classified. Various methods may be employed to do so, one of
    these are the filters. All filters attached to the class are called, until
    one of them returns with a verdict. If no verdict was made, other criteria
    may be available. This differs per qdisc.

    It is important to notice that filters reside within qdiscs - they are not
    masters of what happens.

qdisc
~~~~~

Sacado de `acá <https://www.tldp.org/HOWTO/Traffic-Control-HOWTO>`_:

  Simply put, a qdisc is a scheduler (Section 3.2). Other qdiscs available under
  Linux will rearrange the packets entering the scheduler's queue in accordance
  with that scheduler's rules.

  The qdisc is the major building block on which all of Linux traffic control is
  built, and is also called a queuing discipline.

  The classful qdiscs can contain classes, and provide a handle to which to attach
  filters. There is no prohibition on using a classful qdisc without child
  classes, although this will usually consume cycles and other system resources
  for no benefit.

  The classless qdiscs can contain no classes, nor is it possible to attach filter
  to a classless qdisc. Because a classless qdisc contains no children of any
  kind, there is no utility to classifying. This means that no filter can be
  attached to a classless qdisc.

  A source of terminology confusion is the usage of the terms root qdisc and
  ingress qdisc. These are not really queuing disciplines, but rather locations
  onto which traffic control structures can be attached for egress (outbound
  traffic) and ingress (inbound traffic).

  Each interface contains both. The primary and more common is the egress qdisc,
  known as the root qdisc. It can contain any of the queuing disciplines (qdiscs)
  with potential classes and class structures. The overwhelming majority of
  documentation applies to the root qdisc and its children. Traffic transmitted on
  an interface traverses the egress or root qdisc.

  For traffic accepted on an interface, the ingress qdisc is traversed. With its
  limited utility, it allows no child class to be created, and only exists as an
  object onto which a filter can be attached. For practical purposes, the ingress
  qdisc is merely a convenient object onto which to attach a policer to limit the
  amount of traffic accepted on a network interface.

  In short, you can do much more with an egress qdisc because it contains a real
  qdisc and the full power of the traffic control system. An ingress qdisc can
  only support a policer. The remainder of the documentation will concern itself
  with traffic control structures attached to the root qdisc unless otherwise
  specified

class
~~~~~

Sacado de `acá <https://www.tldp.org/HOWTO/Traffic-Control-HOWTO>`_:

  Classes only exist inside a classful qdisc (e.g., HTB and CBQ). Classes are
  immensely flexible and can always contain either multiple children classes or a
  single child qdisc. There is no prohibition against a class containing a
  classful qdisc itself, which facilitates tremendously complex traffic control
  scenarios.

  Any class can also have an arbitrary number of filters attached to it, which
  allows the selection of a child class or the use of a filter to reclassify or
  drop traffic entering a particular class.

  A leaf class is a terminal class in a qdisc. It contains a qdisc (default FIFO)
  and will never contain a child class. Any class which contains a child class is
  an inner class (or root class) and not a leaf class.


Colas
-----

CIR
~~~

.. todo:: Es un buffer cicular? Supuestamente es policer?

EBS
~~~

.. todo:: No la encuentro?? Supuestamente es policer?

T
~~

.. todo:: No la encuentro?? Supuestamente es policer?

pfifo
~~~~~

Classless.

FIFO significa que el primer paquete que entra es el primero en salir.

Ver ``man tc-pfifo``::

  They are the simplest queues possible and therefore have no overhead. pfifo
  constrains the queue size as measured in packets. bfifo does so as measured
  in bytes.

pfifo_fast
~~~~~~~~~~

Classless.

Es la cola por defecto en Linux y Mikrotik. Es classless pero contienen tres
colas en el caso que se quiera dar prioridades.

Ver ``man tc-pfifo_fast``::

  pfifo_fast is the default qdisc of each interface.

  The algorithm is very similar to that of the classful tc-prio(8) qdisc.
  pfifo_fast is like three tc-pfifo(8) queues side by side, where packets can
  be enqueued in any of the three bands based on their Type of Service bits or
  assigned priority.

  Not all three bands are dequeued simultaneously - as long as lower bands have
  traffic, higher bands are never dequeued. This can be used to prioritize
  interactive traffic or penalize 'lowest cost' traffic.

  Each band can be txqueuelen packets long, as configured with ifconfig(8) or
  ip(8). Additional packets coming in are not enqueued but are instead dropped.

  See tc-prio(8) for complete details on how TOS bits are translated into bands.

CBS
~~~

Classless.

De shaping.

Creo que nunca la usamos. Ver ``man tc-cbs``::

  The CBS (Credit Based Shaper) qdisc implements the shaping algorithm defined
  by the IEEE 802.1Q-2014 Section 8.6.8.2, which applies a well defined rate
  limiting method to the traffic.

  This queueing discipline is intended to be used by TSN (Time Sensitive
  Networking) applications, the CBS parameters are derived directly by what is
  described by the Annex L of the IEEE 802.1Q-2014 Specification. The algorithm
  and how it affects the latency are detailed there.

  CBS is meant to be installed under another qdisc that maps packet flows to
  traffic classes, one example is mqprio(8).

Tocken Bucket Filter
~~~~~~~~~~~~~~~~~~~~

Classless.

Ver ``man tc-tbf``::

  The Token Bucket Filter is a classful queueing discipline available for
  traffic control with the tc(8) command.

  As the name implies, traffic is filtered based on the expenditure of tokens.
  Tokens roughly correspond to bytes, with the additional constraint that each
  packet consumes some tokens, no matter how small it is. This reflects the fact
  that even a zero-sized packet occupies the link for some time.

::

  tc qdisc add dev eth0 root tbf rate 220kbit latency 50ms burst 1540

SFQ
~~~

Classless.

Como la FIFO con bandas.

Ver ``man tc-sfq``::

  Stochastic Fairness Queueing is a classless queueing discipline available for
  traffic control with the tc(8) command.

  SFQ does not shape traffic but only schedules the transmission of packets,
  based on 'flows'. The goal is to ensure fairness so that each flow is able to
  send data in turn, thus preventing any single flow from drowning out the rest.

  This may in fact have some effect in mitigating a Denial of Service attempt.

Ver el `manual de Mikrotik <https://wiki.mikrotik.com/wiki/Manual:Queue#SFQ>`_:

  Stochastic Fairness Queuing (SFQ) is ensured by hashing and round-robin
  algorithms. A traffic flow may be uniquely identified by a 4
  options(src-address, dst-address, src-port and dst-port), so these parameters
  are used by SFQ hashing algorithm to classify packets into one of 1024
  possible sub-streams. Then round-robin algorithm will start to distribute
  available bandwidth to all sub-streams, on each round giving sfq-allot bytes
  of traffic. The whole SFQ queue can contain 128 packets and there are 1024
  sub-streams available.

  SFQ is called "Stochastic" because it does not really allocate a queue for
  each flow, it has an algorithm which divides traffic over a limited number of
  queues (1024) using a hashing algorithm.

Nosotros ponemos que se reconfigure cada 10 segundos::

  tc qudisc add dev ent0 root sfq perturb 10

PCQ
~~~

Classless.

Similar a SFQ pero solo para Mikrotik creo.

Ver el `manual de Mikrotik <https://wiki.mikrotik.com/wiki/Manual:Queue#PCQ>`_:

  Per Connection Queuing (PCQ) is a similar to SFQ, but it has additional features.

  It is possible to choose flow identifiers (from dst-address | dst-port |
  src-address | src-port). For example if you classify flows by src-address on
  local interface (interface with your clients), each PCQ sub-stream will be one
  particular client's upload.

  It is possible to assign speed limitation to sub-streams with pcq-rate option.
  If pcq-rate=0 sub-streams will divide available traffic equally.

  PCQ was introduced to optimize massive QoS systems, where most of the queues are
  exactly the same for different sub-streams. For example a sub-stream can be
  download or upload for one particular client (IP) or connection to server.

  PCQ algorithm is very simple - at first it uses selected classifiers to
  distinguish one sub-stream from another, then applies individual FIFO queue size
  and limitation on every sub-stream, then groups all sub-streams together and
  applies global queue size and limitation.

PRIO
~~~~

Classful.

Es como pfifo_fast que ordena paquetes en solamente 3 bandas.

Hay que tener cuidado de que la suma de las hijas no sea mayor a la de arriba,
porque en tal caso deja de andar y es como que no limita nada, pasa todo de
largo.

Ver ``man tc-prio``::

  The PRIO qdisc is a simple classful queueing discipline that contains an
  arbitrary number of classes of differing priority. The classes are dequeued in
  numerical descending order of priority. PRIO is a scheduler and never delays
  packets - it is a work-conserving qdisc, though the qdiscs contained in the
  classes may not be.

  Very useful for lowering latency when there is no need for slowing down traffic.

  On creation with 'tc qdisc add', a fixed number of bands is created. Each band
  is a class, although is not possible to add classes with 'tc qdisc add', the
  number of bands to be created must instead be specified on the command line
  attaching PRIO to its root.

  When dequeueing, band 0 is tried first and only if it did not deliver a packet
  does PRIO try band 1, and so onwards. Maximum reliability packets should
  therefore go to band 0, minimum delay to band 1 and the rest to band 2.

  As the PRIO qdisc itself will have minor number 0, band 0 is actually major:1,
  band 1 is major:2, etc. For major, substitute the major number assigned to the
  qdisc on 'tc qdisc add' with the handle parameter.

CBQ
~~~

Classful.

Permite prestar trafico a otra rama cuando no lo usa, creo que es el único que
deja hacer eso de las que vemos, pero hay otras variaciones. Muy dificil de
configurar, hay que hacer cuentas.

Es como la prio pero hace un round robin con pesos que se van calculando
dinamicamene que se yo.

Ver ``man tc-cbq``::

  Class Based Queueing is a classful qdisc that implements a rich linksharing
  hierarchy of classes. It contains shaping elements as well as prioritizing
  capabilities. Shaping is performed using link idle time calculations based on
  the timing of dequeue events and underlying link bandwidth.

HTB
~~~

Classful.

Token Bucket Filter con prioridades.

Ver ``man tc-htb``::

  HTB is meant as a more understandable and intuitive replacement for the CBQ
  qdisc in Linux. Both CBQ and HTB help you to control the use of the outbound
  bandwidth on a given link.

  Both allow you to use one physical link to simulate several slower links and to
  send different kinds of traffic on different simulated links. In both cases, you
  have to specify how to divide the physical link into simulated links and how to
  decide which simulated link to use for a given packet to be sent.

  Unlike CBQ, HTB shapes traffic based on the Token Bucket Filter algorithm which
  does not depend on interface characteristics and so does not need to know the
  underlying bandwidth of
  the outgoing interface.

Referencias
-----------

- https://www.tldp.org/HOWTO/Traffic-Control-HOWTO/components.html#c-qdisc
