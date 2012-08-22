Datenbank und Entwicklungs-Webserver
************************************

Nun können wir die Datenbank aktualisieren und danach den Entwicklungs-Webserver auf
rufen, um das Kochbuch über die Admin-Applikation zu befüllen.

Datenmodel überprüfen
=====================

Als erstes solltest du dein Datenmodel mit folgendem Kommando überprüfen:

.. code-block:: bash

    $ python manage.py validate

Django überprüft das Datenmodel automatisch bei allen Operationen, die Models
benutzten. Mit Hilfe dieses Befehls kannst du die Prüfung auch gezielt
durchführen.

Datenbank synchronisieren
=========================

Aus den Models müssen nun SQL Queries erzeugt werden, um die Datenbank zu
füllen.

Mit dem folgenden Kommando kannst du dir die Queries ausgeben lassen:

.. code-block:: bash

    $ python manage.py sqlall recipes

.. code-block:: sql

    BEGIN;
    CREATE TABLE "recipes_category" (
        "id" integer NOT NULL PRIMARY KEY,
        "name" varchar(100) NOT NULL,
        "slug" varchar(50) NOT NULL UNIQUE,
        "description" text NOT NULL
    )
    ;
    CREATE TABLE "recipes_recipe_category" (
        "id" integer NOT NULL PRIMARY KEY,
        "recipe_id" integer NOT NULL,
        "category_id" integer NOT NULL REFERENCES "recipes_category" ("id"),
        UNIQUE ("recipe_id", "category_id")
    )
    ;
    CREATE TABLE "recipes_recipe" (
        "id" integer NOT NULL PRIMARY KEY,
        "title" varchar(255) NOT NULL,
        "slug" varchar(50) NOT NULL UNIQUE,
        "ingredients" text NOT NULL,
        "preparation" text NOT NULL,
        "time_for_preparation" integer,
        "number_of_portions" integer NOT NULL,
        "difficulty" smallint NOT NULL,
        "author_id" integer NOT NULL REFERENCES "auth_user" ("id"),
        "date_created" datetime NOT NULL,
        "date_updated" datetime NOT NULL
    )
    ;
    CREATE INDEX "recipes_recipe_cc846901" ON "recipes_recipe" ("author_id");
    COMMIT;

Um diese Queries direkt auszuführen und so die Tabellen und Indizes anzulegen
musst du folgendes Kommando ausführen

.. code-block:: bash

    $ python manage.py syncdb
    Creating tables ...
    Creating table recipes_category
    Creating table recipes_recipe_category
    Creating table recipes_recipe
    Installing custom SQL ...
    Installing indexes ...
    Installed 0 object(s) from 0 fixture(s)

Entwicklungs-Webserver starten
==============================

Nachdem die Datenbank aktualisiert wurde kannst du nun wieder den Entwicklungs-Webserver starten:

.. code-block:: bash

    $ python manage.py runserver
    Validating models...

    0 errors found
    Django version 1.4, using settings 'cookbook.settings'
    Development server is running at http://127.0.0.1:8000/
    Quit the server with CONTROL-C.

Unter der URL http://127.0.0.1:8000/admin/recipes/ kannst du nun ein paar Rezepte anlegen.

Export und Import von Daten mit Hilfe von JSON
==============================================

Damit man Daten zwischen verschiedenen Systemen austauschen kann gibt es in
Django eingebaute Export- und Importfunktionen. Mit dem Kommando
:program:`dumpdata` kannst du die eben erstellten Models aus der Applikation
``recipes`` exportieren

.. code-block:: bash

    $ mkdir recipes/fixtures
    $ python manage.py dumpdata --indent 4 recipes > recipes/fixtures/initial_data.json

Django lädt die Fixtures aus einer Datei mit dem Namen
:file:`initial_data.json` jedes mal wenn du :program:`syncdb` ausführst. Die
gerade gespeicherten Daten werden also automatisch geladen wenn du die Models
löscht und neu anlegst.

Außerdem kannst du die Daten auch manuell mit dem Befehl :program:`loaddata` laden

.. code-block:: bash

    $ python manage.py loaddata recipes/fixtures/initial_data.json
    Installed 4 object(s) from 1 fixture(s)

.. note::

    Um Daten aus anderen Quellen in Django zu importieren eignet sich
    :program:`loaddata` nur bedingt, da in den Fixtures auch immer die
    Primärschlüssel definiert sind. Es gibt andere Apps, wie zum Beispiel `CSV
    importer`_, die besser zum regelmäßigen Import von neuen Daten geeignet
    sind.

.. _CSV importer: http://django-csv-importer.readthedocs.org/

Weiterführende Links zur Django Dokumentation
=============================================

* :djangodocs:`Informationen zu django-admin.py and manage.py <ref/django-admin/#ref-django-admin>`
* :djangodocs:`Daten für die Erstellung der Models bereit stellen <howto/initial-data/>`
