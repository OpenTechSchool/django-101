Development Webserver
*********************

Now that Django is configured, you can view the first results.

Start the development webserver
===============================

You can start Djangos development webserver with the following command:

..  code-block:: bash

    $ python manage.py runserver
    Validating models...

    0 errors found
    Django version 1.4, using settings 'cookbook.settings'
    Development server is running at http://127.0.0.1:8000/
    Quit the server with CONTROL-C.

Open the URL http://127.0.0.1:8000/ in your browser to view Djangos welcome page.

The first own page
==================

Only a few steps are needed to show simple first page.

Create a template
-----------------

The first thing you need for your website is a template. Create a folder :file:`templates` in the project directory. (Remember that the directory :file:`cookbook` that contains :file:`manage.py`.) In the new directory you create the file :file:`base.html`:

.. code-block:: html+django

    <!doctype html>
    <html>
    <head>
        <meta charset="utf-8">
    	<title>Cookbook</title>
    </head>
    <body>
        <h1>Cookbook</h1>
    </body>
    </html>

Modify URLConf 
--------------

The simplest way to view a template is the ``TemplateView``. 

Open the folder :file:`cookbook/urls.py` and enter the follwing code after line 1::

    from django.views.generic.base import TemplateView

Additionally enter at the end of the ``urlpatterns`` the following line::

    url(r'^home/', TemplateView.as_view(template_name='base.html')),

After your edits the file should look line that::

    from django.conf.urls import patterns, include, url
    from django.views.generic.base import TemplateView

    # Uncomment the next two lines to enable the admin:
    # from django.contrib import admin
    # admin.autodiscover()

    urlpatterns = patterns('',
        # Examples:
        # url(r'^$', 'cookbook.views.home', name='home'),
        # url(r'^cookbook/', include('cookbook.foo.urls')),

        # Uncomment the admin/doc line below to enable admin documentation:
        # url(r'^admin/doc/', include('django.contrib.admindocs.urls')),

        # Uncomment the next line to enable the admin:
        # url(r'^admin/', include(admin.site.urls)),
        url(r'^home/', TemplateView.as_view(template_name='base.html'))
    )

Now you can start the development server again and reach the new page under http://127.0.0.1:8000/home/.


Activate the Admin-Application 
==============================

Django has an Admin-Application included, that allows you to interact with the database over a web interface.

Ajust the configuration
-----------------------

Open the file :file:`settings.py` and find the section ``INSTALLED_APPS``.
Remove the comment sign in the line that contains ``'django.contrib.admin',``,
to activate the Admin-Application.

Adjust URLConf
--------------

To make the Admin-Application ready to be openend in the browser, we have
to activate the URL of it. Open :file:`cookbook/urls.py` and remove the
comment signs in lines 5, 6 and 17. After that the file should look like
that::

    from django.conf.urls import patterns, include, url
    from django.views.generic.base import TemplateView

    # Uncomment the next two lines to enable the admin:
    from django.contrib import admin
    admin.autodiscover()

    urlpatterns = patterns('',
        # Examples:
        # url(r'^$', 'cookbook.views.home', name='home'),
        # url(r'^cookbook/', include('cookbook.foo.urls')),

        # Uncomment the admin/doc line below to enable admin documentation:
        # url(r'^admin/doc/', include('django.contrib.admindocs.urls')),

        # Uncomment the next line to enable the admin:
        url(r'^admin/', include(admin.site.urls)),
        url(r'^home/', TemplateView.as_view(template_name='base.html'))
    )

Synchronise the database
------------------------

Django already includes an App to do the authentication. To configure the database accordingly, execute the following command::

    $ python manage.py syncdb
    Creating tables ...
    Creating table auth_permission
    Creating table auth_group_permissions
    Creating table auth_group
    Creating table auth_user_user_permissions
    Creating table auth_user_groups
    Creating table auth_user
    Creating table django_content_type
    Creating table django_session
    Creating table django_site
    Creating table django_admin_log

    You just installed Django's auth system, which means you don't have any superusers defined.
    Would you like to create one now? (yes/no): yes
    Username (leave blank to use 'vagrant'): admin
    E-mail address: admin@example.com
    Password:
    Password (again):
    Superuser created successfully.
    Installing custom SQL ...
    Installing indexes ...
    Installed 0 object(s) from 0 fixture(s)

..  note::

    In the process of installing Djangos authentication app the first time a new admin/superuser is be created.

The admin application has the URL http://127.0.0.1:8000/admin/. The credentials are the ones of the superuser you just
created.

Weiterführende Links zur Django Dokumentation
=============================================

* :djangodocs:`Informationen about the Admin-Application <ref/contrib/admin/#ref-contrib-admin>`
