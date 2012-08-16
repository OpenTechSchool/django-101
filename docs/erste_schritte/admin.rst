Die Admin-Applikation
*********************

Django bietet die Möglichkeit, eigene Applikationen in die Admin-Applikation einzubinden.

Die eigene Applikation beim Admin registrieren
==============================================

Damit der Admin mit unser Applikation benutzt werden kann, müssen wir unsere
Models dem Admin bekannt machen.

Dazu muss die Datei :file:`admin.py` in der Applikation angelegt werden. Das
Projekt sieht danach folgendermassen aus:

::

    cookbook
    |-- cookbook
    |   |-- __init__.py
    |   |-- settings.py
    |   |-- urls.py
    |   `-- wsgi.py
    |-- manage.py
    `-- recipes
        |-- admin.py
        |-- __init__.py
        |-- models.py
        |-- tests.py
        `-- views.py

Danach öffnest du die Datei in deinem Editor und fügst die beiden folgenden
Zeilen Code ein::

    from django.contrib import admin
    from .models import Category, Recipe

Damit stehen dir der Admin und die Models der Applikation zur Verfügung.

.. note::

    Der zweite ``import`` ist ein relativer import. Diese wurden in
    :pep:`328` definiert und sind ab Python 2.6 direkt verfügbar. In
    Python 2.5 müssen sie mit folgendem Code in der ersten Zeile der
    Datei geladen werden::

        from __future__ import absolute_import

Als nächstes erstellen wir eine Klasse, um das Model ``Category`` beim Admin
zu registrieren::

    class CategoryAdmin(admin.ModelAdmin):
        prepopulated_fields = {'slug': ['name']}


    admin.site.register(Category, CategoryAdmin)

Mehr ist nicht zu tun.

Das Attribut ``prepopulated_fields`` hilft in der Admin-Applikation dabei,
dass Feld ``slug`` bei der Eingabe automatisch zu füllen. In diesem Fall mit
dem Attribut ``name`` des Models.

Das gleiche tun wir jetzt für das Model ``Recipe``::

    class RecipeAdmin(admin.ModelAdmin):
        prepopulated_fields = {'slug': ['title']}


    admin.site.register(Recipe, RecipeAdmin)

Die vollständige Datei
----------------------

Die Datei :file:`admin.py` sollte nun so aussehen (ab Python 2.6, für
Python 2.5 muss wie oben erwähnt noch eine Zeile eingefügt werden)::

    from django.contrib import admin
    from .models import Category, Recipe


    class CategoryAdmin(admin.ModelAdmin):
        prepopulated_fields = {'slug': ['name']}


    class RecipeAdmin(admin.ModelAdmin):
        prepopulated_fields = {'slug': ['title']}


    admin.site.register(Category, CategoryAdmin)
    admin.site.register(Recipe, RecipeAdmin)


Weiterführende Links zur Django Dokumentation
=============================================

* :djangodocs:`Informationen zur Admin-Applikation <ref/contrib/admin/#ref-contrib-admin>`
