Wireshark
=========

Ejemplos de filtros
-------------------

ARP e ICMP que salgan o lleguen a una MAC::

  (arp || icmp) && (eth.src == 08:9e:01:43:f2:eb || eth.dst == 08:9e:01:43:f2:eb)

Tramas Ethernet de broadcast::

  eth.dst == ff:ff:ff:ff:ff:ff

Paquetes IP de broadcast::

  ip.dst == 192.168.0.255

ARP de respuesta::

  arp.opcode == reply

ICMP de respuesta::

  icmp.type == 0

ICMP de request::

  icmp.type == 8

