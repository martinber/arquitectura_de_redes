Programación
============

CGI + Python
------------

CGI (Common Gateway Interface) es una tecnología que permite al cliente
solicitar datos de un programa ejecutado del lado de un servidor web. Lo
utilizamos junto a HTML (Hyper Text Markup Language), el lenguaje deprogramación
estándar de páginas web. Está conformado por el uso de etiquetas o tags que
marcan la estructura del documento. Las aplicaciones CGI fueron las primeras en
ser utilizadas para brindar contenido dinámico para las páginas web.

En una aplicación CGI el servidor pasa las solicitudes del cliente a un programa
externo. Este puede estar escrito en cualquier lenguaje que soporte el servidor.
La salida del programa es devuelta al cliente, en lugar del archivo estático
tradicional.

1. El servidor recibe una petición (el cliente ha ingresado a una URL que
   contiene el CGI), y este redirige la petición con los datos al script.

2. El servidor prepara el entorno para ejecutar la aplicación. Esta información
   procede mayoritariamente del cliente.

3. El servidor ejecuta la aplicación y captura su salida estándar.

4. La aplicación realiza su función, como consecuencia de su actividad se va
   generando un objeto MIME que la aplicación escribe en su salida estándar.

5. Cuando la aplicación finaliza, el servidor envía la información producida,
   junto con información propia, al cliente, que se encontraba en espera. Es
   responsabilidad de la aplicación anunciar el tipo de objeto MIME que se
   genera (campo CONTENT_TYPE).

Nosotros utilizamos CGI junto con Python. Ya hay un módulo existente de CGI. Una
forma convencional de devolver información al cliente es utilizando la función
print para generar un HTML.

Programación orientada a objetos
--------------------------------

- Clase: Una construcción semántica con nombre que describe un comportamiento y
  atributos comunes.

- Objeto: Instancia de una clase. Se caracterizan por:

  - Identidad: Los objetos se distinguen unos de otros.

  - Comportamiento: Pueden realizar tareas.

  - Estado: Contienen información

- Encapsulación: Es un concepto ligado a la programación orientada a objetos que
  consiste en combinar datos y funciones de una clase en una cápsula de forma tal
  de evitar el uso incorrecto o manipulación de las mismas. Dentro de esta
  cápsula:

  - Los métodos son públicos, accesibles desde el exterior.

  - Los datos son privados, accesibles desde el interior.

- Herencia: Indica una relación entre clases. Permite la reutilización de los
  procedimientos y variables de una clase, ya que todos los datos y métodos
  disponibles a la clase padre se encuentran disponibles a la clase hijo con los
  mismos nombres.

  - Herencia simple: La clase deriva de una sola padre.

  - Herencia múltiple: Clase derivada de dos o más clases.

- `Polimorfismo <https://en.m.wikipedia.org/wiki/Object-oriented_programming#Polymorphism>`_ :
  Permite a una función recibir un objeto de una cierta clase padre
  y al mismo tiempo poder recibir clases hijas de forma indistinta.
