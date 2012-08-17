Die erste Applikation
*********************

Nun beginnen wir mit der ersten Applikation für unser Projekt "Kochbuch".

Das Datenmodell ist wie folgt aufgebaut:

.. graphviz:: models.dot

- unsere App heisst *recipes*
- es gibt zwei Models: *Recipe* und *Category*
- im Datenmodell fett gedruckte Felder sind Pflichtfelder
- das Feld *id* wird vom Django ORM automatisch als Primärschlüssel angelegt
- beide Models sind durch eine n-m Verknüpfung *category* verbunden
- *Recipe.author* ist mit dem *User* Model verbunden

Anlegen der Applikation
=======================

Die Applikation soll die Rezepte verwalten, also nennen wir sie
:file:`recipes`:

.. code-block:: bash

    $ cd cookbook
    $ python manage.py startapp recipes

Das Kommando legt ein Verzeichnis :file:`recipes` an, in dem sich vier Dateien
befinden:

.. code-block:: bash

    recipes/
    |-- __init__.py
    |-- models.py
    |-- tests.py
    `-- views.py

Die Datei :file:`__init__.py` definiert, wie schon beim
Konfigurationsverzeichnis, dass das Verzeichnis :file:`recipes` ein `Python
Paket <http://docs.python.org/tutorial/modules.html#packages>`_ ist. Die Models
der Applikation werden wir gleich in der Datei :file:`models.py` anlegen. Tests
werden in der Datei :file:`tests.py` erstellt. Die Datei :file:`views.py`
enthält die Views der Applikation.

Die Models
==========

Öffne die Datei :file:`models.py` in einem Texteditor. Sie enthält nur einen
``import``::

    from django.db import models

Damit wird das Paket, dass die Felder und andere Teile des ORMs enthält,
geladen.

Damit du später keine Probleme mit dem Encoding bekommst füge noch vor dem
``import`` folgende Zeile hinzu::

    # encoding: utf-8


Ein Model für die Kategorien
----------------------------

Unter diesen beiden Zeilen beginnen wir mit dem ersten Model für die
Kategorien::

    class Category(models.Model):
        """Category model."""
        name = models.CharField(u'Name', max_length=100)
        slug = models.SlugField(unique=True)
        description = models.TextField(u'Beschreibung', blank=True)

Das Model hat nun drei Attribute, die drei Feldern in einer Tabelle
entsprechen. Die Feldtypen definieren den Datentyp.

Das Attribut ``name`` entspricht zum Beispiel einem ``VARCHAR(100)`` in der
Datenbank.

Als ersten Parameter kann man optional einen Titel für das Feld angeben, der
dann in der Admin-Applikation benutzt wird.

Der Parameter ``blank=True`` ermöglicht es dieses Feld leer zu lassen. Alle
Felder eines Models sind also Pflichtfelder.

Nun wird die Klasse ``Category`` noch mit dem folgenden Code erweitert::

        class Meta:
            verbose_name = u'Kategorie'
            verbose_name_plural = u'Kategorien'

        def __unicode__(self):
            return self.name

Die Klasse ``Meta`` hat zwei Attribute, die den Namen des Models bestimmen.

Die Methode ``__unicode__`` soll einen Unicode-String zurückgeben. Dies wird
zum Beispiel in der Admin-Applikation benutzt.

Das Model für die Rezepte
-------------------------

Jetzt legen wir das zweite Model für die Rezepte an::

    class Recipe(models.Model):
        """Recipe model."""
        title = models.CharField(u'Titel', max_length=255)
        slug = models.SlugField(unique=True)
        ingredients = models.TextField(u'Zutaten',
            help_text=u'Eine Zutat pro Zeile angeben')
        preparation = models.TextField(u'Zubereitung')
        time_for_preparation = models.IntegerField(u'Zubereitungszeit',
            help_text=u'Zeit in Minuten angeben', blank=True, null=True)
        number_of_portions = models.PositiveIntegerField(u'Anzahl der Portionen')

Das Model ist dem ersten ähnlich. Neu ist der Parameter ``help_text``, der in
der Bearbeitungsansicht der Admin-Applikation als Hilfe benutzt wird.

Neu ist auch das ``IntegerField``. Wenn man bei diesem keine Eingabe verlangt
sollte man den Parameter ``null=True`` benutzen, denn sonst wird ein leerer
String benutzt.

Außerdem bekommt das Model noch fünf weitere Felder::

    difficulty = models.SmallIntegerField(u'Schwierigkeitsgrad')
    category = models.ManyToManyField(Category, verbose_name=u'Kategorien')
    author = models.ForeignKey(User, verbose_name=u'Autor')
    date_created = models.DateTimeField(editable=False)
    date_updated = models.DateTimeField(editable=False)

Hier stellen wir eine Relation zum Model ``Category`` mit Hilfe des Feldtyps
``ManyToManyField`` her. Da dieser als erstes Argument die Klasse erwartet,
mit der die Relation hergestellt werden soll, müssen wir den Bezeichner des
Felds in der Admin-Applikation mit dem Parameter ``verbose_name`` angeben.

Den Autor eines Rezepts legen wir über einen ``ForeignKey`` fest, also eine
1-n Beziehnung.

Die Zeitangaben sollen nicht in der Admin-Applikation bearbeitet werden,
deshalb benutzen wir den Parameter ``editable=False``.

Damit das Objekt ``User`` auch zur Verfügung steht muss vor dem ersten
``import`` ein weiterer eingefügt werden::

    from django.contrib.auth.models import User

Wir importieren das Model ``User`` aus einer Applikation mit dem Namen
``auth``, die Django mitbringt.

Das Feld ``difficulty`` ist vom Typ ``SmallIntegerField``. Nun sollen die
Benutzer nicht eine Zahl eingeben, sondern eine Auswahlliste benutzen. Deshalb
legen wir am Anfang der Klasse eine Liste von Auswahlmöglichkeiten an::

    DIFFICULTY_EASY = 1
    DIFFICULTY_MEDIUM = 2
    DIFFICULTY_HARD = 3
    DIFFICULTIES = (
        (DIFFICULTY_EASY, u'einfach'),
        (DIFFICULTY_MEDIUM, u'normal'),
        (DIFFICULTY_HARD, u'schwer'),
    )

Diese Verknüpfen wir mit dem Feld::

    difficulty = models.SmallIntegerField(u'Schwierigkeitsgrad',
        choices=DIFFICULTIES, default=DIFFICULTY_MEDIUM)

Zuletzt muss wieder eine ``Meta`` Klasse und eine ``__unicode__`` Methode
erstellt werden::

        class Meta:
            verbose_name = u'Rezept'
            verbose_name_plural = u'Rezepte'
            ordering = ['-date_created']

        def __unicode__(self):
            return self.title

Zusätzlich benutzen wir das Attribut ``ordering`` der ``Meta`` Klasse, um die
Standardsortierung der Datensätze zu bestimmen.

Außerdem wollen wir, dass die Zeitangaben automatisch ausgefüllt werden, da
sie ja nicht in der Admin-Applikation bearbeitet werden können. Dazu
überschreiben wir die Methode ``save``::

        def save(self, *args, **kwargs):
            if not self.id:
                self.date_created = now()
            self.date_updated = now()
            super(Recipe, self).save(*args, **kwargs)

Das Feld ``date_created`` wird nur gefüllt, wenn das Model zum ersten mal
gespeichert wird und daher noch kein Attribut ``id`` besitzt. Das Feld
``date_updated`` wird bei jedem Speichern aktualisiert. Am Ende wird die
``save`` Methode der Elternklasse mit Hilfe der Funktion super_ aufgerufen.

.. _super: http://docs.python.org/library/functions.html#super

Das Paket ``now`` müssen wir ebenfalls noch importieren. Also schreiben wir an
den Anfang der Datei::

    from django.utils.timezone import now

..  note::

    Mehr zum Thema ``import`` kannst du im :pep:`8`, in der `Python Dokumentation
    <http://docs.python.org/reference/simple_stmts.html#import>`_ sowie diesem
    `kurzen Artikel <http://effbot.org/zone/import-confusion.htm>`_ nachlesen.

Die vollständige Datei
======================

Die Datei ``models.py`` sollte nun so aussehen::

    # encoding: utf-8
    from django.contrib.auth.models import User
    from django.db import models
    from django.utils.timezone import now


    class Category(models.Model):
        """Category model."""
        name = models.CharField(u'Name', max_length=100)
        slug = models.SlugField(unique=True)
        description = models.TextField(u'Beschreibung', blank=True)

        class Meta:
            verbose_name = u'Kategorie'
            verbose_name_plural = u'Kategorien'

        def __unicode__(self):
            return self.name


    class Recipe(models.Model):
        """Recipe model."""
        DIFFICULTY_EASY = 1
        DIFFICULTY_MEDIUM = 2
        DIFFICULTY_HARD = 3
        DIFFICULTIES = (
            (DIFFICULTY_EASY, u'einfach'),
            (DIFFICULTY_MEDIUM, u'normal'),
            (DIFFICULTY_HARD, u'schwer'),
        )
        title = models.CharField(u'Titel', max_length=255)
        slug = models.SlugField(unique=True)
        ingredients = models.TextField(u'Zutaten',
            help_text=u'Eine Zutat pro Zeile angeben')
        preparation = models.TextField(u'Zubereitung')
        time_for_preparation = models.IntegerField(u'Zubereitungszeit',
            help_text=u'Zeit in Minuten angeben', blank=True, null=True)
        number_of_portions = models.PositiveIntegerField(u'Anzahl der Portionen')
        difficulty = models.SmallIntegerField(u'Schwierigkeitsgrad',
            choices=DIFFICULTIES, default=DIFFICULTY_MEDIUM)
        category = models.ManyToManyField(Category, verbose_name=u'Kategorien')
        author = models.ForeignKey(User, verbose_name=u'Autor')
        date_created = models.DateTimeField(editable=False)
        date_updated = models.DateTimeField(editable=False)

        class Meta:
            verbose_name = u'Rezept'
            verbose_name_plural = u'Rezepte'
            ordering = ['-date_created']

        def __unicode__(self):
            return self.title

        def save(self, *args, **kwargs):
            if not self.id:
                self.date_created = now()
            self.date_updated = now()
            super(Recipe, self).save(*args, **kwargs)

Die Applikation aktivieren
==========================

Damit wir die Applikation im Projekt nutzen können müssen wir sie in die
Konfiguration eintragen.

Öffne dazu die Datei :file:`settings.py` und füge den Namen unserer
Applikation am Ende von ``INSTALLED_APPS`` ein.

Danach sieht ``INSTALLED_APPS`` so aus::

    INSTALLED_APPS = (
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.sites',
        'django.contrib.messages',
        # Uncomment the next line to enable the admin:
        # 'django.contrib.admin',
        'recipes'
    )


Weiterführende Links zur Django Dokumentation
=============================================

* :djangodocs:`Allgemeine Informationen zu den Models <topics/db/models/#topics-db-models>`
* :djangodocs:`Alle in Django enthaltenen Feldtypen <ref/models/fields/#ref-models-fields>`
* :djangodocs:`Die Parameter der Meta Klasse <ref/models/options/#ref-models-options>`
