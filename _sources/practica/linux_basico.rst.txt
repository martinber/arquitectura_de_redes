Linux básico
============

Acá están los comandos más básicos para redes en Linux. Creo que está
actualizado (2018) ya que hay muchos programas que cambiaron (``ip`` reemplaza a
``ifconfig`` y ``route``, ``iw`` reemplaza a ``iwconfig``, etc.).

Lista de comandos o archivos:

- Activar y desactivar interfaces. Direcciones IP, MAC, Gateway:

  - Temporalmente: ``ip link``, ``ip address`` e ``ip route``.

  - Permanentemente: ``/etc/network/interfaces``.

- DHCP:

  - Temporalmente: ``dhclient``.

  - Permanentemente: ``/etc/network/interfaces``.

- DNS: ``/etc/resolv.conf``.

- WiFi:

  - Conexión, información: ``iw``.

  - Autenticación con WPA: ``wpa_supplicant``.

Para deshabilitar cosas que molestan::

  sudo service network-manager stop
  sudo service wpa-supplicant stop

Varios
------

Sobre el comando ip
~~~~~~~~~~~~~~~~~~~

- El comando ip tiene varias secciones (que se llaman objetos en el manual),
  como ``ip address``, ``ip route`` o ``ip link``.

- Según el manual el comando es: ``ip [ OPTIONS ] OBJECT { COMMAND | help }``.

  - La opción ``-c`` agrega color a la salida.

  - El objeto es algo como ``address`` o ``link``.

  - El comando es el resto de las cosas que se escriban.

  - La ubicación de las opciones importa, siempre van antes del objeto.

  - El comando ``help`` es útil. (ej: ``ip address help``).

  - El objeto se puede abreviar, es lo mismo ``address``, ``addr`` o
    simplemente ``a``.

  - Otra opción útil es ``-s`` que agrega más información (estadísticas según
    veo)

Sobre /etc/network/interfaces
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Es un archivo de configuración que es leído cada vez que se inicia el sistema o
cada vez que se conecta un cable ethernet. Esto se usa para configuraciones
permanentes.

Sobre la nueva nomenclatura de las interfaces
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Antes las interfaces de red se llamaban ``ethX`` en las cableadas, y ``wlanX``
en las inalámbricas. Estaba el problema de que no eran nombres permanentes,
entonces la interfaz que ahora es ``eth1`` mañana podría bootear en ``eth0``.

Entonces ahora las interfaces se nombran distinto. Primero van dos letras que
dicen el tipo de interfaz y lo que viene después dice la ubicación.

Prefijo::

  en: Ethernet
  wl: WLAN

Ubicación (puede ser cualquiera de estas formas, lo que está entre ``<>`` es
reemplazado por un número, lo que está entre ``[]`` es opcional)::

  b<number>
  c<bus_id>
  o<index>[n<phys_port_name>|d<dev_port>]
  s<slot>[f<function>][n<phys_port_name>|d<dev_port>]
  x<MAC>
  [P<domain>]p<bus>s<slot>[f<function>][n<phys_port_name>|d<dev_port>]

La forma más común creo que es ``p<bus>s<slot>``, por ejemplo ``p3s4``, que
junto con el prefijo sería ``enp3s4``.

ip link
-------

Sirve para configurar las interfaces.

- **ip -c link**

  Muestra información sobre las interfaces, por ejemplo::

    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    2: enp4s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DEFAULT group default qlen 1000
        link/ether 08:9e:01:43:f2:eb brd ff:ff:ff:ff:ff:ff
    3: wlp9s0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN mode DORMANT group default qlen 1000
        link/ether 76:bb:9f:94:a1:6e brd ff:ff:ff:ff:ff:ff

  Se puede ver si las interfaces están ``state UP`` o ``state DOWN`` (el ``UP``
  que está dentro de los ``<>`` no sé que significa).

- **ip -c link set enp4s0 down**

  Para poner la interfaz en ``UP`` o ``DOWN``.

- **ip -c link set enp4s0 address 12:34:56:78:90:ab**

  Cambiar la MAC de la interfaz, la interfaz primero debe estar en ``DOWN``.

ip address
----------

Sirve para manejar las direcciones IP.

- **ip -c address**

  Muestra información sobre las direcciones MAC e IP. Por ejemplo::

    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
           valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
           valid_lft forever preferred_lft forever
    2: enp4s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
        link/ether 12:34:56:78:90:ab brd ff:ff:ff:ff:ff:ff
        inet 192.168.1.33/24 brd 192.168.1.255 scope global dynamic enp4s0
           valid_lft 862983sec preferred_lft 862983sec
        inet6 fe80::b8a9:1d32:6da6:a5e5/64 scope link
           valid_lft forever preferred_lft forever
    3: wlp9s0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN group default qlen 1000
        link/ether 5e:9a:87:df:3d:80 brd ff:ff:ff:ff:ff:ff

  Se puede ver lo mismo que con ``ip -c link`` pero además las direcciones IP.

- **ip -c address flush dev wlp9s0**

  Borra todas las IPs de una interfaz. Suele borrar las rutas y uno debe
  configurar el gateway de nuevo.

- **ip -c address del 192.168.0.103/24 dev wlp9s0**

  Borra una IP configurada. Suele borrar las rutas y uno debe configurar el
  gateway de nuevo.

- **ip -c address add 192.168.0.50/24 dev wlp9s0**

  Agrega una dirección IP junto con su máscara de red.

ip route
--------

  Configura las rutas.

- **ip -c route**

  Muestra las rutas configuradas.

- **ip -c route flush dev wlp9s0**

  Borra todas rutas de una interfaz

- **ip -c route del default**

  Borra el gateway

- **ip -c route add default via 192.168.0.1**

  Configura el gateway

dhclient
--------

Configura las direcciones automáticamente usando DHCP.

- **dhclient wlp9s0**

  Configura la interfaz usando DHCP, previamente se deben borrar todas las rutas
  y direcciones asociadas a esa interfaz con::

    sudo ip -c route flush dev wlp9s0
    sudo ip -c address flush dev wlp9s0

/etc/network/interfaces
-----------------------

Este archivo configura las interfaces en el inicio del sistema o cuando se
conecta un cable ethernet.

Para definir una interfaz primero usamos ``auto`` o ``allow-hotplug`` junto con
el nombre de la interfaz. la palabra ``auto`` hace que se configure al inicio
del sistema, en cambio ``allow-hotplug`` además configura la interfaz cuando se
conecta un cable.

Ejemplo por DHCP::

  auto lo
  iface lo inet loopback

  allow-hotplug enp4s0
  iface enp4s0 inet dhcp

Ejemplo IP estática::

  auto lo
  iface lo inet loopback

  allow-hotplug enp4s0
  iface eth0 inet static
      address 192.168.0.50
      broadcast 192.168.0.255
      netmask 255.255.255.0
      gateway 192.168.0.1

/etc/resolv.conf
----------------

Para cambiar el DNS, hay que modificar el archivo /etc/resolv.conf con
cualquier editor (como nano). Por ejemplo para usar los DNS de Google::

  nameserver 8.8.8.8
  nameserver 8.8.4.4

Puede que el archivo tenga una advertencia::

  # Dynamic resolv.conf(5) file for glibc resolver(3) generated by resolvconf(8)
  #     DO NOT EDIT THIS FILE BY HAND --
  #     YOUR CHANGES WILL BE OVERWRITTEN

Si esa advertencia está en el archivo los cambios se van a sobreescribir en un
rato. Eso pasa porque este método está cambiando en los Ubuntus, tienen un nuevo
comando que se llama ``resolvconf`` que parece que va a manejar al archivo
``/etc/resolv.conf``.

En esos casos se puede especificar los DNS en ``/etc/network/interfaces`` como
se puede ver en este ejemplo::

  auto lo
  iface lo inet loopback

  allow-hotplug enp4s0
  iface enp4s0 inet static
          address 192.168.0.50
          broadcast 192.168.0.255
          netmask 255.255.255.0
          dns-nameservers 8.8.8.8 8.8.4.4
          gateway 192.168.0.1

La otra opción es agregar las líneas al archivo
``/etc/resolvconf/resolv.conf.d/base`` como si se tratara de
``/etc/resolv.conf``. Hay una advertencia que dice que no hay que editar el
archivo pero hay que ignorarla, este archivo es copiado a ``/etc/resolv.conf``
automáticamente y esa advertencia se aplica a ``/etc/resolv.conf``. Finalmente
hay que actualizar el archivo con::

  sudo resolvconf -u

iw
--

Maneja las conexiones WiFi. ``man iw`` muestra muy poca ayuda, conviene ``iw
help``.

- **iw dev wlp9s0 info**

  Ver información sobre la interfaz.

- **iw dev wlp9s0 link**

  Ver información sobre la conexión.

- **iw dev wlp9s0 disconnect**

  Desconectarse. Me pasó que se vuelve a conectar solo segundos después, tuve
  que deshabilitar el servicio ``wpa_supplicant``.

- **iw dev wlp9s0 set type ibss**

  Configurar el modo a usar, puede ser ``managed`` (lo común) o ``ibss`` (para
  redes ad-hoc) No probé las redes ad-hoc, siempre usé ``managed``.

- **iw dev wlp9s0 scan**

  Escanear AP disponibles. La salida es MUY larga y no sé si hay alguna opción
  para tener información resumida.

  Si solamente se necesitan los nombres de las redes se puede usar ``iw dev
  wlp9s0 scan | grep SSID``.

- **iw dev wlp9s0 connect WiFi_Pedrito**

  Conectarse a un AP sin seguridad.

  Una vez me pasó que no se conectaba, se arregló cambiando el modo a ``ibss`` y
  a ``managed`` de vuelta. No se por qué.

- **iw dev wlp9s0 connect WiFi_Pedrito key 0:password**

  Conectarse a un AP con seguridad WEP (la contraseña es ``password``). Nunca
  probé este comando. Para conectarse a un AP con WPA ver ``wpa_supplicant``
  abajo.

wpa_supplicant
--------------

Sirve para conectarse a APs con WPA o WPA2. Reemplaza al comando ``iw dev wlp9s0
connect WiFi_Pedrito key 0:password`` que servía para WEP.

::

  wpa_passphrase "WiFi_Pedrito" "password" > temp_wpa
  sudo wpa_supplicant -B -i wlp9s0 -c temp_wpa

El primer comando calcula unas claves a partir del SSID y la contraseña, al
resultado lo guarda en el archivo ``temp_wpa``. Este archivo es usado por el
comando siguiente.

El segundo comando se conecta al AP especificado en el archivo recién creado. El
comando queda corriendo para siempre, la opción ``-B`` hace que el comando corra
en el fondo como un daemon.

Hay que acordarse de borrar el archivo ``temp_wpa`` después. Para cortar la
conextión hay que matar a ``wpa_supplicant``. Cuando éste haya sido iniciado
como daemon se puede usar::

  killall wpa_supplicant
