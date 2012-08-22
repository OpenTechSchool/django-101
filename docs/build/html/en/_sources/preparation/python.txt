Python
******

Django is completely based on Python. Because of that, Python has to be installed first.

Install Python
==============

Django |djangoversion| supports the Python versions 2.5, 2.6 and 2.7. If you got an older version, you should update it. Django 1.5 will drop Python 2.5 but have experimental Python 3.3 support.

You can check your Python version by starting the Python interpreter in the command line with the option ``-V``.

.. code-block:: bash

    $ python -V
    Python 2.6.1

If you already got the right version, you can proceed with the installation of the :ref:`Python package manager <python_packagemanager>`.

Linux
-----

Most Linux distributions have Python pre-installed. If not, you can usually install it with the package manager.

As an alternative, you can download the `Python sources <http://python.org/download/>`_  and compile it by your own.

Mac OS X
--------

Mac OS X comes with Python pre-installed. Snow Leopard brings Python 2.6, Lion and Mountain Lion 2.7.

As an alternative, you can install Python with Homebrew_.

.. _Homebrew: http://mxcl.github.com/homebrew/

Windows
-------

Download the `installer <http://python.org/download/>`_ from the Python website and install Python.

.. _python_packagemanager:

Python Package Manager
======================

Python uses it's own package system to distribute and install Python packages. Because we will need some of them, we need to install the package manager beforehand.

distribute
----------

First step is to install :program:`distribute`, a replacement for :program:`setuptools`, which may be already installed on some systems.

There is a bootstrap script to ease installation:

.. code-block:: bash

    $ wget http://python-distribute.org/distribute_setup.py
    $ python distribute_setup.py

.. note::

    *Root* privileges may be required on Linux or Mac OS X.

pip
---

The package installation is done by :program:`pip`, a replacement for :program:`easy_install` which brings `more features <http://www.pip-installer.org/en/latest/index.html#pip-compared-to-easy-install>`_. However :program:`pip` itself has to be installed with :program:`easy_install`:

.. code-block:: bash

    $ easy_install pip

If :program:`easy_install` isn't available, :program:`pip` can also be installed with a bootstrap script:

.. code-block:: bash

    $ wget https://raw.github.com/pypa/pip/master/contrib/get-pip.py
    $ python get-pip.py

.. note::

    *Root* privileges may be required on Linux or Mac OS X.

You can test :program:`pip` after the installation:

.. code-block:: bash

    $ pip --version

Resources
=========

* `Python homepage <http://python.org/>`_
* `Official Python tutorial <http://docs.python.org/tutorial/index.html>`_
* `Learn Python The Hard Way <http://learnpythonthehardway.org/>`_
* `Code Like a Pythonista: Idiomatic Python (interactive tutorial) <http://python.net/~goodger/projects/pycon/2007/idiomatic/presentation.html>`_
* `distribute documentation <http://packages.python.org/distribute/>`_
* `pip homepage <http://www.pip-installer.org/>`_
