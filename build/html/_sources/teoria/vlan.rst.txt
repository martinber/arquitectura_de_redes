VLAN
====

- Separa dominios de difusión y dominios de colisión. Los dominios de colisión
  eran importantes cuando se usaban hubs, en el caso de los switches existen
  dominios de difusión que son separados por medio de VLAN.

- Los switches por defecto tienen todos los puertos en la VLAN 1, la 0 y la 255
  no existen.

- La serie 3000 de Cisco tiene dos protocolos, al crear un trunk hay que elegir
  entre 802.1Q y ISL.

802.1Q
------

Se agrega un tag en el header Ethernet que tiene:

- Tag Protocol Identifier (TPID) (2 bytes): Tiene el valor ``0x8100`` para
  indicar que esta es una tag, ya que en paquetes normales esta posición
  llevaría el tipo de paquete Ethernet.

- Priority Code Point (PCP) (3 bits): Se usa para marcar los paquetes con una
  cierta prioridad.

- Drop Eligible Indicator (DEI) (1 bit): Indica si este paquete se puede
  descartar en caso de congestión.

- VLAN Identifier (VID) (12 bits): Identificador de VLAN.

Se puede hacer *double tagging*, en tal caso hay dos tags uno al lado de otro.

.. todo:: Creo que el double  tagging es 802.1ac

.. image:: ./8021q.png

By `Luca Ghio <https://commons.wikimedia.org/wiki/User:Luca_Ghio>`_ `CC BY-SA
4.0 <https://creativecommons.org/licenses/by-sa/4.0>`_, from Wikimedia Commons.

ISL
---

Es un protocolo propietario de Cisco, encapsula a la trama Ethernet.

Tiene un header de 26 bytes, como datos lleva la trama Ethernet y finalmente
tiene un CRC adicional de 4 bytes. No sé que pasa con el preámbulo de Ethernet,
si queda adentro o afuera.
