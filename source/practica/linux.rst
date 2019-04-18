Linux
=====

Los comandos mas básicos están en :doc:`linux_basico`.

ARP
---

Para hacer ARP gratuitos::

  arping -A -c 100000 -I enp4s0 {mi IP}

NAT
---

::

  sudo bash -c "echo 1 > /proc/sys/net/ipv4/ip_forward"
  modprobe iptable_nat

Ver tabla::

  iptables -t nat -n -L

Agregar regla con IP de salida::

  iptables -t nat -A POSTROUTING -o {interfaz WAN} -j SNAT --to {IP salida}

Si la IP no es estática se puede usar ``MASQUERADE`` que chequea la IP de salida
pero es más lento::

  iptables -t nat -A POSTROUTING -o {interfaz WAN} -j MASQUERADE

Servidor DHCP
-------------

Instalar::

  sudo apt intall isc-dhcp-server

Editar ``/etc/default/isc-dhcp-server`` y configurar interfaz a usar::

  INTERFACESv4="eth0"

Editar ``/etc/dhcp/dhcpd.conf``::

  default-lease-time 600; # 10 minutes
  max-lease-time 7200; # 2 hours

  ddns-update-style none;
  authoritative;

  option domain-name-servers 8.8.8.8;
  option broadcast-address 192.168.1.255;
  option routers 192.168.1.1;
  option subnet-mask 255.255.255.0;

  subnet 192.168.1.0 netmask 255.255.255.0
  {
  range 192.168.1.100 192.168.1.200;
  }

Iniciar::

  sudo /etc/init.d/isc-dhcp-server restart

Si hay problemas usar::

  less /var/log/syslog
  sudo systemctl status isc-dhcp-server.service

Cliente DHCP
------------

Desactivar network-manager, sacarse todas las IP y pedir por DHCP::

  sudo service network-manager stop
  sudo ip flush dev eth0
  sudo dhclient eth0


Servidor PPPoE
--------------

NAT::

  echo 1 > /proc/sys/net/ipv4/ip_forward
  iptables -t nat -A POSTROUTING -o x -j SNAT --to x.x.x.x
  iptables -t nat -n -L

Instalar **rp-pppoe**::

  tar -xvzf rp-pppoe-x.tar.gz
  cd rp-pppoe-x/src
  ./configure
  make
  make install

Configurar ``/etc/ppp/pppoe-server-options``::

  require-chap
  lcp-echo-interval 30 # cada cuanto pregunta si está la conexión activa
  lcp-echo-failure 4 # intentos
  netmask 255.255.255.255
  ms-dns {IP DNS}

Configurar ``/etc/ppp/chap-secrets``::

  # client server secret IP addresses
  "usuario1" * "contraseña1" {IP fija que queres dar}
  "usuario2" * "contraseña2" *

Configurar permisos::

  chmod 600 /etc/ppp/chap-secrets

Configurar direcciones dinámicas en ``nano /etc/ppp/direcciones``, una IP por
línea o un rango como ``192.168.50.2-30``.

Iniciar servidor, dando interfaz e IP de la interfaz por donde sale el PPPoE.
También dar MTU a usar::

  pppoe-server -C {nombre_del_servidor} -L {IP interfaz} -p /etc/ppp/direcciones
  -I {interfaz} -m 1412


Cliente PPPoE
-------------

Seguir los pasos que te pide ``pppoeconf``.

IPv6
----

Automáticamente toma una dirección link-local y toma una dirección global si
recibe un RA.

Para hacer ping a una link-local hay que dar la interfaz::

  ping6 {IPv6}
  ping6 {IPv6 link-local} -I {interfaz}

.. todo:: Ver si eso de dar la interfaz pasa por algo de linux o porque ipc6
   funciona asi

iptables
--------

Ir a :doc:`iptables`.
