General
=======

.. todo:: Reordenar y poner cosas en otros documentos. Ver como separar IPV4 de
   IPV6.

Capas
-----

1. Capa física.

  - Unidad: Símbolos, Bits.

2. Capa de enlace.

  - Unidad: Trama.

  - Protocolos: Ethernet, PPP, HDLC, PPPoE (aunque esté dentro de Ethernet).

3. Capa de red.

  - Unidad: Paquete.

  - Protocolos: IP, ARP, ICMP (aunque esté dentro de IP).

4. Capa de transporte.

  - Unidad: Segmento (TCP), Datagrama (UDP).

  - Protocolos: TCP, UDP

5. Capa de aplicación.

  - Unidad: Datos

  - Protocolos: DHCP, HTTP.

+-----------------+------------+-----------------+
| OSI             | TCP/IP     | Lo que usamos   |
+=================+============+=================+
| 7. Aplicación   |            |                 |
+-----------------+            |                 |
| 6. Presentación | Aplicación | 5. Aplicación   |
+-----------------+            |                 |
| 5. Sesión       |            |                 |
+-----------------+------------+-----------------+
| 4. Transporte   | Transporte | 4. Transporte   |
+-----------------+------------+-----------------+
| 3. Red          | Internet   | 3. Red          |
+-----------------+------------+-----------------+
| 2. Enlace       | Acceso a   | 2. Enlace       |
+-----------------+ la red     +-----------------+
| 1. Física       |            | 1. Física       |
+-----------------+------------+-----------------+

Tamaños
-------

- Tamaños de cabeceras

  - Ethernet:

    - Sin contar preámbulo ni CRC: 14 bytes.

    - Sin contar preámbulo y contando CRC: 18 bytes.

    - Contando preámbulo y CRC: 26 bytes.

    - Si incluye tags (VLAN): + 4 bytes.

  - ARP: 28 bytes.

  - IP: 20 bytes.

  - ICMP: 8 bytes.

  - PPPoE: 6 bytes.

  - PPP:

    - Sin contar flags (delimitadores) ni FCS: 4 bytes.

    - Sin contar flags (delimitadores) pero sí FCS: 6 bytes.

    - Contando flags (delimitadores) y FCS: 8 bytes.

    - Dentro de PPPoE solo lleva el campo Protocol: 2 bytes.

  - DHCP:

    - Sin contar SNAME, ni FILE, ni opciones: 44 bytes.

- Tamaños de tramas:

  - Longitud total máxima de datos que puede llevar Ethernet es 1500 bytes, que
    sumado al header, CRC y preambulo Ethernet dan 1526 bytes por trama.

  - Restando los 20 bytes de header IP, la cantidad máxima que puede llevar IP
    es 1480 bytes. Esto es suponiendo que no hay opciones en el header, en tal
    caso éstas opciones pueden ocupar hasta 32 bytes más.

  - Las tramas Ethernet que llevan un poco más de 1500 bytes se llaman **baby
    giant**.

  - Los **jumbo frames** Ethernet llevan hasta 9000 bytes de datos, son
    soportados por placas Gigabit, pero la mayoría de los ISP no los aceptan.

  - PPPoE:

    - PPPoE va dentro de Ethernet, después del header PPPoE va el header PPP y
      después va el paquete IP. Por lo tanto, si el MTU de Ethernet es 1500, lo
      máximo que puede llevarse de datos es ``1500 - 6 - 2 = 1492 bytes``.

    - Dentro de PPPoE va IP, por lo tanto hay que restar 20 bytes más para saber
      cuántos bytes entran en IP: 1472 bytes.

  - Al usar VLAN, el header Ethernet aumenta en 4 bytes pero sigue teniendo un
    MTU de 1500.


Diagramas de flujo
------------------

ICMP con primero un ARP::

    192.168.0.100                  192.168.0.102                  broadcast

                                    ARP request
                 ------------------------------------------------>
                     ARP reply
                 <----------------
                    ICMP request
                 ---------------->
                    ICMP reply
                 <----------------
                    ICMP request
                 ---------------->
                    ICMP reply
                 <----------------


Internet
--------

Algunas definiciones:

- ISP: Internet Service Provider.

- NSP: National Service Provider.

- NAP: Network Access Point.

- CABASE: Cámara Argentina de Internet.

- ICANN: Internet Corporation for Assigned Names and Numbers, coordina la
  asignación de DNS, direcciones IP y sistemas autónomos.

- IANA: Internet Assigned Numbers Authority, es la autoridad que asigna nombes
  de dominio, direcciones IP y sistemas autónomos. En este momento a esta
  función la posee el ICANN.

- RIR: Regional Internet Registry, organización que asigna direcciones IP y
  números de sistemas autónomos en una región:

  - ARIN: Estados Unidos, Canadá.

  - RIPE NCC: Europa, medio oriente y Asia central

  - APNIC: Asia y Pacífico.

  - LACNIC: Latin American and Caribbean Internet Addresses Registry.

  - AfriNIC: África.

- AS: Sistema Autónomo, un conjunto de routers y redes bajo una misma
  administración, se identifican por un número de 16 bits.

- IETF: Internet Engineering Task Force, se encarga de crear los estándares RFC.

- RFC: Request For Comments, para que un protocolo se estandarice tiene que
  estár publicado en una RFC, pero no todos los RFC son estándares. Son
  publicados por la IETF.

Serial
------

Se puede usar HDLC o PPPoE. El cable tiene dos extremos:

- DSE: (Hembra), Configura velocidad.

- DTE: (Macho).

Resumen headers
---------------

Para comparar un poco los tamaños, todos ocupan 1 byte por renglón, excepto DHCP
que es tan grande que lo dibujé a 4 bytes por renglón.

.. image:: ./headers.jpg

Ruteo
-----

- Una ruta es un prefijo más el próximo salto.

- Hay dos funciones básicas:

  - RIB (Routing Information Base): Para determinación de ruta.

  - FIB (Forwarding Information Base): Para conmutación de paquetes, la
    información que tiene deriva de las mejores rutas de la RIB.

- Las tablas de ruteo indican el próximo salto. Si hay varias rutas que
  contienen a la dirección de destino, se elige la ruta que tiene la máscara más
  larga.
  
- Si tienen la misma longitud de máscara se elige la ruta con menor distancia
  administrativa que depende del protocolo desde donde se aprendió, sólo tiene
  significado local.

- Los algoritmos clasifican las rutas de acuerdo a métricas, que son números
  generados a partir de variables como cantidad de saltos, velocidad de enlaces,
  etc. Si dos rutas coinciden en largo de máscara y en distancia administrativa,
  se elige la que tiene menor métrica. Se usa cuando hay dos rutas aprendidas
  con el mismo protocolo.

- Pueden ser protocolos

Ruteo dinamico
~~~~~~~~~~~~~~

- Pueden ser IGP (Interior Gateway Protocol) o EGP (External Gateway Protocol)
  dependiendo de si es interno a un sistema autónomo o no. El único EGP es BGP.

- La distancia administrativa (grado de conocimiento y confiabilidad) depende
  del protocolo, tiene un valor por defecto pero se puede cambiar:

  - RIP: 120.

  - Redes directamente conectadas: 0.

  - Rutas estáticas: 1.

  - eBGP: 20.

  - OSPF: 110.

  - IS-IS: 115.

  - iBGP: 200.

- Un protocolo elige entre distintos caminos para llegar a un destino mirando
  las métricas.

- Si hay una ruta aprendidas por más de un protocolo, se elige las ruta con
  menor distancia administrativa.

- Por como funcionan los sistemas operativos de routers, en protocolos como RIP
  o OSPF hay dos formas de propagar rutas directamente conectadas: usando
  comandos como ``network`` en todas las redes y pasivando algunas si es
  necesario, o usando ``network`` en las activas y anunciando las demás con un
  comando como ``redistribute connected``.

Route redistribution
~~~~~~~~~~~~~~~~~~~~

- Para propagar rutas de un protocolo de ruteo dinamico a otro protocolo se usa
  *Route Redistribution*, no es un estándar y varía un poco dependiendo de los
  fabricantes.

- En el caso de que hayan dos routers de borde, pueden ocurrir loops. Por lo
  tanto una ruta recibida desde un protocolo no debe ser reinyectada en el mismo
  protocolo.

- Creo que este tema es complejo cuando se desistribuye entre dos IGP. En cambio
  cuando se resistribuye entre BGP y un IGP debería ser más estándar y fácil.
