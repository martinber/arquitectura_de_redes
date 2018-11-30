GNS3
====

No me acuerdo bien como instalé GNS3, la cosa es que anduvo y que no lo quise
tocar. Pongo algunas notas de lo que hago a partir de ahora y de lo que me
acuerdo que hice.

Elementos usados
----------------

- Hosts: Como host uso un Docker con la imagen de ``gns3-ipterm``, que es un
  Linux bien básico con las herramientas de red básicas ya instaladas, por eso
  es mejor que un Alpine Linux o un Debian pelado.

  Hay que instalar Docker en la computadora, después en las preferencias de GNS3
  hay una sección que se llama "Docker containers", agregué el contenedor de
  ``gns3/ipterm``, GNS3 se encarga de descargarlo y todo.

- Router Mikrotik: En este caso descargué un "Appliance" oficial desde la wiki
  de GNS3 y al mismo tiempo una imagen que se llama ``chr-6.41.4.img``. Una vez
  que se hayan descargado esas dos cosas, en el GNS3 se importa el "Appliance"
  que te permite buscar el ``.img`` descargado y te deja todo listo para usar.

  Una vez agregado, en las preferencias de GNS3 este router aparece en la parte
  de "QEMU VMs". Al abrir una consola el usuario es ``admin`` y no tiene
  contraseña, no hay que escribir nada. Para usar ipv6 en este router Mikrotik
  ver abajo.

- Router Cisco: Uso el modelo 3745, lo importé usando la imagen
  ``c3745-advipservicesk9-mz.124-25d.bin`` que no se puede obtener gratis de
  forma legal. No me acuerdo como hice para agregarlo al GNS3, ahora que veo, en
  las preferencias de GNS3 aparece en la parte de "IOS Routers".

- Switch Cisco: Este fué dificilisimo de conseguir, no se al final que modelo
  tengo. Hay que conseguir:

  - El Appliance ``cisco-iosvl2.gns3a``. Este es fácil de conseguir.

  - Las imágenes ``vios_l2-adventerprisek9-m.03.2017.qcow2`` y
    ``vios_l2-adventerprisek9-m.vmdk.SSA.152-4.0.55.E``. Estas son muy difíciles
    de conseguir, el único lugar en donde las encontré es en una pagina en chino
    y árabe.

  Hay que agregar al Appliance, al agregarlo te va a pedir que busques las dos
  imágenes y listo. Una vez terminado aparece en la parte de "QEMU VMs" con el
  nombre de ``Cisco IOSvL2 15.2.4055``.

Otros tips
----------

- Cuando intenté usar ipv6 en el router Mikrotik no encotraba los comandos. En
  mi caso pasaba que el paquete ``ipv6`` del router no estaba habilitado, esto
  se puede ver al hacer ``system package print`` y viendo qué paquetes tienen
  una ``X`` al lado. Para habilitar un paquete hay que hacer ``system package
  enable numbers=`` y poniendo el número correspondiente a ese paquete (el
  número se ve al hacer ``system package print``). Finalmente hay que hacer un
  ``system reboot`` paar que se termine de activar.

- Cuando te pasan un proyecto con Ciscos adentro puede que ande lento, para
  acelerarlo hay que hacer click derecho en un Cisco cualquiera y poner *Auto
  Idle-PC value*.

- Para hacer una imagen de Docker para algún servidor, me hago una carpeta con
  algún *Dockerfile*, creo la imagen con ``docker image build -t nombre_image
  .`` y la agrego a GNS3.
