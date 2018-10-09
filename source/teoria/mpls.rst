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

- LSR (Label Switcher Router): Router MPLS que conmuta etiquetas, están dentro
  de la red MPLS, porque los de borde se llaman LER.

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

- RIB (Routing Information Base): Es la tabla de ruteo, la que se ve cuando uno
  hace ``show ip route``. Tiene cosas como métricas.

- FIB (Forwarding Information Base): Es una versión optimizada de la RIB, con
  sólo las mejores rutas. En Cisco se llama CEF.

- LIB (Label Information Base): Tabla que contiene todas las etiquetas MPLS. Es
  formada a partir de la tabla RIB y del intercambio de información con otros
  LSR.

- LFIB (Label Forwarding Information Base): Es la versión optimizada de la tabla
  LIB.

- PHP (Penultimate Hop Popping): Es una de las configuraciones que se pueden
  usar, en la que el penúltimo router del trayecto quita (pop) la etiqueta
  porque sabe que el siguiente es el último. Permite reducir el consumo de CPU y
  se usa generalmente por defecto.

- Upstream: Construcción de camino, se eligen las etiquetas.

- Downstream: Ya está el camino, pasan datos.

.. todo::

  Terminar.

  - HELLO: Protocolo de hello, empieza con UDP y pasa a TCP.

  - LFIB (Label Forwarding Information Base):

Mensajes LDP
------------

- Discovery: Anuncia y mantiene la presencia de un LSR en al red. Son multicast
  UDP.

- Session: Establece, mantiene y finaliza la sesión LDP, una vez establecida la
  sesión se pueden transferir *Advertisements*. Usa TCP.

- Advertisement: Crea, cambia y borra mapeos para FECs.

- Notification: Avisa o señaliza errores.

Funcionamiento
--------------

Cuando un paquete llega desde fuera a un LER, se le asocia una etiqueta que
contiene información de la ruta que debe tomar el paquete en la red MPLS. Los
LSR leen las etiquetas, y mirando su LFIB, determinan la interfaz de salida y le
asignan una nueva etiqueta (swap). Antes de salir, si se usa PHP, el penúltimo
router le quita la etiqueta y el último lo rutea correctamente hacia el destino.

Planos
------

.. image:: ./planos_mpls.png

Cada plano tiene varios componentes.

- Plano de control o ruteo: Lleva una tarea lenta y compleja.

  - Protocolo de ruteo: Por ejemplo OSPF, RIP, BGP.

  - RIB (Routing Information Base).

  - LDP (Label Distribution Protocol).

  - LIB (Label Information Base).

- Plano de datos o forwarding: Tiene dos tables construidas gracias a la
  información creada en el plano de control. La tarea que tiene es simple y
  rápida.

  - FIB: Tabla de forwarding IP.

  - LFIB: Tabla de forwarding de etiquetas.

Problemas a solucionar en el futuro
-----------------------------------

Estos son problemas que se intentan solucionar con MPLS pero todavía no están
totalmente resueltos.

- CBR (Constraint Based Routing): Buscar caminos que safisfagan unas
  restricciones explícitas, como que la pérdida de paquetes sea menor a cierto
  valor o que el retardo sea menor a un cierto tiempo.

- Balanceo de Carga: Se intenta dividir el tráfico (de varios flujos) a través
  de distintos caminos.

Links
-----

- http://networkstatic.net/juniper-and-cisco-comparisons-of-rib-lib-fib-and-lfib-tables/
