Entwicklungs-Webserver
**********************

Nun da Django konfigiriert ist, kannst du dir die ersten Resultate ansehen.

Den Entwicklungs-Webserver starten
==================================

Zunächst starte Djangos Entwicklungs-Webserver:

..  code-block:: bash

    $ python manage.py runserver
    Validating models...

    0 errors found
    Django version 1.4, using settings 'cookbook.settings'
    Development server is running at http://127.0.0.1:8000/
    Quit the server with CONTROL-C.

Unter der URL http://127.0.0.1:8000/ kannst du dir die Willkommens-Seite von Django ansehen.

Die erste eigene Seite
======================

Um eine einfache Seite anzuzeigen, braucht es nur ein paar wenige Schritte.

Template anlegen
----------------

Als erstes benötigst du ein Template für deine Website. Erstelle das Verzeichnis :file:`templates` im Projektverzeichnis. Das ist das Verzeichnis :file:`cookbook` mit der Datei :file:`manage.py` darin. Im neuen Verzeichnis erstellst du die Datei :file:`base.html`:

.. code-block:: html+django

    <!doctype html>
    <html>
    <head>
        <meta charset="utf-8">
    	<title>Kochbuch</title>
    </head>
    <body>
        <h1>Kochbuch</h1>
    </body>
    </html>

URLConf anpassen
----------------

Die einfachste Art ein Template anzuzeigen, ist der ``TemplateView``. 

Öffne die Datei :file:`cookbook/urls.py` und füge nach Zeile 1 folgenden Code ein::

    from django.views.generic.base import TemplateView

Außerdem füge am Ende der ``urlpatterns`` folgende Zeile ein::

    url(r'^home/', TemplateView.as_view(template_name='base.html')),

Danach sieht die Datei so aus::

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

Du kannst nun wieder den Entwicklungs-Webserver starten und die neue Seite unter http://127.0.0.1:8000/home/ erreichen.


Die Admin-Applikation aktivieren
================================

Django bringt eine Admin-Applikation mit, die es dir erlaubt, über ein Web-Interface mit der Datenbank zu interagieren.

Anpassen der Konfiguration
--------------------------

Entferne in der Datei :file:`settings.py` in ``INSTALLED_APPS`` das
Kommentarzeichen vor der Zeile ``'django.contrib.admin',``, um die
Admin-Applikation zu aktivieren.

URLConf anpassen
----------------

Damit die Admin-Applikation auch im Browser aufgerufen werden kann müssen wir
die URL des Admins ebenfalls aktivieren.

Öffne dazu die Datei :file:`cookbook/urls.py` und entferne die
Kommentarzeichen in den Zeilen 5, 6 und 17. Danach sieht die Datei so aus::

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

Datenbank synchronisieren
-------------------------

Django bringt bereits eine App zur Authentifizierung mit. Um die Datenbank entsprechend einzurichten, führe folgendes Kommando aus::

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

    Weil die in Django enthaltene App zur Authentifizierung zum ersten mal
    installiert wird, wird auch ein neuer Superuser angelegt.

Starte nun erneut den Entwicklungs-Webserver.

..  code-block:: bash

    $ python manage.py runserver
    Validating models...

    0 errors found
    Django version 1.4, using settings 'cookbook.settings'
    Development server is running at http://127.0.0.1:8000/
    Quit the server with CONTROL-C.

Die Admin-Applikation kann unter http://127.0.0.1:8000/admin/ erreicht werden. Die Zugangsdaten entsprechen denen des eben angeleten Superusers.

Weiterführende Links zur Django Dokumentation
=============================================

* :djangodocs:`Informationen zur Admin-Applikation <ref/contrib/admin/#ref-contrib-admin>`
