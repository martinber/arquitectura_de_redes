Notas de Arquitectura de Redes, Redes de Información, etc. Esto está hecho con
Sphinx.

Para verlo online
-----------------

- https://martinber.github.io/arquitectura_de_redes.

Para generarlo uno mismo
------------------------

Instalar::

  pip3 install sphinx sphinx_rtd_theme

Para exportar::

  make html

Lo exportado está en la carpeta ``build``.

Para hacer build y push a GitHub::

  make html
  git subtree push --prefix build/html origin gh-pages
