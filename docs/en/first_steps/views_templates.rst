The first views
***************

Now that you've entered some data with the admin application, the next step is to show this data in the front-end. Therefore three things need to be done:

    #. define URLs
    #. write views
    #. create templates

..
    .. note::

    Check :ref:`Grafik: Schematische Darstellung einer Request / Response
    Verarbeitung <grafik_request_response>`

Define URLs
===========

First, define the URLs which route the request to the views. For now two URLs will be sufficient. Open the file :file:`urls.py` and append the following code to the ``urlpatterns``::

    url(r'^recipes/(?P<slug>[-\w]+)/$', 'recipes.views.detail'),
    url(r'^$', 'recipes.views.index'),
    
The line from our first template isn't needed anymore and can be removed::

    url(r'^home/', TemplateView.as_view(template_name='base.html'))

The file :file:`urls.py` now should look like this::

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
        url(r'^recipes/(?P<slug>[-\w]+)/$', 'recipes.views.detail'),
        url(r'^$', 'recipes.views.index'),
    )

.. note::

    The ``url`` function's first argument is a `raw string <http://docs.python.org/reference/lexical_analysis.html#string-literals>`_ containing a regular expression.

    If you are not familiar with regular expressions you can read more in the Regular-Expression-HOWTO_, on Regular-Expressions.info_ or in an article from Doug Hellmann about the re-module_ erfahren. At RegexPlanet_ you can test regular expressions directly in the browser.

.. _Regular-Expression-HOWTO: http://docs.python.org/howto/regex.html
.. _Regular-Expressions.info: http://www.regular-expressions.info/
.. _re-module: http://www.doughellmann.com/PyMOTW/re/
.. _RegexPlanet: http://www.regexplanet.com/advanced/python/index.html

Now start the development server:

.. code-block:: bash

    $ python manage.py runserver
    Validating models...
    0 errors found

    Django version 1.4, using settings 'cookbook.settings'
    Development server is running at http://127.0.0.1:8000/
    Quit the server with CONTROL-C.

When you open the URL http://127.0.0.1:8000/ you will see an ``ViewDoesNotExist``
exception. That's correct because by now you didn't create any view.

How a template gets rendered
============================

Before we write the first views we'll look at how Django renders templates.

Django templates are normal Python objects whose constructor expects a string. The placeholders are replaced with the values from a context object.

The first example shows how a dictionary can be used as data structure

.. code-block:: bash    

    $ python manage.py shell

.. note::

    The command :program:`shell` loads the configurations from :file:`settings.py` of the current project. The normal Python interpreter wouldn't do that.

::

    >>> from django.template import Context, Template
    >>> t = Template('My name is {{ person.first_name }}.')
    >>> d = {'person': {'first_name': 'Foo'}}
    >>> t.render(Context(d))
    u'My name is Foo.'

In the second example we use a Python object as data structure::

    >>> class Person: pass
    ...
    >>> p = Person()
    >>> p.first_name = 'Bar'
    >>> c = Context({'person': p})
    >>> t.render(c)
    u'My name is Bar.'

Lists can be used as well::

    >>> t = Template('First article: {{ articles.0 }}')
    >>> c = Context({'articles': ['bread', 'eggs', 'milk']})
    >>> t.render(c)
    u'First article: bread'

Create the first view
=====================

Now we have to create the views. They will use the ORM to get the data from the database.

Open the file :file:`views.py` in the ``recipes`` application which you have created with the command :command:`startapp recipes`.

Most views return a ``HttpResponse`` object, so we write a really simple view which does exactly this::

    from django.http import HttpResponse


    def index(request):
        return HttpResponse('My first view.')

Now open http://127.0.0.1:8000/ in the browser. You will see the string you passed to the ``HttpResponse`` object.

Instead of the string, we'll now load a ``Template`` and render it with a ``Context`` containing a ``Recipe`` object. The rendered string from the ``Template`` is passed to the ``HttpResponse``::

    from django.http import HttpResponse
    from django.template import Context, loader

    from .models import Recipe


    def index(request):
        recipes = Recipe.objects.all()
        t = loader.get_template('recipes/index.html')
        c = Context({'object_list': recipes})
        return HttpResponse(t.render(c))

When opening http://127.0.0.1:8000/ you will see a ``TemplateDoesNotExist`` exception. It is raised because the template doesn't exist yet.

Create the list template
========================

Open the file :file:`base.html` from the :fiel:`templates` folder and change it this way:

.. code-block:: html+django

    <!doctype html>
    <html>
    <head>
        <meta charset="utf-8">
    	<title>{% block title %}Cookbook{% endblock %}</title>
    </head>
    <body>
        <h1>Cookbook</h1>
        {% block content %}{% endblock %}
    </body>
    </html>

It now contains two **blocks**. These will be filled by the templates which derive from this one.

Now you have to create two folders for the templates inside the application folder: :file:`recipes/templates/recipes/`. Create the file :file:`index.html` inside this new folders.

.. code-block:: html+django

    {% extends "base.html" %}

    {% block title %}{{ block.super }} - All Recipes{% endblock %}

    {% block content %}
    <h2>All Recipes</h2>
    <ul>
        {% for recipe in object_list %}
        <li><a href="/rezept/{{ recipe.slug }}/">{{ recipe.title }}</a></li>
        {% endfor %}
    </ul>
    {% endblock %}

The folder structure should now look like this:

.. code-block:: none

    cookbook
    |-- cookbook
    |   |-- __init__.py
    |   |-- settings.py
    |   |-- urls.py
    |   `-- wsgi.py
    |-- cookbook.db
    |-- manage.py
    |-- recipes
    |   |-- __init__.py
    |   |-- admin.py
    |   |-- fixtures
    |   |   `-- initial_data.json
    |   |-- models.py
    |   |-- templates
    |   |   `-- recipes
    |   |       `-- index.html
    |   |-- tests.py
    |   `-- views.py
    `-- templates
        `-- base.html

After restarting the development server and opening http://127.0.0.1:8000/ you will see a list of all recipes. 

Create the second view
======================

Now we need a second view to get the recipe details and begin with adding another import to the top :file:`views.py`::

    from django.http import Http404

At the bottom create a new method for the second view::

    def detail(request, slug):
        try:
            recipe = Recipe.objects.get(slug=slug)
        except Recipe.DoesNotExist:
            raise Http404
        t = loader.get_template('recipes/detail.html')
        c = Context({'object': recipe})
        return HttpResponse(t.render(c))

The whole files now looks like this::

    from django.http import Http404, HttpResponse
    from django.template import Context, loader

    from .models import Recipe


    def index(request):
        recipes = Recipe.objects.all()
        t = loader.get_template('recipes/index.html')
        c = Context({'object_list': recipes})
        return HttpResponse(t.render(c))


    def detail(request, slug):
        try:
            recipe = Recipe.objects.get(slug=slug)
        except Recipe.DoesNotExist:
            raise Http404
        t = loader.get_template('recipes/detail.html')
        c = Context({'object': recipe})
        return HttpResponse(t.render(c))

Create the detail template
==========================

Now we need the second template :file:`recipes/detail.html` inside the same folder :file:`index.html` is located. 

.. code-block:: html+django

    {% extends "base.html" %}

    {% block title %}{{ block.super }} - {{ object.title }}{% endblock %}

    {% block content %}
    <h2>{{ object.title }}</h2>
    <p>{{ object.number_of_portions }} portions.</p>
    <h3>Indigrents</h3>
    {{ object.ingredients|linebreaks }}
    <h3>Preparation</h3>
    {{ object.preparation|linebreaks }}
    <p>Preparation time: {{ object.time_for_preparation }} minutes</p>
    {% endblock %}

Now you can see the recipe details when clicking the links on the start page.

Why does the template engine hide non-existing variables?
=========================================================

The Django template engine ignores variables which aren't defined as key in the context. This is reasonable in a production environment, where you want the site to work even when a variable is missing.

You can add a line to :file:`settings.py` to change this behavior.

    TEMPLATE_STRING_IF_INVALID = 'TEMPLATE NAME ERROR'

Remember to remove this setting when the site goes live.

Escaping HTML and JavaScript
============================

Django's template engine escapes all HTML and JavaScript in the context because of security reasons. Imagine a user writes the following text into the field *preparation* of his recipe:

.. code-block:: html

    <script>alert("The world's best recipe!")</script>

The HTML would look like this:

.. code-block:: html

    <p>&lt;script&gt;alert(&quot;The world&#39;s best recipe!&quot;)&lt;/script&gt;</p>

The JavaScript won't be executed.

It's also possible to remove all HTML tags. To do this, you have to use the ``striptags`` filter:

.. code-block:: html+django

    {% block content %}
    <h2>{{ object.title }}</h2>
    <p>{{ object.number_of_portions }} portions.</p>
    <h3>Indigrents</h3>
    {{ object.ingredients|linebreaks }}
    <h3>Preparation</h3>
    {{ object.preparation|striptags|linebreaks }}
    <p>Preparation time: {{ object.time_for_preparation }} minutes</p>
    {% endblock %}

Now the HTML looks like this

.. code-block:: html

    <p>alert(&quot;The world&#39;s best recipe!&quot;)</p>

If you are sure that HTML and JavaScript should be rendered and execured, you can use the ``safe`` filter to allow it explicitly.

.. code-block:: html+django

    {% block content %}
    <h2>{{ object.title }}</h2>
    <p>{{ object.number_of_portions }} portions.</p>
    <h3>Indigrents</h3>
    {{ object.ingredients|linebreaks }}
    <h3>Preparation</h3>
    {{ object.preparation|safe|linebreaks }}
    <p>Preparation time: {{ object.time_for_preparation }} minutes</p>
    {% endblock %}

Now the JavaScript will be executed by the browser.

.. code-block:: html

    <p><script>alert("The world's best recipe!")</script></p>

.. note::

    This can lead to `XSS attacks`_ and should only be used when you are absolutely sure what you are doing.

.. _XSS attacks: https://de.wikipedia.org/wiki/Cross-Site-Scripting

Resources
=========

* :djangodocs:`The URL dispatcher <topics/http/urls/#topics-http-urls>`
* :djangodocs:`Creating views <topics/http/views/#topics-http-views>`
* :djangodocs:`Templates and their inheritance <topics/templates/#topics-templates>`
* :djangodocs:`Automatic escaping of HTML and JavaScript <topics/templates/#automatic-html-escaping>`
* :djangodocs:`Django templates for python developers <ref/templates/api/>`
