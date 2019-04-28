Acceso remoto
=============

.. todo::

   - Telnet

   - Rlogin

   - SSH

Telnet
------

Teletype Network.

- Se ubica sobre TCP y usa el puerto 23.

- Permite a un cliente acceder a los recursos de un servidor abriendo una
  terminal virtual remota allí.

- Se envían caracteres ASCII de 7 bits, El octavo bit es de control y se usa
  sólo en los comandos, pero tengo entendido que en la práctica se pueden enviar
  los 8 bits tranquilamente.

- Hay muchos comportamientos y detalles que según entiendo varían entre
  implementaciones, solamente explico las ideas básicas.

- Es inseguro ya que ni las contraseñas ni los datos son cifrados, por lo tanto
  actualmente se usa SSH en su lugar.

- Cuando se establece la conexión, ambos extremos asumen que debe generarse una
  NVT (Network Virtual Terminal). También se asumen algunas cosas como que por
  ejemplo el servidor haga eco de lo recibido (teclas presionadas) para que sea
  mostrado en la terminal virtual del cliente.

- Define algunos comandos especiales que se envían luego del carácter `0xFF`
  llamado IAC (Interpret As Command), algunos de los comandos posibles son:

  - IP (Interrupt Process): Indica que el usuario quiere interrumpir unterrmpir
    el proceso en ejecución (por ejemplo al hacer Ctrl-C).

  - AYT (Are You There): Solicita al otro extremo respuesta para asegurarse de
    que el sistema sigue en funcionamiento.

  - EC (Erase Character): Elimina el último caracter en la línea actual.

  - EL (Erase Line): Elimina la línea actual.

- Se suele utilizar el comando `telnet` para hacer una conexión TCP como si se
  tratase de *netcat* ya que la única diferencia con TCP puro es la posibilidad
  de enviar comandos luego del carácter IAC.

rlogin y rsh
------------

Remote Login y Remote Shell.

- rlogin provee mejoras respecto de telnet ya que se aprovecha del hecho que
  asume que ambos extremos son sistemas operativos UNIX.

- rsh se incluye en el paquete de rlogin y permite iniciar una shell en el
  sistema remoto sin necesidad de iniciar una sesión allí y recibir los
  resultados.

SSH
---

Secure Shell.

- Va sobre TCP puerto 22.

- Las contraseñas y los datos son cifrados antes de su transmisión.

- Permite el uso de claves públicas y privadas para la autenticación,
  permitiendo por ejemplo al cliente comprobar la autenticidad del servidor
  antes de conectarse.
