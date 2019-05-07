FTP
===

File Transfer Protocol.

- Va sobre TCP.

- Permite la transferencia de archivos bidireccional entre un cliente y un
  servidor.

- La sesión se establece de la misma forma que una sesión Telnet. Después se
  hace otra conexión para la transferencia de datos.

.. todo:: FTP Anonimo

Modos
-----

FTP puede usar dos modos de conexión:

- **Activo**: El cliente se conecta al puerto 21 del servidor para establecer la
  sesión de comandos, luego el cliente envía el comando ``PORT`` indicando un
  número de puerto aleatorio al cuál el servidor se conectará (por ej 2056) para
  establecer la conexión para la transferencia de datos. El problema de este
  modo es que generalmente el usuario tiene un firewall y NAT, por lo tanto el
  servidor no puede conectarse al cliente.

- **Pasivo**: El cliente se conecta al puerto 21 del servidor para establecer la
  sesión de comandos, luego el cliente envía el comando ``PASV`` y el servidor
  responde con un número de puerto aleatorio al cual el cliente se conectará
  para establecer la conexión para la transferencia de datos.

.. image:: ./ftp_activo.svg

.. image:: ./ftp_pasivo.svg

Gabiwxp [`CC BY-SA 3.0 <https://creativecommons.org/licenses/by-sa/3.0>`_]

Comandos
--------

No son todos obligatorios, hay algunos que no están disponibles en todas las
implementaciones. Estos son los comandos más básicos:

- **USER**: Se envía como argumento el nombre de usuario.

- **PASS**: Se envía la contraseña en texto plano.

- **PORT**: Se envía como argumento una IP y puerto del host para iniciar la
  conexión activa, el servidor luego se conectará a esa IP y puerto para iniciar
  la conexión para la transferencia de datos.

- **PASV**: El cliente solicita al servidor un puerto para iniciar una conexión
  pasiva, el cliente luego se conectará al puerto que indique el servidor para
  iniciar la conexión para la transferencia de datos.

- **CWD** (Change Working Directory): Cambia el directorio actual en el
  servidor.

- **MKD** (Make Directory): Crea un directorio en el servidor.

- **DELE**: Elimina un archivo en el servidor.

- **LIST**: Solicita una lista de archivos disponibles en el directorio
  especificado.

- **RETR**: Solicita al servidor la transferencia de un archivo.

- **STORE**: Solicita al servidor a aceptar los datos enviados por la conexión
  de datos y a almacenarlos en el nombre de archivo dado.

- **QUIT**: Cierra la conexión.

Respuestas
----------

Algunas de las respuestas son:

- ``1XX``: La operación se ha iniciado correctamente.

  - ``150``: Iniciando transferencia de archivos.

- ``2XX``: La operación se ha completado correctamente.

  - ``200``: Comando OK.

  - ``211``: Estado del sistema.

  - ``212``: Estado de directorio.

  - ``213``: Estado de archivo.

  - ``220``: Servicio OK.

  - ``221``: Cerrando conexión de control.

  - ``226``: Transferencia de archivo correcta.

  - ``227``: Entrando a modo pasivo, lleva como argumento IP y puerto.

  - ``230``: Sesión iniciada.

- ``3XX``: El comando ha sido aceptado pero se necesita más información.

  - ``331``: Usuario correcto, contraseña necesaria.

- ``4XX``: Falla temporal.

  - ``450``: Archivo no disponible: archivo ocupado.

- ``5XX``: Falla permanente.

  - ``500``: Error de sintaxis, comando no reconocido.

  - ``501``: Error de sintaxis en los argumentos.

  - ``502``: Comando no implementado.

  - ``504``: Comando no implementado para ese argumento.

  - ``550``: Archivo no disponible: archivo no encontrado.

Variaciones
-----------

- TFTP: Sin usuario, contraseña, nada.

- SFTP:

- FTPS:

.. todo:: Ver variaciones de FTP.

TFTP
----

