The admin application
*********************

With Django it's easy to integrate your own application into the admin
application.

Registering the application in the admin app
============================================

To use our application with the admin app, we have to register its models.

This is done by creating the file :file:`admin.py` in the application
directory. The project structure now looks like this:

.. code-block:: none

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

Now open :file:`admin.py` in your editor and insert the following lines::

    from django.contrib import admin
    from .models import Category, Recipe

This makes the admin and your app's models available.

.. note::

    The second ``import`` is a relative import. These are defined in
    :pep:`328` and implemented in Python 2.6 and above. In Python 2.5 you
    have to insert the following line before the relative import::

        from __future__ import absolute_import

Next we create a class to register the model ``Category`` with the admin::

    class CategoryAdmin(admin.ModelAdmin):
        prepopulated_fields = {'slug': ['name']}


    admin.site.register(Category, CategoryAdmin)

And that's all.

The attribute ``prepopulated_fields`` tells the admin application to
automatically fill the field ``slug`` - in this case with the text entered
into the ``name`` field.

Now we do the same with the ``Recipe`` model::

    class RecipeAdmin(admin.ModelAdmin):
        prepopulated_fields = {'slug': ['title']}


    admin.site.register(Recipe, RecipeAdmin)

The whole file
--------------

The file :file:`admin.py` should now look like this (starting with Python
2.6, for Python 2.5 see the note above)::

    from django.contrib import admin
    from .models import Category, Recipe


    class CategoryAdmin(admin.ModelAdmin):
        prepopulated_fields = {'slug': ['name']}


    class RecipeAdmin(admin.ModelAdmin):
        prepopulated_fields = {'slug': ['title']}


    admin.site.register(Category, CategoryAdmin)
    admin.site.register(Recipe, RecipeAdmin)


Resources
=========

* :djangodocs:`Information about the admin application <ref/contrib/admin/#ref-contrib-admin>`
