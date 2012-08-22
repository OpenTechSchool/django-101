Django 101 Tutorial
*******************

The *Django 101 Tutorial* is an adaption of http://www.django-workshop.de for
the OpenTechSchool_ PyCoaches_ workshops. While creating a cookbook
application, you will get to know following aspects of Django:

- Django setup/configuration
- Models and object-relational mapping
- Django's admin interface
- URL routing
- Views
- Templates

The online version is available at
http://opentechschool.github.com/django-101/.

Setup
=====

The *Django 101 Tutorial* is written in the reStructuredText_ format. The
``.rst`` files can be edited with a normal text editor.

It can be rendered to several output formats using Sphinx_. To do that, you
need to have the following Python packages installed:

- docutils
- Pygments
- Sphinx
- Fabric

You also need Graphviz_, a graph visualization software. 


Build
=====

The fabric script ``fabfile.py`` contains tasks making the build process very
easy. The following commands have to be executed in the ``docs`` directory.

If you just want to render the HTML version, it's sufficient to run

.. code:: bash
   
    fab build

This will create a directory ``_build`` inside the ``docs`` directory,
containing the HTML version.

Other `builders <http://sphinx.pocoo.org/builders.html#builders>`_ can be
passed as argument. For instance use ``singlehtml`` to render the whole
tutorial into a single HTML file.

.. code:: bash

   fab build:singlehtml
  
Deploy
======

The *Django 101 Tutorial* is deployed as a `GitHub Page`_. A good way to do
that is described `here <https://gist.github.com/791759>`_. To simplify this
process, you can use the fabric target ``setup``.

.. code:: bash

   fab setup

This creates a clone of the repository inside the ``_build`` folder. You can 
now run ``fab build``, change into ``docs/_build/html``, commit and push.


.. _OpenTechSchool: http://opentechschool.org
.. _PyCoaches: http://python.opentechschool.org
.. _reStructuredText: http://docutils.sourceforge.net/docs/
.. _Sphinx: http://sphinx.pocoo.org/index.html
.. _Graphviz: http://www.graphviz.org/
.. _GitHub Page: https://help.github.com/categories/20/articles

