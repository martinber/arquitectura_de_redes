DNS
===

Domain Name System.

.. todo:: Hacer

  Quien tiene autoridad sobre la zona

- Puerto 53

- UDP para resoluciones

- TCP para transferencias de zona

- Clasificación:

  - Solo Cache: No es autoridad para ninguna zona. No posee base de datos (DB).
    Almacena las consultas.

  - Primario (Maestro): Es autoridad de zona. Posee DB, las cuales son
    administradas localmente.

  - Secundario (Esclavo): Es autoridad de zona. Trabaja con transferencia de
    zona. Toma las zonas de un primario y las almacena localmente.

- Tipos de consultas:

  - Iterativas: El server responde la mejor respuesta, si no conoce la respuesta
    deriva el cliente a otro servidor.

  - Recursivas: el servidor asume la responsabilidad de proporcionar la respuesta,
    si la desconoce, se encarga de consultar otros servidores hasta obtenerla para
    finalmente proporcionar dicha respuesta al cliente (no se hace derivación).

.. todo:: Tipos de registros
