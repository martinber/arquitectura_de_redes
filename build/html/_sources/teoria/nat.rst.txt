NAT
===

Network Address Translation.

Basic NAT
---------

- Hace un mapeo de direcciones IP uno a uno.

NAPT
----

El tipo de NAT más usado es NAPT Dinámico (Network Address Port Translation):

- Hace IP masquerading o One-to-many NAT: es una técnica que esconde un espacio
  de direcciones IP (privadas) detrás de una sola IP pública.

- Se cambia la IP de origen de los paquetes que salen del Router.

- Hace falta porque las IP privadas no son ruteables en la internet.

- Para permitir que salgan varios usuarios se debe cambiar también el puerto de
  origen TCP o UDP, para de esa forma identificar las conexiones a distintos
  usuarios.

- En el caso de ping ICMP se cambia el Identifier.

Tipos
-----

- Según los campos que modifican:

  - NAT Básico: Sólo se cambia la dirección IP, requiere una IP pública por cada
    IP privada en uso.

  - NAPT (Network Address Port Translation): Modifica la dirección IP y además
    el puerto TCP o UDP. Permite sacar muchas IP privadas por un número menor de
    direcciones públicas.

- Según si es estático o dinámico:

  - Estático: La tabla de correspondencias es estáticas.

  - Dinámico: La tabla se va modificando, permite reutilizar direcciones de
    salida.

Otras terminologías
-------------------

- DNAT (Destination Network Address Translation): Cambia la dirección IP de
  destino.

- SNAT: Puede significar:

  - Source NAT: Se cambia la IP de origen, lo común.

  - Stateful NAT, Secure NAT, que se yo.
