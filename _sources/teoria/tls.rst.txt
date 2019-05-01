TLS
===

Transport Layer Security.

- Su predecesor es SSL, no se utiliza más por cuestiones de seguridad, por eso
  muchos nombran a TLS y SSL como sinónimos.

- Normalmente asegura tres cosas (salvo que explícitamente alguna aplicación
  ignore una de ellas):

  - Cifrado: La comunicación viaja cifrada extremo a extremo utilizando claves
    simétricas generadas de forma única para cada conexión, estas claves son
    negociadas al comienzo de a sesión.

  - Autenticación: Generalmente al menos el servidor demuestra su identidad
    utilizando un par de claves pública/privada.

  - Confiabilidad: Se verifica la integridad de cada mensaje para detectar
    errores o alteraciones de los datos transmitidos por un atacante.

- Al conectarse se realiza un handshake en el cual se decide el cifrado a
  utilizar

Historia breve
--------------

Tomada de
`High Performance Browser Networking (O'Reilly) <https://hpbn.co/transport-layer-security-tls/>`_:

  The SSL protocol was originally developed at Netscape to enable ecommerce
  transaction security on the Web, which required encryption to protect
  customers’ personal data, as well as authentication and integrity guarantees
  to ensure a safe transaction. To achieve this, the SSL protocol was
  implemented at the application layer, directly on top of TCP (Figure 4-1),
  enabling protocols above it (HTTP, email, instant messaging, and many others)
  to operate unchanged while providing communication security when communicating
  across the network.

  SSL 2.0 was the first publicly released version of the protocol, but it was
  quickly replaced by SSL 3.0 due to a number of discovered security flaws.
  Because the SSL protocol was proprietary to Netscape, the IETF formed an
  effort to standardize the protocol, resulting in RFC 2246, which was published
  in January 1999 and became known as TLS 1.0. Since then, the IETF has
  continued iterating on the protocol to address security flaws, as well as to
  extend its capabilities: TLS 1.1 (RFC 4346) was published in April 2006, TLS
  1.2 (RFC 5246) in August 2008, and work is now underway to define TLS 1.3.

Referencias
-----------

- https://hpbn.co/transport-layer-security-tls/

- https://en.wikipedia.org/wiki/Transport_Layer_Security
