Juniper
=======

..todo::

   - Se usa puerto de consola serial a ??? baudios

   - Para ayuda ir poniendo ``?``.

Mostrar cosas::

  show configuration

  show interface
  show interface terse
  show interface detail

  show route
  show route terse
  show route detail

Habilitar o deshabilitar interfaz::

  set interface ge-0/0/1 enable
  set interface ge-0/0/1 disable

.. todo:: Agregar como borrar IP y como borrar rutas

Agregar IP::

  set interfaces ge-0/0/0 unit 0 family inet address 192.168.1.1/24
  como borrar?

  set interfaces ge-0/0/0 unit 0 family inet6 address 2001:AA::2/64
  como borrar?

Agregar rutas::

  set routing-options static route 0.0.0.0/0 next-hop 192.168.1.1
  como borrar?

  set routing-options rib inet6.0 static route ::/0 next-hop 2001:A::1/64
  como borrar?

Configurar DNS::

  ip dns set servers=8.8.8.8

Dar IP a loopback::

  set interfaces lo0 unit 1 family inet address 192.168.1.1/24
  set interfaces lo0 unit 1 family inet6 address 2001:AA::2/64
