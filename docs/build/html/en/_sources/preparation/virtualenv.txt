Virtualenv
**********

What is Virtualenv?
===================

When working with many projects, sooner or later there will be conflicts between the used package versions.

For instance an old project might depend on Django 1.2, and can't be migrated because lack of time. At the same time, a new Project with Django |djangoversion| has to be started. Such problems can be solved with :program:`virtualenv`.

:program:`virtualenv` creates a "container" for each project, containing the installed packages and separates them from the system version.

In addition, :program:`virtualenv` can assign different Python versions to different environments.

:program:`virtualenv` is also suitable for the production environment on the live server.

Installation
============

:program:`virtualenv` can be installed with :program:`pip`:

.. code-block:: bash

    $ pip install virtualenv

.. note::

    *Root* privileges may be required on Linux or Mac OS X.

After the installation, a folder for *all* virtual environments should be created. For instance in the home directory.

.. code-block:: bash

    $ mkdir .virtualenvs

Simplification through Virtualenvwrapper
========================================

To simplify working with :program:`virtualenv` on Linux or Mac OS X, you can install the package :program:`virtualenvwrapper`:

.. code-block:: bash

    $ pip install virtualenvwrapper

Add the following lines to the file :file:`.bashrc` or :file:`.profile`. In your home directory.

.. code-block:: bash

    export WORKON_HOME=$HOME/.virtualenvs
    source /usr/local/bin/virtualenvwrapper.sh

This lets :program:`virtualenvwrapper` know, where the virtual environments are located. The script :file:`virtualenvwrapper.sh` loads the shell commands we will work with.

After editing :file:`.bashrc` or :file:`.profile`, the configuration has to be loaded manually so :program:`virtualenvwrapper` can create the necessary scripts.

.. code-block:: bash

    $ source .bashrc
    virtualenvwrapper.user_scripts creating /home/vagrant/.virtualenvs/initialize
    virtualenvwrapper.user_scripts creating /home/vagrant/.virtualenvs/premkvirtualenv
    virtualenvwrapper.user_scripts creating /home/vagrant/.virtualenvs/postmkvirtualenv
    virtualenvwrapper.user_scripts creating /home/vagrant/.virtualenvs/prermvirtualenv
    virtualenvwrapper.user_scripts creating /home/vagrant/.virtualenvs/postrmvirtualenv
    virtualenvwrapper.user_scripts creating /home/vagrant/.virtualenvs/predeactivate
    virtualenvwrapper.user_scripts creating /home/vagrant/.virtualenvs/postdeactivate
    virtualenvwrapper.user_scripts creating /home/vagrant/.virtualenvs/preactivate
    virtualenvwrapper.user_scripts creating /home/vagrant/.virtualenvs/postactivate
    virtualenvwrapper.user_scripts creating /home/vagrant/.virtualenvs/get_env_details
    virtualenvwrapper.user_scripts creating /home/vagrant/.virtualenvs/premkproject
    virtualenvwrapper.user_scripts creating /home/vagrant/.virtualenvs/postmkproject
    virtualenvwrapper.user_scripts creating /home/vagrant/.virtualenvs/prermproject
    virtualenvwrapper.user_scripts creating /home/vagrant/.virtualenvs/postrmproject

Resources
=========

* `virtualenv documentation <http://www.virtualenv.org/en/latest/>`_
* `virtualenvwrapper homepage <http://www.doughellmann.com/projects/virtualenvwrapper/>`_
