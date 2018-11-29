Cisco
=====

- Se usa puerto de consola serial a 9600 baudios

- Para ayuda ir poniendo ``?``.

- Para ir volviendo atrás poner ``exit``.

Básico
------

Pasar al modo privilegiado::

  router> enable

Mostrar cosas::

  router# show ip interface brief
  router# show ipv6 interface brief
  router# show ip interface f0/0
  router# show ipv6 interface f0/0
  router# show ip route
  router# show running-config

Configurar IP::

  router# configure terminal
  router(config)# interface FastEthernet0/0
  router(config-if)# ip address 192.168.1.1 255.255.255.0

Configurar IP secundaria::

  router(config-if)# ip address 192.168.2.1 255.255.255.0 secondary

Habilitar interfaz::

  router(config-if)# no shutdown
  router(config-if)# exit

Configurar rutas::

  router(config)# ip route 0.0.0.0 0.0.0.0 {IP gateway}
  router(config)# ip route {IP destino} {mascara destino} {IP gateway}

  router(config)# ipv6 route ::/0 {IP gateway}
  router(config)# ipv6 route {IP destino}/{mascara destino} {IP gateway}

Crear loopback y dar IP::

  (config)# interface loopback 0
  (config-if)# ip address 7.0.0.4 255.0.0.0

Para guardar la configuracion de inicio::

  router# copy running-config startup-config

Para borrar la configuración de inicio encontré tres formas, en GNS3 no anda
ninguna::

  router# erase startup-config
  router# erase nvram:
  router# write erase

Para reiniciar en GNS3 usar el botón de "Stop", en un dispositivo físico::

  router# reload

Para reiniciar la configuración en GNS3, agregar un nuevo router, click derecho,
exportar su configuración, hacer click derecho en el router a reiniciar, imporar
configuración.

NAT
---

Configurar cual es la interna y cual externa::

  router# configure terminal
  router(config)# interface FastEthernet0/0
  router(config-if)# ip nat inside
  router(config-if)# exit
  router(config)# interface FastEthernet0/1
  router(config-if)# ip nat outside
  router(config-if)# exit

Configurar lista de acceso con un numero mayor a 100 protocolo IP, para
cualquier origen y cualquier destino::

  router(config)# access-list 101 permit ip any any

Configurar pool de rango de IPs a usar para nateo (las de afuera, normalmente
una sola)::

  router(config)# ip nat pool {nombre} {IP inicial} {IP final} netmask {mascara}

Terminar::

  router(config)# ip nat inside source list 101 pool {nombre pool} overload

Mostrar info::

  router(config)# exit
  router# show ip nat translations


Servidor DHCP
-------------

Primero configurar IPs y rutas.

Dar de alta dhcp, con "no" se da de baja::

  router# configure terminal
  router(config)# service dhcp

Crear pool de IPs, excluir IPs de la red a no asignar y dar red a utilizar::

  router(config)# ip dhcp excluded-address {IP minima} {IP maxima}
  router(config)# ip dhcp pool {nombre}

Configurar parametros, la red es el rango de IPs a dar::

  router(dhcp-config)# network {Numero red} {mascara}
  router(dhcp-config)# domain-name {IP DNS}
  router(dhcp-config)# default-router {IP gateway}
  router(dhcp-config)# lease {dias}

Configurar tiempo de comprobacion de si la IP esta asignada::

  router(dhcp-config)# exit
  router(config)# ip dhcp timeout {milisegundos}

Comprobacion de configuracion::

  router(config)# exit
  router# exit

  router# show ip dhcp binding
  router# show ip dhcp conflict
  router# show ip dhcp server statistics

Mostrar debug::

  router# debug ip dhcp server events|packet|linkage


Enlace serial
-------------

- DCE: (Hembra), Configura velocidad.
- DTE: (Macho), Recibe velocidad.

Para ver interfaces seriales en Cisco (muestra si es DCE o DTE)::

  router# show controllers Serial0/0/0

Para configurar DCE::

  router# configure terminal
  router(config)# interface serial 0/0/0
  router(config-if)# clock rate 125000
  router(config-if)# ip address {ip}

Para configurar DTE en Cisco no hay nada especial. Directamente te pones IP.

Servidor PPPoE
--------------

Configurar salida al exterior::

  router# configure terminal
  router(config)# interface fastEthernet 0/0
  router(config-if)# ip address {IP salida}
  router(config-if)# no shutdown
  router(config-if)# exit
  router(config)# ip route 0.0.0.0 0.0.0.0 {gateway}

PPPoE::

  router# configure terminal
  router(config)# username {usuario cliente} password {pass cliente}
  router(config)# bba-group pppoe global
  router(config)# virtual-template 20

Configurar interfaz::

  router(config)# interface fastEthernet 0/1
  router(config-if)# pppoe enable

Configurar PPPoE::

  router(config)# interface virtual-template 20
  router(config-if)# mtu 1492
  router(config-if)# ppp mtu adaptive
  router(config-if)# ip unnumbered fastEthernet 0/1
  router(config-if)# peer default ip address pool pppoepool
  router(config-if)# ppp authentication {no me acuerdo si era "pap" o "chap"}
  router(config-if)# ppp chap hostname {nombre servidor}
  router(config-if)# ppp chap password 0 {pass servidor}
  router(config-if)# exit
  router(config)# ip local pool pppoepool 10.10.10.1 10.10.10.200

Falta hacer NAT para que pueda salir afuera.

VLAN (switch)
-------------

Mostrar VLANs activas::

  switch# show vlan

Agregar VLAN::

  switch(config)# vlan 2
  switch(config-vlan)# name {nombre_vlan}
  switch(config-vlan)# exit

Agregar interfaces a una VLAN, especificando rango::

  switch(config)# interface range fastEthernet 0/{x} - {y}
  switch(config-if-range)# switchport mode access
  switch(config-if-range)# switchport access vlan 2

Agregar trunk, también se puede especificar un rango pero este es ejemplo de la
configuración de solo una::

  switch(config)# interface fastEthernet 0/{x}
  switch(config-if)# encapsulation dot1q
  switch(config-if)# switchport mode trunk
  switch(config-if)# switchport trunk native vlan 1
  switch(config-if)# switchport trunk allowed vlan all
  switch(config-if)# switchport trunk allowed vlan add 2
  switch(config-if)# switchport trunk allowed vlan remove 3

VLAN (router)
-------------

Routear con Cisco en trunk::

  router(config)# interface FastEthernet0/0

  router(config-if)# sub-interface FastEthernet0/0.2
  router(config-if)# encapsulation dot1q 2
  router(config-if)# ip address 192.168.1.1 255.255.255.0

  router(config-if)# sub-interface FastEthernet0/0.3
  router(config-if)# encapsulation dot1q 3
  router(config-if)# ip address 192.168.2.1 255.255.255.0

IPv6
----

Habilitar ruteo IPv6::

  router(config)# ipv6 unicast-routing

Habilitar dirección link-local bajo una interfaz::

  router(config)# interface {interfaz}
  router(config-if)# ipv6 enable

Habilitar RA en una interfaz y configurar el intervalo en segundos::

  router(config)# interface {interfaz}
  router(config-if)# no ipv6 nd suppress-ra
  router(config-if)# ipv6 nd ra-interval {intervalo}

Me pasó a veces que RA viene activado en las interfaces por defecto, es posible
que si uno no quiere RA, haya que dehabilitarlo manualmente::

  router(config)# interface {interfaz}
  router(config-if)# ipv6 nd suppress-ra

En routers nuevos puede que los comandos sean::

  router(config)# interface {interfaz}
  router(config-if)# no ipv6 nd ra suppress
  router(config-if)# ipv6 nd ra interval {intervalo}

6to4
----

.. image:: ./6to4.svg

Activar ruteo IPv6::

  router(config)# ipv6 unicast-routing

Dar IPv4 a la interfaz WAN::

  router(config)# interface FastEthernet {if_wan}
  router(config-if)# ip address {ipv4_if_wan} {ipv4_mask_if_wan}
  router(config-if)# no shutdown
  router(config-if)# exit

Crear tunel y ponerle una IPv6 que está en la red ``2002:{ipv4}:0000::/64``,
generalmente la ``::1`` e indicar que sale por la ``{if_wan}``::

  router(config)# interface Tunnel2002
  router(config-if)# no ip address
  router(config-if)# no ip redirects
  router(config-if)# no shutdown
  router(config-if)# ipv6 address {ipv6_if_wan}/128
  router(config-if)# tunnel source FastEthernet {if_wan}
  router(config-if)# tunnel mode ipv6ip 6to4
  router(config-if)# exit

Configurar la interfaz con IPv6 que da a la LAN, tiene que estar en una subred,
generalmente también es la ``::1``, en tal caso es
``2002:{ipv4}:{subred}::1/64``. También habilitar RA::

  router(config)# interface FastEthernet {if_lan}
  router(config-if)# ipv6 address {ipv6_if_lan} {ipv6_mask_if_lan}
  router(config-if)# no shutdown
  router(config-if)# no ipv6 nd suppress-ra

Configurar las rutas, la primera es una ruta IPv4 común para que se llegue a
destino por IPv4. La segunda no se bien por qué está. La tercera es el gateway
IPv6, en el caso en el que hayan varios túneles hacia varias redes IPv6 habría
que separar las rutas en vez de tener una que vaya a ``::/0``. Pero en el caso
más simple es::

  router(config)# ip route 0.0.0.0 0.0.0.0 {ipv4_if_gw}
  router(config)# ipv6 route 2002::/16 Tunnel2002
  router(config)# ipv6 route ::/0 {ipv6_if_wan_destino}

RIP
---

RIPv2
~~~~~

Hay que decirle que version usar.

::

  router(config)#router rip
  router(config-router)#version 2

Desactivar la sumarización de redes, por lo tanto al usar el comando ``network``
se publican las subredes que pertenezcan a esa red en lugar de la red completa
como una sola::

  router(config-router)#no auto-summary

Agregar redes en donde trabajar. De paso publica esas redes, como no pide
máscara se fija en la clase de la red para determinar la máscara, sumarizando
las redes::

  router(config-router)#network {red}

Pasivar una interfaz::

  R6(config-router)#passive-interface {interfaz}

Distribuir más redes, usar metricas menores a 15::

  router(config-router)#redistribute static
  router(config-router)#redistribute static metric {metrica}

Redistribuir EBGP si es necesario, usar metricas menores a 15::

  router(config)# router rip
  router(config-route)# redistribute bgp {numero_as} metric {metrica}

Redistribuir IBGP si es necesario, usar metricas menores a 15::

  router(config)# router rip
  router(config-route)# redistribute bgp {numero_as} metric {metrica}
  router(config-route)# exit
  router(config)# router bgp {numero_as}
  router(config-route)# bgp redistribute-internal

RIPng
~~~~~

Primero dar IPv6 estáticas a cada interfaz y borrar las rutas si es que hay.

Después habilitar RIP en las interfaces que uno quiera, asignándole un nombre
cualquiera al proceso::

  router(config)# interface {interfaz}
  router(config-if)# ipv6 rip {nombre_proceso} enable
  router(config-if)# exit
  router(config)# ipv6 router rip {nombre_proceso}
  router(config-if)# redistribute connected

OSPF
----

OSPFv2
~~~~~~

Crear loopback::

  router(config)# interface loopback 0
  router(config-if)# ip address {ip_loopback} {mascara}

Crear un proceso OSPF, por ejemplo poner el número ``100``::

  router(config)# router ospf {id_proceso}

Agregar redes en donde trabajar. De paso publica esas redes, con la máscara
dada. La mascara debe ser invertida, por ejemplo ``255.255.255.240`` se vuelve
``0.0.0.15``, si se suman queda 255 en cada octeto::

  router(config-route)# network {red} {mascara_invertida} area 1

Mascara invertida: 0.255.255.255 publica la 10.X.X.X.

Pasivar interfaces::

  router(config-route)# passive-interface {interfaz}
  router(config-route)# passive-interface loopback0

Redistribuir EBGP si es necesario::

  router(config)# router ospf {proceso}
  router(config-route)# redistribute bgp {numero_as} subnets

Redistribuir IBGP si es necesario::

  router(config)# router ospf {proceso}
  router(config-route)# redistribute bgp {numero_as} subnets
  router(config-route)# exit
  router(config)# router bgp {numero_as}
  router(config-route)# bgp redistribute-internal

OSPFv3
~~~~~~

Primero se debe asignar una IPv4 al loopback, esta IP es usada por OSPF para
identificar a este router::

  router(config)# interface loopback 0
  router(config-if)# ip address {ipv4_loopback} {mascara}
  router(config-if)# exit

Después se debe entrar a una interfaz de WAN y activar OSPF, se le debe asignar
un ID de proceso cualquiera mientras que sea un número, yo uso ``1``::

  router(config)# interface {interfaz}
  router(config-if)# ipv6 ospf {id_proceso} area 0

Para indicar qué rutas se deben publicar::

  router(config-if)# ipv6 router ospf {id_proceso}
  router(config-rtr)# redistribute static
  router(config-rtr)# redistribute connected
  router(config-rtr)# exit

Si se quieren agregar más interfaces WAN se puede compartir el ID del proceso,
no es necesario indicar las rutas a distribuir de nuevo::

  router(config)# interface FastEthernet {interfaz}
  router(config-if)# ipv6 ospf {id_proceso} area 0

BGP
---

Solamente hay un proceso, hay que darle el numero de AS::

  router(config)# router bgp {numero_as}

Dar redes a anunciar, a diferencia de otros protocolos esto no indica las
interfaces que corren el protocolo, solamente las redes a anunciar::

  router(config-router)# network {red} mask {mascara}

Agregar vecino peer::

  router(config-router)# neighbor {ip_destino} remote-as {as_destino}

Agregar peer estableciendo dirección de loopback de destino por si una de sus
interfaces se cae, normalmente se hace en IBGP::

  router(config-router)# neighbor {ip_loopback_destino} remote-as {as_destino}

Originar tráfico hacia peer desde mi propio loopback, en caso en que este router
tenga varias interfaces de salida hacia el peer. Si se cae una de las
interfaces, la comunicación comienza a originarse desde otra interfaz, el
destino verá que la IP de origen cambió y rechazará la conexión::

  router(config-router)# neighbor {ip_loopback_destino} update-source loopback0

Publicar una ruta sumarizada que engloba a todas las redes dentro del AS, no se
pueden publicar rutas que no están en la tabla de ruteo el ``null0`` asegura que
si llega un paquete y no hay una ruta más específica el paquete se descarta.::

  router(config)# ip route {red} {mascara} null0 250

  router(config)# router bgp {numero_as}
  router(config)# network {red} mask {mascara}

Configurar para que un peer reciba solamente la ruta por defecto, esto se podría
en un AS de tránsito para darle servicio a un AS que no es de tránsito::

  router(config)# ip route 0.0.0.0 0.0.0.0 null0 250
  router(config)# ip prefix-list DEFAULT permit 0.0.0.0/0
  router(config)# route-map DEFAULT permit 10
  router(config-route-map)# match ip address prefix-list DEFAULT
  router(config-route-map)# exit

  router(config)# router bgp {numero_as}
  router(config-router)# neighbor {ip_destino} route-map DEFAULT out
  router(config-router)# neighbor {ip_destino} default-originate

.. note::

   Supongo que ese último bloque de comandos se puede resumir, el último comando
   propaga la ruta por defecto a un peer en particular, el problema es que
   cuando lo probé el peer recibía todas las rutas BGP más la por defecto.

   Todos los comandos anteriores lo que hacen es algo así como un filtro de
   rutas para el peer, al cual se le daría solamente la ruta por defecto. El
   problema que tuve es que cuando lo probé no propagaba nada, entonces agregué
   el último comando que propaga la ruta por defecto y quedó como me gustaría.

Ver estado de sesiones BGP::

  router# show ip bgp summary

Ver tabla de enrutamiento BGP::

  router# show ip bgp

Ver vecinos y tipo y cantidad de mensajes intercambiados::

  router# show ip bgp neighbors

MPLS
----

No hacenos la implementación, pero pongo algunos comandos que dan información.

Ver las interfaces sobre las que funciona MPLS-LDP::

  show mpls interfaces

Muestra los parámetros que está usando el protocolo en este equipo::

  show mpls ldp parameters

Mostrar los vecinos MPLS::

  show mpls ldp neighbor

Mostrar la tabla de etiquetas::

  show mpls ldp binding

Mostrar la tabla de forwarding::

  show mpls forwarding-table

No sé que hace este::

  show mpls ip binding
