Mail
====

.. todo::

   Hacer

   - 822

   - SMTP

   - MIME

   - POP

   - IMAP

Para entenderlo bien creo que habría que saberse un poco la historia de cómo fue
evolucionando porque es como que siempre se fueron corrigiendo cosas, sobre todo
para intentar mejorar la seguridad, disminuir el SPAM, impedir el `spoofing`__,
etc.

__ https://en.wikipedia.org/wiki/Email_spoofing

Hay distintos agentes (programas/servidores), al final hay servidores que son
más de una cosa a la vez pero la división sería la siguiente:

  - Mail User Agent (MUA): Es un cliente de correo, es el programa que usa el
    usuario.

  - Mail Submission Agent (MSA): Recibe correos desde un MUA y lo envía a un
    MTA. Es el primer servidor de la cadena y es recibe sólo correos de MUAs
    autorizados.

  - Mail Transfer Agent (MTA): Transfiere correos de un agente a otro usando
    SMTP, recibe correos desde MSAs o MTAs potencialmente desde otros dominios.

  - Mail Delivery Agent (MDA): Recibe mensajes desde un MTA y lleva los mensajes
    a una casilla de correo local de un usuario. Hace también por ejemplo
    filtrado de SPAM y separación de correos en carpetas.

  - Mail Retrieval Agent (MRA): Solicita mensajes a un MDA y los muestra al
    usuario. Generalmente es el mismo software que el MUA.

Todos los pasos de de la cadena menos el último usan SMTP. El último paso usa
POP3 o IMAP::

  [MUA] --SMTP--> [MSA] --SMTP--> [MTA] --SMTP--> [MDA] --POP3/IMAP--> [MRA]

Explicación desde Wikipedia
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. image:: ./mail.svg

Modificado por mí, original de Ale2006-from-en [`CC BY-SA 3.0`__].

__ https://creativecommons.org/licenses/by-sa/3.0

Email is submitted by a mail client (mail user agent, MUA) to a mail server
(mail submission agent, MSA) using SMTP on TCP port 587. Most mailbox providers
still allow submission on traditional port 25. The MSA delivers the mail to its
mail transfer agent (mail transfer agent, MTA). Often, these two agents are
instances of the same software launched with different options on the same
machine. Local processing can be done either on a single machine, or split among
multiple machines; mail agent processes on one machine can share files, but if
processing is on multiple machines, they transfer messages between each other
using SMTP, where each machine is configured to use the next machine as a smart
host. Each process is an MTA (an SMTP server) in its own right.

The boundary MTA uses the Domain name system (DNS) to look up the mail exchanger
record (MX record) for the recipient's domain (the part of the email address on
the right of @). Based on the target host and other factors the MTA selects an
exchange server, the MTA connects to the exchange server as an SMTP client.

Message transfer can occur in a single connection between two MTAs, or in a
series of hops through intermediary systems. A receiving SMTP server may be the
ultimate destination, an intermediate "relay" (that is, it stores and forwards
the message) or a "gateway" (that is, it may forward the message using some
protocol other than SMTP). Each hop is a formal handoff of responsibility for
the message, whereby the receiving server must either deliver the message or
properly report the failure to do so.

Once the final hop accepts the incoming message, it hands it to a mail delivery
agent (MDA) for local delivery. An MDA saves messages in the relevant mailbox
format. As with sending, this reception can be done using one or multiple
computers, but in the diagram above the MDA is depicted as one box near the
mail exchanger box. An MDA may deliver messages directly to storage, or forward
them over a network using SMTP or other protocol such as Local Mail Transfer
Protocol (LMTP), a derivative of SMTP designed for this purpose.

Once delivered to the local mail server, the mail is stored for batch retrieval
by authenticated mail clients (MUAs/MRAs). Mail is retrieved by end-user
applications, called email clients, using Internet Message Access Protocol
(IMAP), a protocol that both facilitates access to mail and manages stored mail,
or the Post Office Protocol (POP) which typically uses the traditional mbox mail
file format. Webmail clients may use either method, but the retrieval protocol
is often not a formal standard.

SMTP defines message transport, not the message content. Thus, it defines the
mail envelope and its parameters, such as the envelope sender, but not the
header (except trace information) nor the body of the message itself. STD 10
and RFC 5321 define SMTP (the envelope), while STD 11 and RFC 5322 define the
message (header and body), formally referred to as the Internet Message Format. 

SMTP
----

- Usa TCP normalmente con puerto 25.

- Es importante que el correo permita la entrega con retardo en el caso que se
  pierda la conexión o la máquina remota falle.

- Está basado en texto, se envían comandos de junto con argumentos separados por
  espacios. Se responde similar a HTTP.

Comandos
~~~~~~~~

- HELO: Enviado por el cliente para iniciar la conversación, generalmente se
  envía como argumento el nombre de dominio del cliente.

- EHLO: Lo mismo que HELO pero indica que se va a usar Extended SMTP.

- AUTH: Autentica el cliente ante el servidor, envía el nombre de usuario y
  contraseña.

- MAIL FROM: Indica la dirección de mail del remitente.

- RCPT TO: Indica la dirección de mail del destino, se puede utilizar varias
  veces para agregar más destinatarios.

- DATA: Comienza la transmisión de los contenidos del correo incluyendo los
  archivos adjuntos. Para indicar el fin del mensaje se envía una línea con un
  punto, es decir ``\r\n.\r\n``

- QUIT: Cierra la conversación.


POP
---


IMAP
----
