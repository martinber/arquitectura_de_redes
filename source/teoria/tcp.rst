TCP
===

Transmission Control Protocol.

- El numero de secuencia empieza aleatorio, porque cuenta cada no se cuantos
  microsegundos, a partir de la conexion cuenta de a uno.

- Ventana deslizante pero cuenta bytes. Hasta 65000.

- Telnet usa el flag de urgente cuando se hace Ctrl-C

- En las conexiones sin estado como HTTP se suele usar RST en lugar de FIN.

- No se puede establecer una doble conexion porque se repitirian los 4 cosas.

- Para hacer un firewall que impida conectarse a un host en la LAN hayq ue
  bloquear los SYN=1/ACK=0 entrantes.

- Funciona bien cuando el BDP es bajo.


- Iterativo: Atiende y corta al toque.

- Concurrente: Duplica el socket y sigue atendiendo.


.. todo::

   Hacer

   - Control de flujo

   - Control de congestión (Tahoe, Reno, NewReno)
