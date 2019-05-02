Seguridad
=========

Detección de Intrusos
---------------------

- IDS (Intrusion Detection System): Analiza tráfico o sistemas para detectar
  intrusiones y generar alertas pero no actúa. Puede ser NIDS (Network Intrusion
  Detection System) si es un sniffer que monitorea la red, o puede ser un HIDS
  (Host Intrusion Detection System) que analiza los archivos y logs del sistema.

- IPS (Intruder Protection System): Es como IDS pero actúa.

- Ver también :doc:`tls`.

Cifrado
-------

Simétrico
~~~~~~~~~

También se llaman algoritmos de clave privada. Se basan en claves de cifrado y
descifrado secretas e iguales (o diferentes con una transformación simple entre
ellas).

Algoritmos más conocidos:

- DES (Data Encryption Stamdard), 1975: Inseguro a fuerza bruta por usar claves
  chicas.

- IDEA (International Data Encryption Algorithm), 1991: Conviene pasarse a AES?.

- 3DES (Triple Data Encryption Standard), 1998: También llamado TDEA (Triple
  Data Encryption Algorithm). Bastante seguro, pero conviene pasarse a AES.

- AES (Advanced Encryption Standard), 1998: El más usado actualmente.

Asimétrico
~~~~~~~~~~

También se llaman algoritmos de clave pública. Se utilizan claves públicas y
privadas, las claves privadas son secretas mientras que las claves públicas se
derivan de las claves privadas y pueden ser mostradas libremente. La clave
privada no puede ser derivada a partir de la clave pública.

Mensajes cifrados con la clave pública sólo pueden ser descifrados con la clave
privada, permitiendo comunicación confidencial en un sentido. Si se cifra un
mensaje con la clave privada, es posible verificar con la clave pública que el
que envía el mensaje posee la clave privada. Es una firma digital.

PKI (Public Key Infraestructure) es un mecanismo mediante terceras partes
llamadas autoridades de certificado (CAs) certifican la autenticidad de un
usuario de un par de claves.

La criptografía de curva elíptica es una variante de la criptografía asimétrica
que se basa en las matemáticas de las curvas elípticas. Es más rápida y requiere
de claves más cortas.

Algoritmos más conocidos (creo que son todos seguros):

- DH (Diffie-Hellman), 1977.

- RSA (Rivest–Shamir–Adleman), 1977.

- DSA (Digital Signature Algorithm), 1994.

- ECDSA (Elliptic Curve Digital Signature Algorithm): Variante de DSA que usa
  criptografía de curva elíptica).

Hash
----

Es una función que toma una cierta cantidad de datos y como salida tiene una
longitud fija de bits. Es muy difícil encontrar dos entradas que presenten una
misma salida, o a partir de una salida encontrar una posible entrada.

- MD5: Inseguro.

- SHA-1: Seguro, recientemente se logró encontrar una colisión utilizando
  bastante fuerza bruta.

- SHA-2: Seguro y muy usado, tiene variantes SHA-256, SHA-512, etc.

- SHA-3: Seguro pero no muy usado todavía.

- RIPEMD: Segura en sus variantes mayores a RIPEMD-160.

Certificados
------------

Es un vínculo entre una clave pública y una identidad de usuario. Se consigue a
partir de una firma digital por una tercera parte (autoridad de certificación o
CA). Todos confían en la clave pública del CA.

El certificado raíz es un certidicado emitido por la CA para sí misma y se
utiliza para comprobar certificados emitidos por ella.

X.509v3 es el protocolo o certificado que se utiliza, contiene el nombre de la
CA, el nombre del usuario, clave pública del usuario, tiempo de validez, etc. Se
cifra con la clabe privada del CA y por lo tanto se descifra con la clave
pública del CA.

Si el certificado no es firmado por una CA y en su lugar es firmado por la misma
identidad que certifica se denomina *self-signed*. En tal caso generalmente no
se puede determinar la autenticidad.

RADIUS
------

Remote Authentication Dial-In User Server.

- Usa UDP puerto 1813 o TCP con TLS.

- Autentica usuarios que se conectan a una red WiFi, a PPP, a VoIP, etc.

- El dispositivo que controla el acceso a algún recurso protegido se llama NAS
  (Network Access Server). El usuario solicita al NAS acceso al recutso usando
  credenciales, luego el NAS envía las credenciales al servidor RADIUS usando el
  protocolo RADIUS y éste decidirá si el acceso será permitido o si se requerirá
  información adicional al usuario.

IPSec
-----

- Es un conjunto de protocolos de seguridad que agrega cifrado y autenticación a
  IP (capa 3).

- Se puede usar a través de LAN, WAN o Internet.

Modo tranporte
~~~~~~~~~~~~~~

- Cifra los datos de IP.

- Se agrega una cabecera IPSec entre la cabecera IP y los datos (que irán
  cifrados).

- Si se usa el encabezado de autenticación, las IPs no pueden ser modificadas
  por NAT porque esto invalida el hash. Pero existe el mecanismo NAT-T que
  permite atravesar NAT.


Modo túnel
~~~~~~~~~~

- Cifra la cabecera IP y sus datos.

- Se agrega una cabecera IP de Túnel y una cabecera IPSec antes de la cabecera
  IP cifrada y de los datos cifrados.

- Permite hacer VPNs.

Tipos de firmas
----------------

- Digital: Es una firma que se basa en PKI (Public Key Infraestructure). Es
  fácil determinar que es auténtica y no es repudiable ya que sólo puede ser
  generada por el propietario. Utiliza criptografía asimétrica, se obtiene a
  partir de aplicar la una clave privada en el hash de un mensaje y se comprueba
  con la clave pública.

- Electronica: Es un concepto más general que la firma digital y abarca a otros
  métodos además de PKI. Generalmente la firma digital tiene validez legal
  mientras que la firma electrónica puede que no.

- Digitalizada: Es como cuando firmas en una tablet para el carnet.

Firewall
--------

Ver :doc:`../practica/iptables` que tiene mucho de teoría también.

- Puede ser basado en software o hardware.

- Tipos:

  - Filtros de paquetes: Mira IPs, puertos, estado de la conexión TCP, protocolo
    .de aplicación.

.. todo:: Firewall

Perfect Forward Secrecy
-----------------------

Es un concepto usado en la criptografía. Que un protocolo como TLS asegure PFS
(Perfect Forward Secrecy) significa que:

- Las claves de sesión no serán comprometidas incluso si la clave privada del
  servidor es comprometida.

- Sesiones pasadas no pueden ser descifradas en el caso que en el futuro se
  comprometan claves.

VPN
---

- Consiste en simular una red provada a través de una red pública.

- Generalmente se extienden redes LAN a través de Internet.

- Generalmente se supone que además se provee seguridad y cifrado.

- Se basa en crear túneles entre puertas de acceso.
