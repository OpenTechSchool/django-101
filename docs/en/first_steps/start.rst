Start a new project
*******************

A folder for all python projects
================================

Lets create a folder for this and future projects:

..  code-block:: bash

    $ mkdir pythonprojects


Create the Django Project
=========================

Our project will be a cookbook, so we name the 
project aptly :file:`cookbook`.

Change into the 'pythonprojects' dir and create a Django project with 
the following commands:

..  code-block:: bash

    $ cd pythonprojects
    $ django-admin.py startproject cookbook

..  note::

    In Windows it could be possible that you have to enter the full path :file:`django-admin.py`::

        C:\pythonprojects> python C:\virtualenvs\django-workshop\Scrips\django-admin.py startproject cookbook

After you created the new project, you can have a look at whats in the :file:`cookbook` directory:

::

    cookbook # project directory
    |-- cookbook # config directory
    |   |-- __init__.py
    |   |-- settings.py
    |   |-- urls.py
    |   `-- wsgi.py
    `-- manage.py           

The first directory named :file:`cookbook` is the project directory.
It contains the file :file:`manage.py`, that is used, to manage the 
project, e.g. ... . In this document it will be refered as 
**project directory**.

The project directory contains the `Python package
<http://docs.python.org/tutorial/modules.html#packages>`_ :file:`cookbook`, 
with the central configuration for the Django project.
The empty file :file:`__init__.py` turns that directory into a package.
The file :file:`settings.py`, contains all the settings of the project. We will
edit this file in the next step. The file :file:`urls.py` contains rules
to direct an URL to the right view. Regular expressions are used to 
describe this rules. We will talk about that later. The file :file:`wsgi.py` 
defines the WSGI Application, that is needed later for the deployment. 
This whole directory will be called **configuration directory** in all
chapters of this document.

Adjust the configuration
========================

The first step of work with the project will be the setting of some 
configuration values. For that you edit the file :file:`settings.py` 
with your text editor of choice.

The working directory is used in different places of the settings file.
For convinience we ... it dynamically and save the value into the "constant"
SITE_ROOT::

    import os

    SITE_ROOT = os.path.realpath(os.path.dirname(__file__))

..  note::

    It is a convention in python that to write constants with capital letters.

Now we configure the database connection. We use a `SQLite
<http://www.sqlite.org/>`_ database, because it is already included in Python
2.5 and later as ``sqlite3``.

If you use Python 2.4, you would have to install the package SQLite manually.

Configure the database connection ``default`` like this::

    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.sqlite3',
            'NAME': os.path.join(SITE_ROOT, '..', 'cookbook.db'),
            'USER': '',
            'PASSWORD': '',
            'HOST': '',
            'PORT': '',
        }
    }

Next the time zone and language will be set::

    TIME_ZONE = 'Europe/Berlin'

    LANGUAGE_CODE = 'de'

The last thing on the list is to set the path to the templates::

    TEMPLATE_DIRS = (
        os.path.join(SITE_ROOT, '..', 'templates'),
    )

We will create the directory for the templates later in the root directory
of the project. Notice how we make use of the SITE_ROOT constant.

..  note::

    It is possible to have your templates outside of the project.
    You just would have to reference the path in the TEMPLATE_DIRS constant.


Further Readings in the Django Documentation
=============================================

* :djangodocs:`Configuration of Django <topics/settings/#topics-settings>`
* :djangodocs:`Listing of all possible constants in the settings <ref/settings/#ref-settings>`
