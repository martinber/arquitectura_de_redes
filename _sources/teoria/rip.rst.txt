RIP
===

Routing Information Protocol.

.. todo:: Completar teoría.

Hay tres versiones de RIP: RIPv1, RIPv2 y RIPng. Las primeras dos son para IPv4,
la tercera para IPV6.

- Sólo mira la cantidad de saltos.

- Cantidad máxima de saltos es 16.

- Cantidad máxima de redes es 25.

- Va sobre UDP 520.

- Se envían las rutas cada 30 segundos.

- Las rutas vencen 180 segundos después de caerse, para prevenir oscilaciones.

- RIPv1 no admite VLSM y es inseguro, porque no se identifica al que envía el
  paquete y un atacante podría redirigir todo por donde quiera.

- RIPv2 envía las máscaras de red y por lo tanto soporta VLSM, también
  autentica los routers.

- RIPng es como RIPv2 pero para IPv6.

- Conviene para redes chicas que no cambian mucho.

- Usa el algoritmo de vector distancia Bellman-Ford.

- RIPv1 anuncia por broadcast, RIPv2 anuncia por multicast.

Bellman-Ford
------------

Es el algoritmo que usa RIP para determinar el mejor camino.

- Cada router tiene una tabla con las mejores distancias conocidas y los
  enlaces a utilizar para cada destino.

- Se intercambian las tablas de ruteo con los vecinos cada cierto tiempo.

- Una vez que el router tiene toda la información actualiza su tabla e informa
  a los vecinos de los cambios.

- A diferencia del algoritmo de Dijkstra:

  - Se intercambian tablas de ruteo en lugar de estados de enlace.

  - Se envía la información a sólo los vecinos en lugar de enviar a todos los
    routers.

  - La convergencia es más lenta, hay mayor carga en la red pero se necesita
    una capacidad de procesamiento menor.

RIPng
-----

Captura
~~~~~~~

En el GNS3 puse varios Mikrotik y varios Cisco. Después capturé los paquetes RIP
en una de las conexiones.

.. image:: ./captura_rip.png

En la foto se pueden ver los Cisco (routers azules), Mikrotik (routers azules y
rojos) y el punto de captura (la lupa). En la captura se ven los paquetes RIP
que se envían entre el Mikrotik encerrado con rojo (R3) y el Cisco encerrado en
azul (R1), estos paquetes llevan las distancias entre el router que envía el
paquete y una red determinada.

Los números en rojo son las distancias que informa el Mikrotik y los números en
azul las distancias que informa el Cisco.

Paquete de Mikrotik a Cisco::

  Frame 320: 266 bytes on wire (2128 bits), 266 bytes captured (2128 bits) on interface 0
  Ethernet II, Src: 0c:45:23:ee:a3:00 (0c:45:23:ee:a3:00), Dst: IPv6mcast_09 (33:33:00:00:00:09)
  Internet Protocol Version 6, Src: fe80::e45:23ff:feee:a300, Dst: ff02::9
  User Datagram Protocol, Src Port: 521, Dst Port: 521
  RIPng
    Command: Response (2)
    Version: 1
    Reserved: 0000
    Route Table Entry: IPv6 Prefix: 2001:1::/64 Metric: 16
    Route Table Entry: IPv6 Prefix: 2001:2::/64 Metric: 2
    Route Table Entry: IPv6 Prefix: 2001:3::/64 Metric: 1
    Route Table Entry: IPv6 Prefix: 2001:4::/64 Metric: 2
    Route Table Entry: IPv6 Prefix: 2001:5::/64 Metric: 3
    Route Table Entry: IPv6 Prefix: 2001:a::/64 Metric: 16
    Route Table Entry: IPv6 Prefix: 2001:b::/64 Metric: 16
    Route Table Entry: IPv6 Prefix: 2001:c::/64 Metric: 1
    Route Table Entry: IPv6 Prefix: 2001:d::/64 Metric: 1
    Route Table Entry: IPv6 Prefix: 2001:e::/64 Metric: 2

Paquete de Cisco a Mikrotik::

  Frame 317: 186 bytes on wire (1488 bits), 186 bytes captured (1488 bits) on interface 0
  Ethernet II, Src: c4:01:09:68:00:10 (c4:01:09:68:00:10), Dst: IPv6mcast_09 (33:33:00:00:00:09)
  Internet Protocol Version 6, Src: fe80::c601:9ff:fe68:10, Dst: ff02::9
  User Datagram Protocol, Src Port: 521, Dst Port: 521
  RIPng
    Command: Response (2)
    Version: 1
    Reserved: 0000
    Route Table Entry: IPv6 Prefix: 2001:1::/64 Metric: 1
    Route Table Entry: IPv6 Prefix: 2001:a::/64 Metric: 1
    Route Table Entry: IPv6 Prefix: 2001:b::/64 Metric: 1
    Route Table Entry: IPv6 Prefix: 2001:e::/64 Metric: 2
    Route Table Entry: IPv6 Prefix: 2001:2::/64 Metric: 2
    Route Table Entry: IPv6 Prefix: 2001:5::/64 Metric: 3

Yo creo que el Mikrotik cuando informa las métricas pone la cantidad de saltos.
Pero cuando sabe de el Cisco tiene una ruta más corta que a través de él,
informa 16 que significa inalcanzable. El Cisco hace lo mismo pero en vez de
poner métrica 16 directamente no informa de la ruta.

Al final la ruta del Cisco quedó::

  IPv6 Routing Table - 15 entries
  Codes: C - Connected, L - Local, S - Static, R - RIP, B - BGP
         U - Per-user Static route
         I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea, IS - ISIS summary
         O - OSPF intra, OI - OSPF inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
         ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2

  C   2001:1::/64 [0/0]
       via ::, FastEthernet0/0
  L   2001:1::1/128 [0/0]
       via ::, FastEthernet0/0
  R   2001:2::/64 [120/2]
       via FE80::C602:AFF:FE0D:1, FastEthernet0/1
  R   2001:3::/64 [120/2]
       via FE80::E45:23FF:FEEE:A300, FastEthernet1/0
  R   2001:4::/64 [120/3]
       via FE80::E45:23FF:FEEE:A300, FastEthernet1/0
  R   2001:5::/64 [120/3]
       via FE80::C602:AFF:FE0D:1, FastEthernet0/1
  C   2001:A::/64 [0/0]
       via ::, FastEthernet0/1
  L   2001:A::1/128 [0/0]
       via ::, FastEthernet0/1
  C   2001:B::/64 [0/0]
       via ::, FastEthernet1/0
  L   2001:B::1/128 [0/0]
       via ::, FastEthernet1/0
  R   2001:C::/64 [120/2]
       via FE80::C602:AFF:FE0D:1, FastEthernet0/1
       via FE80::E45:23FF:FEEE:A300, FastEthernet1/0
  R   2001:D::/64 [120/2]
       via FE80::E45:23FF:FEEE:A300, FastEthernet1/0
  R   2001:E::/64 [120/2]
       via FE80::C602:AFF:FE0D:1, FastEthernet0/1
  L   FE80::/10 [0/0]
       via ::, Null0
  L   FF00::/8 [0/0]
       via ::, Null0

Y la del Mikrotik::

  Flags: X - disabled, A - active, D - dynamic, C - connect, S - static,
  r - rip, o - ospf, b - bgp, U - unreachable

   #      DST-ADDRESS    GATEWAY                            DISTANCE
   0 ADr  2001:1::/64    fe80::c601:9ff:fe68:1...  {R1}          120
   1 ADr  2001:2::/64    fe80::c602:aff:fe0d:2...  {R2}          120
   2 ADC  2001:3::/64    ether4                                    0
   3 ADr  2001:4::/64    fe80::e45:23ff:fee7:3...  {R4}          120
   4 ADr  2001:5::/64    fe80::c602:aff:fe0d:2...  {R2}          120
   5 ADr  2001:a::/64    fe80::c601:9ff:fe68:1...  {R1}          120
   6 ADC  2001:b::/64    ether1                                    0
   7 ADC  2001:c::/64    ether2                                    0
   8 ADC  2001:d::/64    ether3                                    0
   9 ADr  2001:e::/64    fe80::c602:aff:fe0d:2...  {R2}          120

Revisé mirando el dibujo y todas las rutas están bien.

Algo raro es que en el caso del Cisco, para llegar a la red ``2001:c::/64``, es
lo mismo usar como gateway al R2 que al R3, por lo tanto en la ruta están ambos
gateway. El Mikrotik para llegar a la ``2001:a::/64`` también tiene dos
opciones, R1 y R2, pero solamente tiene como gateway a R2.
