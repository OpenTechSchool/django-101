The first application
*********************

Now we start with the first application for our *Cookbook* project.

Here is the data model:

.. graphviz:: models.dot

- the app's name is *recipes*
- there are two models: *Recipe* and *Category*
- bold fields in the model are required
- the field *id* will be generated automatically by Django's ORM and used as primary key
- both models are connected through a many-to-many relationship named *category*
- *Recipe.author* is connected to the *User* model

Creating the application
========================

The application has to store recipes, so we name it :file:`recipes`:

.. code-block:: bash

    $ cd cookbook
    $ python manage.py startapp recipes

This command creates a folder :file:`recipes` which contains four files:

.. code-block:: bash

    recipes/
    |-- __init__.py
    |-- models.py
    |-- tests.py
    `-- views.py

As in the :file:`cookbook` directory, the file :file:`__index__.py` makes the folder a `Python package <http://docs.python.org/tutorial/modules.html#packages>`_. The application's models go into :file:`models.py`. Tests go into :file:`tests.py` and views into :file:`views.py`.

The models
==========

Open the file :file:`models.py` in a text editor. It only contains an import::

    from django.db import models

The ``models`` module contains the fields and other parts of the ORM.

To make your life easier with special characters, add the following line above the ``import``::

    # encoding: utf-8


The model for the categories
----------------------------

Below these two lines we start with the first model for the categories::

    class Category(models.Model):
        """Category model."""
        name = models.CharField(u'Name', max_length=100)
        slug = models.SlugField(unique=True)
        description = models.TextField(u'Description', blank=True)

The models has three attributes which correspond to three rows in a table. The field types define data type.

For instance the attribute ``name`` leads to ``VARCHAR(100)`` in the database.

The first parameter is optional and can be used to give the field a name which will be used as label in the admin application.

The parameter ``blank=True`` allows the field to be empty. All fields are required in default.

Now we extend the class ``Category`` with the following code::

        class Meta:
            verbose_name = u'Category'
            verbose_name_plural = u'Categories'

        def __unicode__(self):
            return self.name

The class ``Meta`` has two attributes which define the model's name.

The ``__unicode__`` method returns a unicode string. This will be used in the admin application amongst other things.

The models for the recipes
--------------------------

Now we create the second model for the recipes::

    class Recipe(models.Model):
        """Recipe model."""
        title = models.CharField(u'Title', max_length=255)
        slug = models.SlugField(unique=True)
        ingredients = models.TextField(u'Indigrents',
            help_text=u'One indigrent per line')
        preparation = models.TextField(u'Preparation')
        time_for_preparation = models.IntegerField(u'Preparation time',
            help_text=u'In minutes', blank=True, null=True)
        number_of_portions = models.PositiveIntegerField(u'Number of portions')

This models is similar to the first one. We introduced the parameter ``help_text`` which will be shown as help text in the admin application's edit mode.

There is also an ``IntegerField``. You should use ``null=True`` here if no input is required, because otherwise an empty string will be used.

Now add five more fields to the model::

    difficulty = models.SmallIntegerField(u'Difficulty')
    category = models.ManyToManyField(Category, verbose_name=u'Categories')
    author = models.ForeignKey(User, verbose_name=u'Author')
    date_created = models.DateTimeField(editable=False)
    date_updated = models.DateTimeField(editable=False)

Here we use a ``ManyToManyField`` to create a relation to the ``Category`` model. The ``ManyToManyField`` expects the related model class as first argument. Therefore we have to define the label in the admin interface with the named parameter ``verbose_name``.

The recipe's author is stored in a ``ForeignKey`` field which represents a many-to-one relation.

The time values shouldn't be editable in the admin application, so we set the parameter ``editable=False``.

The ``User`` object has to be imported to be available. We import it from Django's auth application::

    from django.contrib.auth.models import User

The field ``difficulty`` is a ``SmallIntegerField``. Because the users shouldn't have to enter a number but get a list, we create the choices at the beginning of the model class::

    DIFFICULTY_EASY = 1
    DIFFICULTY_MEDIUM = 2
    DIFFICULTY_HARD = 3
    DIFFICULTIES = (
        (DIFFICULTY_EASY, u'easy'),
        (DIFFICULTY_MEDIUM, u'normal'),
        (DIFFICULTY_HARD, u'hard'),
    )

These we link with the field ``difficulty``::

    difficulty = models.SmallIntegerField(u'Difficulty',
        choices=DIFFICULTIES, default=DIFFICULTY_MEDIUM)

We also add a ``Meta`` class and a ``__unicode__`` method::

        class Meta:
            verbose_name = u'Recipe'
            verbose_name_plural = u'Recipes'
            ordering = ['-date_created']

        def __unicode__(self):
            return self.title

In addition we use the attribute ``ordering`` in the ``Meta`` class to define the default ordering.

The time values should be filled automatically because we didn't allow them to be edited in the admin application. To make this happen we override the ``save`` method::

        def save(self, *args, **kwargs):
            if not self.id:
                self.date_created = now()
            self.date_updated = now()
            super(Recipe, self).save(*args, **kwargs)

The field ``date_created`` only gets filled when the model is saved the first time what is determined by ``id`` not having a value. The field ``date_updated`` will be refreshed every time the model gets saved. At the bottom of the ``save`` method we call the super_ function.

.. _super: http://docs.python.org/library/functions.html#super

The package now also has to be imported so we add the following line to the top of the file::

    from django.utils.timezone import now

.. note::

    You can read more about ``import`` in :pep:`8`, in the `Python documentation <http://docs.python.org/reference/simple_stmts.html#import>`_ and in this `article <http://effbot.org/zone/import-confusion.htm>`_.

The whole file
==============

The file ``models.py`` now should look like this::

    # encoding: utf-8
    from django.contrib.auth.models import User
    from django.db import models
    from django.utils.timezone import now


    class Category(models.Model):
        """Category model."""
        name = models.CharField(u'Name', max_length=100)
        slug = models.SlugField(unique=True)
        description = models.TextField(u'Description', blank=True)

        class Meta:
            verbose_name = u'Category'
            verbose_name_plural = u'Categories'

        def __unicode__(self):
            return self.name


    class Recipe(models.Model):
        """Recipe model."""
        DIFFICULTY_EASY = 1
        DIFFICULTY_MEDIUM = 2
        DIFFICULTY_HARD = 3
        DIFFICULTIES = (
            (DIFFICULTY_EASY, u'easy'),
            (DIFFICULTY_MEDIUM, u'normal'),
            (DIFFICULTY_HARD, u'hard'),
        )
        title = models.CharField(u'Title', max_length=255)
        slug = models.SlugField(unique=True)
        ingredients = models.TextField(u'Indigrents',
            help_text=u'One indigrent per line')
        preparation = models.TextField(u'Preparation')
        time_for_preparation = models.IntegerField(u'Preparation time',
            help_text=u'Zeit in Minuten angeben', blank=True, null=True)
        number_of_portions = models.PositiveIntegerField(u'Number of portions')
        difficulty = models.SmallIntegerField(u'Difficulty',
            choices=DIFFICULTIES, default=DIFFICULTY_MEDIUM)
        category = models.ManyToManyField(Category, verbose_name=u'Categories')
        author = models.ForeignKey(User, verbose_name=u'Author')
        date_created = models.DateTimeField(editable=False)
        date_updated = models.DateTimeField(editable=False)

        class Meta:
            verbose_name = u'Recipe'
            verbose_name_plural = u'Recipes'
            ordering = ['-date_created']

        def __unicode__(self):
            return self.title

        def save(self, *args, **kwargs):
            if not self.id:
                self.date_created = now()
            self.date_updated = now()
            super(Recipe, self).save(*args, **kwargs)

Activating the application
==========================

To use the application in our project, it has to be activated in the configuration.

Open the file :file:`settings.py` and add the application's name the end of ``INSTALLED_APPS``.

``INSTALLED_APPS`` now looks like this::

    INSTALLED_APPS = (
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.sites',
        'django.contrib.messages',
        # Uncomment the next line to enable the admin:
        'django.contrib.admin',
        'recipes'
    )


Resources
=========

* :djangodocs:`Basic information about models <topics/db/models/#topics-db-models>`
* :djangodocs:`Django's built-in fields <ref/models/fields/#ref-models-fields>`
* :djangodocs:`The parameters of the Meta class <ref/models/options/#ref-models-options>`
