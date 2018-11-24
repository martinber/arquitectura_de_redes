Seguridad
=========

.. todo:: Hacer

- IDS (Intruder Detection System): Como que analiza el tráfico con interligencia
  artificial.

- IPS (Intruder Protection System): Es como IDS pero actúa (creo).

Tipos de firmas
----------------

- Digital: Es una firma que cumple la ley, como el certificado del SIAL.

- Electronica: No cumple la ley.

- Digitalizada: Es como cuando firmas en una tablet para el carnet.


Firewall
--------

iptables
~~~~~~~~

Como ventajas se le pueden ir agregando módulos. También se pueden hacer filtros
stateless y stateful, pero no vamos a hacer filtros stateful.

.. todo:: Explicar mejor esta lista:

- Tablas: Conjunto de cadenas.

- Cadenas: Conjunto de reglas.

- Reglas:

- Objetivos: Es lo que quiero hacer

Tablas que hay:

- filter:

- nat:

- mangle:

- raw:

Cadenas que hay:

- Prerouting:

- Input:

- Forward:

- Output:

- Postrouting:

Objetivos:

- ACCEPT:

- REJECT:

- DROP:

- SNAT/MASQUERADE:

- DNAT:

- LOG:


.. todo:: Poner dibujo de las tablas y cadenas
  https://commons.wikimedia.org/wiki/Category:Netfilter#/media/File:Netfilter-diagram-rus.png

https://www.net.t-labs.tu-berlin.de/teaching/ss08/RL_labcourse/docs/08-lartc.pdf

https://www.booleanworld.com/depth-guide-iptables-linux-firewall/#How_does_iptables_work
