Gestion
=======

SNMPD en linux
--------------

::

  sudo apt install snmp snmpd

Editar ``/etc/snmp/snmpd.conf``

Cambiar puerto e IP para escuchar desde localhost::

  agentAddress udp:127.0.0.1:106

Cambiar puerto e IP para escuchar desde todos lados::

  agentAddress udp:0.0.0.0:106

Ver todo desde localhost::

  rocommunity public localhost

Ver ``systemonly`` desde localhost::

  rocommunity public localhost -V systemonly

Ver todo desde fuera::

  rocommunity public default

Ver ``systemonly`` desde fuera::

  rocommunity public localhost -V systemonly

Reiniciar::

  sudo service restart snmpd

SNMP en linux
-------------

::

  snmpget -c public -v 2c 127.0.0.1 1.3.6.1.2.1.1.2.0
  snmpwalk -c public -v 2c 127.0.0.1 1.3.6.1.2.1.1
  snmpgetnext
  snmpgetbulk
  snmpset

.. todo:: Terminar

Cacti
-----
