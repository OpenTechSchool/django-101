Django
******

Create a new *virtual environment* for the project
==================================================

After Python and :program:`virtualenv` have been installed, a new *virtual environment* can be created for Django:

.. code-block:: bash

    $ mkvirtualenv --distribute django-workshop

If :program:`virtualenvwrapper` hasn't been installed, execute the following command:

.. code-block:: bash

    $ virtualenv --distribute .virtualenvs/django-workshop

What happens:

* The option ``--distribute`` installs ``distribute`` instead of ``setuptools`` in the *virtual environment*.
* ``django-workshop`` is the name under which the *virtual environment* will be available.

If :program:`virtualenvwrapper` has been installed, the *virtual environment* is now already active.

Otherwise, it has to be activated manually:

.. code-block:: bash

    $ cd .virtualenvs/django-workshop
    $ . bin/activate

Install Django
==============

Now we can install Django into the activated *virtual environment*:

.. code-block:: bash

    $ pip install django

If :program:`virtualenvwrapper` has been installed, you can list the installed packages with the following command:

.. code-block:: bash

    $ lssitepackages -l

Without :program:`virtualenvwrapper` you can check the packages in the :file:`site-packages` directory:

.. code-block:: bash

    $ ls -l .virtualenvs/django-workshop/lib/python2.6/site-packages/

There you should see a folder `django`.

The Django version can be checked with this command::

    $ django-admin.py --version
    1.4

Install timezone support
------------------------

Starting with Django 1.4, support for :djangodocs:`timezones<topics/i18n/timezones/#time-zones>` is available, which is activated by default. It is recommended to install the package ``pytz``::

    $ pip install pytz

Resources
=========

* `Django homepage <http://www.djangoproject.com/>`_
* `The Django Book <http://djangobook.com/en/2.0/>`_
* `Django Packages <http://www.djangopackages.com/>`_ - A list of reusable apps
* `Django Snippets <http://djangosnippets.org/>`_ - A list of useful code snippets
