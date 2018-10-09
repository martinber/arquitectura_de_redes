Esto está hecho con Sphinx.

Para verlo online
-----------------

- `HTML <https://martinber.github.io/arquitectura_de_redes>`_.

- `PDF <https://github.com/martinber/arquitectura_de_redes/tree/master/build/pdf`_.

Para generarlo uno mismo
------------------------

Instalar, ``rst2pdf`` necesita Python 2::

  pip install sphinx sphinx_rtd_theme rst2pdf

Se puede exportar a:

- HTML: Usando directamente Sphinx con el tema ``sphinx_rtd_theme``.

- PDF: Usando la extensión rst2pdf y un tema hecho por mí ubicado en
  ``source/sphinx_styles``.

Para exportar::

  make html
  make pdf

Lo exportado está en la carpeta ``build``.
