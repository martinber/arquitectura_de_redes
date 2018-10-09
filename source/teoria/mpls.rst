MPLS
====

Multiprotocol Label Switching.

- Más rápido porque hace switching de capa 2 mirando etiquetas en lugar de ruteo
  de capa 3 mirando direcciones.

- Intenta conseguir las ventajas de ATM pero sin sus inconvenientes.

- No reemplaza el enrutamiento IP, aunque sea una red MPLS también es una red
  IP.

- Permite agrupar paquetes en flujos que recibirán todos el mismo tratamiento.

- Permite realizar ingeniería de tráfico, ya que no siempre se usa el camino más
  corto. También realizar QoS o Policy Routing.

- Permite realizar VPN porque realiza túneles entre dominios IP.

- La etiqueta va después de la cabecera de la capa de enlace (Ethernet, ATM,
  Frame-Relay, PPP) y antes de la cabecera IP. Pueden anidarse formando una
  pila, como si fuera una red MPLS dentro de otra.

- Al viajar por la red MPLS el campo TTL de IP no cambia, al salir se le coloca
  el valor adecuado.

- El enrutamiento se hace en base a la información que suministra un protocolo
  de ruteo, como OSPF.

- Se pueden usar switches ATM sin cambiar su hardware. Pero de todas formas es
  una desventaja que los routers deban ser MPLS.

Definiciones
------------

- LSR (Label Switcher Router): Router MPLS, sobre todo los router dentro de la
  red MPLS, porque los de borde se llaman LER.

- LER (Label Edge Router): Router MPLS de borde.

- FEC (Forwarding Equivalence Class): Es un flujo de tráfico, está asociado a un
  LSP.

- LSP (Label Switched Path): Es un túnel virtual dentro de la red MPLS. Los
  túneles son unidireccionales (un solo sentido).

- LDP (Label Distribution Protocol): Protocolo que distribuye etiquetas. Permite
  a los LSR informar la relación FEC/etiqueta. Hay más protocolos de
  distribución de etiquetas, como BGP y RSVP.


- AToM (Any Transport Over MPLS): Permite usar MPLS para enviar tráfico de capa
  2 como Ethernet, PPP o ATM. Por lo tanto se pueden hacer VLAN sobre MPLS.

- Upstream: Construcción de camino, se eligen las etiquetas.

- Downstream: Ya está el camino, pasan datos.

.. todo::

  Terminar.

  - HELLO: Protocolo de hello, empieza con UDP y pasa a TCP.

  - LFIB (Label Forwarding Information Base):

Planos
------

.. image:: ./planos_mpls.png

- Plano de control: Donde se establece el vinculo. MPLS construye una tabla LIB
  de etiquetas.

- Plano de datos: Por donde se mandan datos. Está FIB y LFIB.

Funcionamiento
--------------

Cuando un paquete o flujo de paquetes llega a un LER, éste les asigna una
etiqueta determinada.

Los LSR reenvían paquetes dependiendo la Label que
llevan. Antes de reenviar le asignan la nueva etiqueta (swap).

En la red se generan de esta forma LSP (túneles), cada par de LSR deciden la
etiqueta a usar entre ellos.

Cuando el paquete está por salir de la red MPLS, el LER extrae las etiquetas
(pop).

Los LSP pueden ser creados de forma estática o por un protocolo de señalización
como LDP.

Problemas a solucionar en el futuro
-----------------------------------

Estos son problemas que se intentan solucionar con MPLS pero todavía no están
totalmente resueltos.

- CBR (Constraint Based Routing): Buscar caminos que safisfagan unas
  restricciones explícitas, como que la pérdida de paquetes sea menor a cierto
  valor o que el retardo sea menor a un cierto tiempo.

- Balanceo de Carga: Se intenta dividir el tráfico (de varios flujos) a través
  de distintos caminos.
