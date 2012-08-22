Database
********

Now we want to update the database and and then start the development webserver,
to fill the cookbook over the admin interface.

Validate the data model
=======================

With the following command you can validate the your data model:

..  code-block:: bash

    $ python manage.py validate

Django generally checks the data model on all operations, which use models. 
This command allows you to explicitly check the validity of your model.

Synchronise the database
========================

The database is filled by SQL Queries. Django create those for you, but 
if you are interested whats going on the following command shows you
the queries:

..  code-block:: bash

    $ python manage.py sqlall recipes

..  code-block:: sql

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

To execute these queries and to create the tables and indices with it, you have
to run the following command.::

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
    Creating table recipes_category
    Creating table recipes_recipe_category
    Creating table recipes_recipe
    Installing custom SQL ...
    Installing indexes ...
    Installed 0 object(s) from 0 fixture(s)


Run the development webserver
=============================

After the database update you can start the development server again:

..  code-block:: bash

    $ python manage.py runserver
    Validating models...

    0 errors found
    Django version 1.4, using settings 'cookbook.settings'
    Development server is running at http://127.0.0.1:8000/
    Quit the server with CONTROL-C.

Under the URL http://127.0.0.1:8000/admin/recipes/ you can now create some recipes.

Export and import data with JSON
================================

Django supports an export and import functionality to allow an exchange
of data between different systems. With the command :program:`dumpdata` 
you can export the models you created from the application ``recipes``::

    $ mkdir recipes/fixtures
    $ python manage.py dumpdata --indent 4 recipes > recipes/fixtures/initial_data.json

Django loads the fixtures from a file named :file:`initial_data.json` everytime
when you run :program:`syncdb`. That means, that the data you just exported 
will be autimatically loaded when you delete or create models in the future.

It is also possible to load data manually with the following command :program:`loaddata`::

    $ python manage.py loaddata recipes/fixtures/initial_data.json
    Installed 4 object(s) from 1 fixture(s)

.. note::

    :program:`loaddata` is not the very best solution to load data from different sources,
    because the fixture alway contain primary keys.
    There are different Apps, for example `CSV
    importer`_, that is more suited for importing new data.

.. _CSV importer: http://django-csv-importer.readthedocs.org/

Further Django ressources
=========================

* :djangodocs:`information about django-admin.py and manage.py <ref/django-admin/#ref-django-admin>`
* :djangodocs:`Provide data for the creation of the models <howto/initial-data/>`
