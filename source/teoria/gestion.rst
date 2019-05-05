Gestión de redes
================

FCAPS
-----

Fault, Configuration, Accounting, Performance, Security.

La ISO definió el modelo OSI y también el modelo FCAPS que especifica las áreas
que debe satisfacer un método de gestión de redes, pero no es un protocolo en
sí.

Áreas funcionales
~~~~~~~~~~~~~~~~~

- Fallos: Detecta, aísla y corrige los fallos, incluyendo el mantenimiento de un
  registro y un diagnóstico de los errores. SNMP es un protocolo que permite
  notificar de fallos.

- Configuración: Administra la configuración de la red, coordinando cambios de
  hardware y software.

- Contabilidad: Reunir estadísticas para tarifar a los usuarios. Para redes no
  tarifadas se reemplaza este punto por administración, que sólo lleva la cuenta
  de usuarios y permisos. Ejemplos de protocolos son RADIUS, TACACS y DIAMETER.

- Rendimiento: Mantiene una eficiencia y un desempeño máximos, incluyendo la
  recopilación de estadísticas y el mantenimiento de registros para definir un
  umbral y alertas en el caso que el rendimiento sea anormal.

- Seguridad: Controla el accerso a los recursos de la red.

Definiciones
------------

- Management Information Base (MIB): Es una base de datos de objetos,
  configuraciones, parámetros, etc. Tiene formato SMI y cada objeto se
  identifica con un OID. Se suele separar en varios archivos y define la lista
  de objetos a utilizar para cada entidad, protocolo, etc. Se usa MIB-II.

- Object Identifier (OID), es un mecanismo de identificación de objetos. Cada
  objeto es un nodo en un árbol o jerarquía. Se puede representar a cada nodo
  por el camino desde la raíz hasta el nodo separado por puntos, por ejemplo
  ``1.3.6.1.4.1.343`` que es lo mismo que
  ``iso.identified-organization.dod.internet.private.enterprise.intel``.

- Structure of Management Information (SMI) es el formato que define los tipos
  de datos a utilizar para cada OID. Se deriva de Abstract Syntax Notation One
  (ASN.1). Por ejemplo define los tipos de objetos y cómo se codificarán para
  enviarlos por la red. Entonces es independiente del dispositivo, lenguaje,
  etc.

SNMP
----

Simple Network Management Protocol.

- Usa UDP puerto 161 y puerto 162 para traps.

- Se usa MIB-II.

- Recolecta y organiza información sobre los dispositivos de la red, también
  permite modificar la configuración.

- Los dispositivos a administrar son agentes y los que administran son
  *managers*.

- Permite a los managers solicitar y leer información desde los agentes o
  modificar alguna configuración en un agente.

- También permite el uso de *traps* que es una notificación enviada por un
  agente sin previa consulta desde un manager. Las *inform* requests son
  similares a los traps pero solicita al receptor que informe que ha llegado el
  mensaje, de lo contrario intenta de nuevo.

- SNMPv1 y SNMPv2 usan *communities* que es un estilo de contraseña en texto
  plano para determinar si el manager tiene permisos de lectura, lectura y
  escritura o de recepción de traps. SNMPv3 también usa string se comunidad pero
  permite autenticación y comunicación segura.

- Versiones, tengo entendido que lo más usado es SNMPv2c:

  - SNMPv1: Tiene muy poca seguridad.

  - SNMPv2: No utilizado porque el modelo de seguridad utilizado era
    controversial.

  - SNMPv2c: Similar a versión 1 pero permite contadores de 64 bit en lugar de
    32 bits y agrega el comando inform.

  - SNMPv2u: Intenta ofrecer un poco más de seguridad que SNMPv1 y SNMPv2c pero
    tampoco se usó.

  - SNMPv3: Solamente agrega seguridad.

- Ejemplos de software de administración son NetFlow, Nagios y Cacti, se
  complementan.

- Hay que tener cuidado de no congestionar la red.

- Remote Monitor (RMON) es una mejora de SNMP que permite utilizar sondas que
  funcionan como agentes que recopilan información y la transmiten
  periódicamente.

Grupos
~~~~~~

La MIB-II está definida en ``iso.org.dod.internet.mgmt.1`` o ``1.3.6.1.2.1``, a
partir de ahí existen varios grupos que contienen variables:

1. ``system``: Nombre del sistema, uptime, etc.

2. ``interfaces``: Tiene todas las interfaces, bytes enviados y recibidos,
   errores en paquetes, etc.

3. ``at``: Address Translation, no se utiliza.

4. ``ip``: Todos los aspectos de IP, incluyendo el ruteo.

5. ``icmp``: Errores de ICMP, paquetes descartados, etc.

6. ``tcp``: Estado de conexiones TCP entre otras cosas.

7. ``udp``: Estadísticas de UDP.

8. ``egp``: Estadísticas sobre EGP y tabla de lista de vecinos.

9. No usado.

10. ``transmission``: No tiene variables pero teien otros grupos dentro.

11. ``snmp``: Performance de la implementación de SNMP, cantidad de paquetes
    enviados y recibidos.

Comandos
~~~~~~~~

- GetRequest: Solicita al agente una variable o lista de variables.

- GetNextRequest: Solicita al agente la siguiente variable para permitir al
  manager atravesar el árbol de la MIB valor por valor.

- GetBulkRequest: Solicita múltiples iteraciones de GetNextRequest.

- SetRequest: Solicita al agente cambiar una variable o lista de variables.

- Response: Devuelve al manager variables y ACKs por los demás comandos.

- Trap: Permite al agente enviar un evento al manager sin una solicitud previa.
  Tienen varios tipos:

  - Coldstart (0): El agente se reinicia y su configuración puede cambiar.

  - Warmstart (1): El agente se reinicia y su configuración no puede cambiar.

  - LinkDown (2): Enlace caído.

  - LinkDown (3): Enlace levantado.

  - AuthenticationFailure (4): Recibido mensaje SNMP incorrectamente
    autenticado.

  - EgpNeighborLoss (5): Enlace BGP caído.

  - EnterpriseSpecific (6): Permite definir traps particulares.

- InformRequest: Es similar a un Trap pero solicita al manager un ACK, de lo
  contrario intenta de nuevo. También permite comunicación manager a manager.

Referencias
-----------

- https://en.wikipedia.org/wiki/Simple_Network_Management_Protocol

- https://en.wikipedia.org/wiki/Management_information_base

- https://blog.paessler.com/snmp-monitoring-via-oids-mibs

- https://docstore.mik.ua/orelly/networking_2ndEd/snmp/ch02_05.htm
