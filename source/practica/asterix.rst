Asterix
=======

.. todo:: Hacer

Para iniciar/parar el servidor::

  service asterix status
  service asterix start
  service asterix stop
  service asterix restart
  service asterix reload

Los usuarios pueden estar en más de un contexto al mismo tiempo.

Tipos de usuario
----------------

- user: Solo llamadas salientes
- peer: Solo llamadas entrantes
- friend: Entradas salientes y entrantes


Otras opciones para el usuario
------------------------------

- ``canreinvite``: Permite que las conexiones RTP de audio ocurran punto a punto
  entre los usuarios, si se desactiva pasan por el servidor, si se activa
  generalmente va por el servidor pero generalmente va punto a punto.

- ``qualify``: Algo del retardo, no se.

- ``nat``: Indica si el usuario esta atrás de un NAT.
