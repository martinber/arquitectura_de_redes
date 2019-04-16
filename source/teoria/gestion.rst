Gestión de redes
================

.. todo::

   Hacer y revisar

   - Monitoreo

   - SNMP

   - Gestión remota

- Hay dos estándares: SNMP y OSI.

- Aplicación de gestión de red

- Entidad de administración de red: Envía información

- MIB: Management Information Base

OSI
---

Áreas funcionales
~~~~~~~~~~~~~~~~~

- Administración de la contabilidad: Proporciona información sobre costos y los
  usos contables.

- Administración de la configuración: Administra la configuración real de la
  red.

- Administración de fallos: Detecta, aísla y corrige los fallos, incluyendo el
  mantenimiento de un registro y un diagnóstico de los errores.

- Administración del desempeño (prestaciones): Mantiene una eficiencia y un
  desempeño máximos, incluyendo la recopilación de estadísticas y el
  mantenimiento de registros para definir un “baseline”. Para ver si el tráfico
  es anormalmente alto o bajo.

- Administración de la seguridad: Mantiene un sistema seguro y administra el
  acceso.

SNMP
----

Simple Network Management Protocol.

- Todos los equipos administrables tienen MIB y usan SNMP.

- La MIB tiene estructura SMI.

- Puede informarse por consulta o evento extraordinario.

- Usamos NetFlow, Nagios y Cacti, se complementan.

- Puerto IDP 161 o 162.

- Es importante en las nuevas versiones que se autentique para mejor seguridad.

.. todo:: Ver versiones. Ver diferencias entre NetFlow, Nagios, Cacti.

MIB
~~~

- Se usa MIB-II, versión 2. Y SMI.

- Es jerárquica en vez de relacional, en forma de árbol.

- El formato de SMI es Abstract Syntax Notation One (ASN.1). Entonces es
  independiente del dispositivo, lenguaje, etc. Define los tipos de variables
  entre otras cosas.

- Se puede acceder en formato ASCII o por números.


Comandos
~~~~~~~~

- get

  - request

  - next request

  - bulk request

  - response

- set

  - request

  - next request

- trap

- inform request

.. todo:: Agregar primitivas TRAPS (coldstart, warmstart, etc.)

.. todo:: Ver System, AT, TCP, UDP, EGP, etc.
