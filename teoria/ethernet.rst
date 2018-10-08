Ethernet
========

- Está el Ethernet II que es lo que se usa, sino también está el Ethernet 802.3
  que tiene un header ligeramente distinto.

Header
------

- Preamble (8 bytes): Generalmente no se ve en Wireshark.

- Destination MAC address (6 bytes).

- Source MAC address (6 bytes).

- Tags (Opcional) (4 bytes): En el caso en el que se utilizen VLANs.

- Type (2 bytes): ID del protocolo encapsulado.

- Data (46-1500 bytes).

- Frame Check Sequence (FCS) (4 bytes): Es CRC, generalmente no se ve en
  Wireshark.

MTU
---

Ver en la sección :doc:`./general`.

Cableado
--------

Hay dos normas para el orden de los cables, 568-A y 568-B. Se puede usar
cualquiera de las dos normas.

- Si las normas de ambos extremos son iguales, es un cable directo. Sirve para
  conectar dispositivos de distintas capas (PC-Switch, Switch-Router, etc.).

- Si las normas de ambos extremos son distintas, es un cable cruzado. Sirve para
  conectar dispositivos de la misma capa (PC-PC, Router-Router, etc.).

Normas
~~~~~~

Este es el orden de los colores visto desde la cara opuesta a la pestaña, con el
cable apuntando hacia arriba y viendo los colores de izquierda a derecha.

- Norma 586-A:

  - Verde y blanco. TX+.

  - Verde. TX-.

  - Naranja y blanco. RX+.

  - Azul.

  - Azul y blanco.

  - Naranja. RX-.

  - Marrón y blanco.

  - Marrón.

- Norma 586-B:

  - Naranja y blanco. TX+.

  - Naranja. TX-.

  - Verde y blanco. RX+.

  - Azul.

  - Azul y blanco.

  - Verde. RX-.

  - Marrón y blanco.

  - Marrón.

Switch
------

El switch sólo reenvía las tramas unicast por una interfaz salvo de que se trate
de una trama broadcast, en tal caso hace un *flood* reenviando por todas las
bocas.

Para esto el switch tiene una tabla de direcciones MAC con su correspondiente
interfaz. Cuando recibe una trama en alguna interfaz aprende la MAC mirando la
dirección de origen. Un caso especial es cuando recibe una trama que tiene como
destino una MAC que no está en la tabla, como no sabe por qué interfaz reenviar,
entonces hace un *flood*, aprenderá esa MAC cuando ese host responda.
