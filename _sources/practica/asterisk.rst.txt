Asterisk
========

.. todo:: Hacer

Para iniciar/parar el servidor::

  service asterisk status
  service asterisk start
  service asterisk stop
  service asterisk restart
  service asterisk reload

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

Configuración
-------------

Tiene un montón de archivos de configuración en ``/etc/asterisk``. Los que más
importan me parece que son:

- ``/etc/asterisk/asterisk.conf``: Es el archivo principal pero no tiene nada
  interesante.

- ``/etc/asterisk/extensions.conf``: Establece cómo serán manejadas y ruteadas
  las llamadas entrantes y salientes.

- ``/etc/asterisk/sip.conf``: Configuración para los canales SIP.

extensions.conf
~~~~~~~~~~~~~~~

Este archivo establece cómo serán manejadas y ruteadas las llamadas entrantes y
salientes. Es muy largo, tiene varias secciones:

- ``[general]``: Define algunas opciones generales.

- ``[globals]``: Acá se pueden definiar variables globales que se podrán usar en
  otros contextos.

- Las demás secciones son contextos, llevan el nombre entre corchetes.

Hay varias definiciones:

- Dialplan: Es el conjunto de contextos.

- Contexto: Permite separar funcionalidades, cada contexto tiene extensiones.

- Extensión: Es un conjunto de aplicaciones que se ejecutarán cuando el número
  de esa extensión es marcado.

- Aplicación: Es una acción.

Los usuarios se colocan en contextos (puede que en más de un contexto al mismo
tiempo), y los contextos pueden incluir a otros contextos.

La sintaxis de la extensión es simple pero se complejiza cuando se empiezan a
usar variables y ese tipo de cosas, pongo solamente la sintaxis general junto
con un ejemplo que llama al número 1234 cuando se marca 5000::

  exten => number,priority,application([parameter[,parameter2...]])
  exten => 5000,1,Dial(SIP/1234,20)

Una configuración normal sería la que pongo ahora::

  [general]
  ; por razones de seguridad
  static = yes
  writeprotect = yes
  comandos.

  [default]
  ; poner un echotest al llamar al numero 999
  exten => 999,1,Answer()
  exten => 999,2,Playback(demo-echotest)
  exten => 999,3,Echo()
  exten => 999,4,Playback(demo-echodone)
  exten => 999,5,Hangup()

  [internos]
  ; incluir al contexto default y permitir llamar a numeros que sean 1XX.
  include => default
  exten => _1XX,1,Dial(SIP/${EXTEN},30,Ttm)
  exten => _1XX,n,Hangup()

sip.conf
~~~~~~~~

Este archivo configura los canales SIP. Lo que nos interesa es registrar acá a
todos los usuarios. La sección ``[general]`` se aplica a todos los usuarios,
luego se pueden hacer templates para usuarios similares::

  [general]
  context=default
  udpbindaddr=0.0.0.0:5060 ; Escuchar en todas las IP, puerto 5060
  tcpbindaddr=0.0.0.0:5060 ; Escuchar en todas las IP, puerto 5060
  tcpenable=no
  transport=udp ; Transporte por defecto
  srvlookup=yes

  [estandar](!)
  type=friend
  context=internos
  disallow=all ; Deshabilitar todos los codecs
  allow=ulaw ; Habilitar codec
  allow=gsm
  host=dynamic ; IP dinamica
  canreinvite=no ; Impide a los usuarios hacer conexiones RTP punto a punto sin
                 ; pasar por el servidor
  qualify=300 ; Cortar si la latencia es mayor a 300ms
  nat=no ; No dar tratamiento especial para nat, puede que funcione igual

  [101](estandar)
  callerid=Martin <101>
  secret=password101

  [102](estandar)
  callerid=Juan <102>
  secret=password102

  [103](estandar)
  callerid=Francisco <103>
  secret=password103
