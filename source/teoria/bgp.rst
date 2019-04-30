BGP
===

Border Gateway Protocol.

- Es el único protocolo de ruteo EGP.

- Actualmente se utiliza la versión 4.

- Usa un algoritmo de vector distancia modificado.

- EBGP por defecto tiene TTL de uno, pero IBGP está diseñado para ser ruteado
  por un IGP.

- Los vecinos se llaman peers.

.. todo:: - Usa UDP para algunas cosas y TCP para otras

- Cada router tiene un ID de 24 bits, por defecto es la IP de una interfaz de
  loopback. De lo contrario se usa una IP de alguna interfaz pero puede traer
  problemas si esa interfaz se cae.

- Los ID de router deben ser únicos dentro del sistema autónomo.

- Para evitar bucles descarta las rutas que pasan por él mismo.

- Permite introducir restricciones o reglas políticas.

- Los sistemas autónomos pueden ser:

  - Stub: Sólo tienen una conexión.

  - De tránsito: Tienen varias conexiones.

  - Multihomed: Tienen varias conexiones pero rechaza el tráfico de tránsito,
    esto se logra al no anunciar las redes de los demás peers.

.. todo:: Buscar atributos de rutas, como AS-Path.

Formas de aceptar rutas
-----------------------

1. Aceptar sólo rutas por defecto desde todos los ISP. En este caso los consumos
   de recursos serán muy bajos y la selección de rutas se hará utilizando el
   router BGP más cercano.

   Hay varias formas:

   - Hay que pedirle al ISP que te envíe sólamente la ruta por defecto.

   - Podés filtrar las rutas que recibís y poner una ruta por defecto hacia el
     ISP.

   - Creo que había otra más que no me acuerdo.

2. Aceptar algunas rutas más las rutas por defecto desde los ISP. En este caso
   el consumo de recursos de memoria y CPU será medio. El router seleccionará la
   ruta específica y si no la conoce lo hará a través del router BGP más
   cercano.

3. Aceptar todas las rutas desde todos los ISP, en este caso el consumo de
   recursos es alto pero en contra posición siempre se elegirá la ruta más
   directa.

Sincronización
--------------

Se aplica a AS de tránsito que tienen algún IGP dentro. Cuando se activa la
sincronización, un AS de tránsito no anuncia por EBGP que puede alcanzar
determinadas rutas hasta que se asegura de que su IGP haya convergido.

Suponiendo que hay un AS de tránsito 200 conectado a dos AS: 100 y 300. Si la
sincronización no está habilitada puede pasar que el AS de tránsito 200
anuncie al AS 300 que posee una ruta hacia al AS 100. Esto está bien siempre y
cuando el IGP dentro del AS de tránsito 100 haya logrado la convergencia, de lo
contrario cuando llegue un paquete desde el AS 300 hacia el AS 100 puede ocurrir
que algún router dentro del AS de tránsito 200 que corre EBGP pero no IGP no
conozca todavía esa ruta.

Al activar la sincronización uno se asegura que no se anuncian por EBGP rutas
que temporalmente no son alcanzables. Si se deshabilita se logra la convergencia
de EBGP más rápidamente a expensas de perder paquetes al principio.

Tipos de mensajes
-----------------

- Open: Una vez que se estableció una conexión TCP se establece una sesión BGP
  mediante mensajes Open que informan de parámetros básicos como los IDs,
  números de AS, etc.

- Update: Contiene información sobre los prefijos publicados.

- Keepalive: Se usan para indicar que el host sigue activo.

- Notification: Indica que hubo un error, luego de este mensaje se cierra la
  conexión TCP.

- Route refresh: Es opcional, se usa para solicitar o enviar actualizaciones de
  rutas.

Mi escenario en GNS3
--------------------

Hice un escenario con un AS de tránsito conectado a dos AS de punta. El AS 200
(de tránsito) tiene OSPF y el AS 300 tiene RIP.

La idea era que los routers del AS de tránsito tengan todas las rutas para poder
elegir los caminos, pero que los AS de punta reciban por BGP sólo una ruta por
defecto porque no necesitan más que eso.

.. image:: ./gns3_bgp.png

Rutas
~~~~~

R1::

       1.0.0.0/8 is variably subnetted, 3 subnets, 3 masks
  C       1.0.1.0/26 is directly connected, FastEthernet0/0
  S       1.0.1.0/24 is directly connected, Null0
  C       1.0.1.192/32 is directly connected, Loopback0
       2.0.0.0/30 is subnetted, 1 subnets
  C       2.0.0.0 is directly connected, Serial0/0
  B*   0.0.0.0/0 [20/0] via 2.0.0.2, 00:27:25

R2::

       1.0.0.0/8 is variably subnetted, 10 subnets, 4 masks
  B       1.0.1.0/24 [20/0] via 2.0.0.1, 00:51:38
  O E2    1.0.3.0/24 [110/1] via 1.0.2.134, 00:10:26, FastEthernet0/1
  C       1.0.2.0/26 is directly connected, FastEthernet0/0
  O       1.0.2.64/26 [110/30] via 1.0.2.134, 00:10:26, FastEthernet0/1
  C       1.0.2.128/30 is directly connected, Serial0/1
  C       1.0.2.132/30 is directly connected, FastEthernet0/1
  O       1.0.2.136/30 [110/20] via 1.0.2.134, 00:10:26, FastEthernet0/1
  O       1.0.2.194/32 [110/21] via 1.0.2.134, 00:10:27, FastEthernet0/1
  O       1.0.2.193/32 [110/20] via 1.0.2.134, 00:10:27, FastEthernet0/1
  C       1.0.2.192/32 is directly connected, Loopback0
       2.0.0.0/30 is subnetted, 2 subnets
  C       2.0.0.0 is directly connected, Serial0/0
  O       2.0.0.4 [110/84] via 1.0.2.134, 00:10:31, FastEthernet0/1
  S*   0.0.0.0/0 is directly connected, Null0

R3::

   #      DST-ADDRESS        PREF-SRC        GATEWAY            DISTANCE
   0 ADo  1.0.1.0/24                         1.0.2.133               110
   1 ADo  1.0.2.0/26                         1.0.2.133               110
   2 ADo  1.0.2.64/26                        1.0.2.138               110
   3 ADo  1.0.2.128/30                       1.0.2.138               110
                                             1.0.2.133
   4 ADC  1.0.2.132/30       1.0.2.134       ether1                    0
   5 ADC  1.0.2.136/30       1.0.2.137       ether2                    0
   6 ADo  1.0.2.192/32                       1.0.2.133               110
   7 ADC  1.0.2.193/32       1.0.2.193       loopback0                 0
   8 ADo  1.0.2.194/32                       1.0.2.138               110
   9 ADo  1.0.3.0/24                         1.0.2.138               110
  10 ADo  2.0.0.0/30                         1.0.2.133               110
  11 ADo  2.0.0.4/30                         1.0.2.138               110

R4::

       1.0.0.0/8 is variably subnetted, 10 subnets, 4 masks
  O E2    1.0.1.0/24 [110/1] via 1.0.2.137, 00:11:25, FastEthernet0/1
  B       1.0.3.0/24 [20/0] via 2.0.0.6, 00:49:10
  O       1.0.2.0/26 [110/30] via 1.0.2.137, 00:11:25, FastEthernet0/1
  C       1.0.2.64/26 is directly connected, FastEthernet0/0
  C       1.0.2.128/30 is directly connected, Serial0/1
  O       1.0.2.132/30 [110/20] via 1.0.2.137, 00:11:25, FastEthernet0/1
  C       1.0.2.136/30 is directly connected, FastEthernet0/1
  C       1.0.2.194/32 is directly connected, Loopback0
  O       1.0.2.193/32 [110/20] via 1.0.2.137, 00:11:26, FastEthernet0/1
  O       1.0.2.192/32 [110/21] via 1.0.2.137, 00:11:26, FastEthernet0/1
       2.0.0.0/30 is subnetted, 2 subnets
  O       2.0.0.0 [110/84] via 1.0.2.137, 00:11:26, FastEthernet0/1
  C       2.0.0.4 is directly connected, Serial0/0
  S*   0.0.0.0/0 is directly connected, Null0

R5::

       1.0.0.0/8 is variably subnetted, 5 subnets, 4 masks
  R       1.0.3.0/26 [120/1] via 1.0.3.130, 00:00:07, FastEthernet0/0
  S       1.0.3.0/24 is directly connected, Null0
  C       1.0.3.128/30 is directly connected, FastEthernet0/0
  C       1.0.3.192/32 is directly connected, Loopback0
  R       1.0.3.193/32 [120/1] via 1.0.3.130, 00:00:07, FastEthernet0/0
       2.0.0.0/30 is subnetted, 1 subnets
  C       2.0.0.4 is directly connected, Serial0/0
  B*   0.0.0.0/0 [20/0] via 2.0.0.5, 00:49:46

R6::

  #      DST-ADDRESS        PREF-SRC        GATEWAY            DISTANCE
  0 ADr  0.0.0.0/0                          1.0.3.129               120
  1 ADr  1.0.3.0/24                         1.0.3.129               120
  2 ADC  1.0.3.0/26         1.0.3.1         ether2                    0
  3 ADC  1.0.3.128/30       1.0.3.130       ether1                    0
  4 ADr  1.0.3.192/32                       1.0.3.129               120
  5 ADC  1.0.3.193/32       1.0.3.193       loopback0                 0
  6 ADr  2.0.0.4/30                         1.0.3.129               120

Configuraciones
~~~~~~~~~~~~~~~

Solamente pongo las de los Ciscos porque son las que más trabajo hacen.

R1::

  interface Loopback0
   ip address 1.0.1.192 255.255.255.255
  !
  interface FastEthernet0/0
   ip address 1.0.1.1 255.255.255.192
   duplex auto
   speed auto
  !
  interface Serial0/0
   ip address 2.0.0.1 255.255.255.252
   clock rate 2000000
  !
  router bgp 100
   no synchronization
   bgp log-neighbor-changes
   network 1.0.1.0 mask 255.255.255.0
   neighbor 2.0.0.2 remote-as 200
   no auto-summary
  !
  ip route 1.0.1.0 255.255.255.0 Null0 250

R2::

  interface Loopback0
   ip address 1.0.2.192 255.255.255.255
  !
  interface FastEthernet0/0
   ip address 1.0.2.1 255.255.255.192
   duplex auto
   speed auto
  !
  interface Serial0/0
   ip address 2.0.0.2 255.255.255.252
   clock rate 2000000
  !
  interface FastEthernet0/1
   ip address 1.0.2.133 255.255.255.252
   duplex auto
   speed auto
  !
  interface Serial0/1
   ip address 1.0.2.129 255.255.255.252
   clock rate 2000000
  !
  router ospf 10
   log-adjacency-changes
   redistribute bgp 200 subnets
   passive-interface FastEthernet0/0
   passive-interface Serial0/0
   passive-interface Loopback0
   network 1.0.2.0 0.0.0.63 area 0
   network 1.0.2.128 0.0.0.3 area 0
   network 1.0.2.132 0.0.0.3 area 0
   network 1.0.2.192 0.0.0.0 area 0
   network 2.0.0.0 0.0.0.3 area 0
  !
  router bgp 200
   no synchronization
   bgp log-neighbor-changes
   network 1.0.2.0 mask 255.255.255.192
   network 1.0.2.128 mask 255.255.255.252
   network 1.0.2.132 mask 255.255.255.252
   network 1.0.2.192 mask 255.255.255.255
   network 2.0.0.0 mask 255.255.255.252
   neighbor 1.0.2.194 remote-as 200
   neighbor 1.0.2.194 update-source Loopback0
   neighbor 2.0.0.1 remote-as 100
   neighbor 2.0.0.1 default-originate
   neighbor 2.0.0.1 route-map DEFAULT out
   no auto-summary
  !
  ip route 0.0.0.0 0.0.0.0 Null0
  !
  ip prefix-list DEFAULT seq 5 permit 0.0.0.0/0
  !
  route-map DEFAULT permit 10
   match ip address prefix-list DEFAULT

R4::

  interface Loopback0
   ip address 1.0.2.194 255.255.255.255
  !
  interface FastEthernet0/0
   ip address 1.0.2.65 255.255.255.192
   duplex auto
   speed auto
  !
  interface Serial0/0
   ip address 2.0.0.5 255.255.255.252
   clock rate 2000000
  !
  interface FastEthernet0/1
   ip address 1.0.2.138 255.255.255.252
   duplex auto
   speed auto
  !
  interface Serial0/1
   ip address 1.0.2.130 255.255.255.252
   clock rate 2000000
  !
  router ospf 10
   log-adjacency-changes
   redistribute bgp 200 subnets
   passive-interface FastEthernet0/0
   passive-interface Serial0/0
   passive-interface Loopback0
   network 1.0.2.64 0.0.0.63 area 0
   network 1.0.2.128 0.0.0.3 area 0
   network 1.0.2.136 0.0.0.3 area 0
   network 1.0.2.194 0.0.0.0 area 0
   network 2.0.0.4 0.0.0.3 area 0
  !
  router bgp 200
   no synchronization
   bgp log-neighbor-changes
   network 1.0.2.64 mask 255.255.255.192
   network 1.0.2.128 mask 255.255.255.252
   network 1.0.2.136 mask 255.255.255.252
   network 1.0.2.194 mask 255.255.255.255
   network 2.0.0.4 mask 255.255.255.252
   neighbor 1.0.2.192 remote-as 200
   neighbor 1.0.2.192 update-source Loopback0
   neighbor 2.0.0.6 remote-as 300
   neighbor 2.0.0.6 default-originate
   neighbor 2.0.0.6 route-map DEFAULT out
   no auto-summary
  !
  ip route 0.0.0.0 0.0.0.0 Null0 250
  !
  ip prefix-list DEFAULT seq 5 permit 0.0.0.0/0
  !
  route-map DEFAULT permit 10
   match ip address prefix-list DEFAULT

R5::

  interface Loopback0
   ip address 1.0.3.192 255.255.255.255
  !
  interface FastEthernet0/0
   ip address 1.0.3.129 255.255.255.252
   duplex auto
   speed auto
  !
  interface Serial0/0
   ip address 2.0.0.6 255.255.255.252
   clock rate 2000000
  !
  router rip
   version 2
   redistribute bgp 300 metric 1
   passive-interface Serial0/0
   passive-interface Loopback0
   network 1.0.0.0
   network 2.0.0.0
   no auto-summary
  !
  router bgp 300
   no synchronization
   bgp log-neighbor-changes
   network 1.0.3.0 mask 255.255.255.0
   neighbor 2.0.0.5 remote-as 200
   no auto-summary
  !
  ip route 1.0.3.0 255.255.255.0 Null0 250

Links
-----

- https://www.juniper.net/documentation/en_US/junos/topics/concept/bgp-routing-messages-overview.html
