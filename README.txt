Esto está hecho con Sphinx.

Instalar, rst2pdf necesita Python 2::

  pip install sphinx sphinx_rtd_theme rst2pdf

Se puede exportar a:

- HTML: Usando directamente Sphinx con el tema sphinx_rtd_theme.

- PDF: Usando la extensión rst2pdf y un tema hecho por mí ubicado en _themes.

Para exportar::

  make html
  make pdf

Lo exportado está en _build.
