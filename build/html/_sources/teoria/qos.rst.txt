QOS
===

.. todo:: Hacer.

- QOS: Lo que la red da
- COS: Lo que se requere de la red, es indistinto a QOS
- TOS: Un campo de IPv4 obsoleto

IP QOS peermite calidad de servicio extremo a extremo, de capa 2 como VLAN no


La cola es punteros a memoria. Un buffer es la memoria.


- Shaper elimina paquetes que se pasan del limite, el policer creo que los
  encola.

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

.. todo:: Ver bien a ambos

- Policing: Token bucket, segun el profe trunca arriba del limite y no permite
  rafagas. CIR, CBS, EBS, T. pero para mi si permite rafagas, lo que no permite
  es encolar paquetes. Seria un token bucket sin buffer para paquetes.

- Shaping: No permite rafagas, pero en vez de perderse se encolan. Token bucket
  con cola.

- Marking: Marcado de paquetes, en el origen o en el ingreso de la red. Hay
  varias formas de marcar.

.. todo:: Random Early Detection, RED, WRED.

TOS
~~~

En IPv4: 3 bit para precedencia, o sea 8 prioridades distintas. Despues 4 bits
DTRC. Nunca se usó.

DSCP
~~~~

Creo que es DiffServ tambien

El DS RFC 2474 le dio otro significado a ese campo. EN IPv6 le pusieron
significado al campo de clase y al primer pedazo del campo flow type o algo asi.
Se definio DSCP.

Primero 3 bits para 8 prioridades (coincide con el TOS de IPv4)
despues hay 2 bits para tener 4 prioridades de descarte. El sexto bit se pone en
cero.

Si los primeros 3 bits dan:

- 7: Control de la red
- 6: Control de la red
- 5: Expedited forwarding
- 4: Assured forwarding: Linea dedicada, garantiza todo
- 3: Assured forwarding
- 2: Assured forwarding
- 1: Assured forwarding
- 0: Best Effort

- Expedited forwarding: Linea dedicada, garantiza todo, como CBR en ATM
- Assured forwarding: Preferencial
- Best Effort: Sin garantías.

Despues para cada clase tenes tres probabilidades de descarte.

- PHB: Per Hop Behavior. Lo que hace cada router. Los routers en conjunto son un
  dominio DiffServ.

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

Colas
-----

- PCQ Mikrotik
- Pfifo

Para marcar usamos la tabla mangle de iptables.

FIFO
~~~~

??

Tocken Bucket Filter
~~~~~~~~~~~~~~~~~~~~

::

  tc qdisc add dev eth0 root tbf rate 220kbit latency 50ms burst 1540

SFQ
~~~

Como la FIFO con bandas

Dice que se reconfigure cada 10 segundos::

  tc qudisc add dev ent0 root sfq perturb 10

PRIO
~~~~

Es como pfifo_fast que ordena paquetes en solamente 3 bandas.

Hay que tener cuidado de que la suma de las hijas no sea mayor a la de arriba,
porque en tal caso deja de andar y es como que no limita nada, pasa todo de
largo.

CBQ
~~~

Permite prestar trafico a otra rama cuando no lo usa, creo que es el único que
deja hacer eso de las que vemos, pero hay otras variaciones. Muy dificil de
configurar, hay que hacer cuentas.

Es como la prio pero hace un round robin con pesos que se van calculando
dinamicamene que se yo.

HTB
~~~

Token Bucket Filter pero que permite hacer árboles.
