Making queries - Set Up
========================================================
Following the documentation at https://docs.djangoproject.com/en/5.0/topics/db/queries/

* Start a new project called QuerieTutorial with an app called queries.
* Make a new directory

```shell
mkdir QuerieTutorial
```

* Create a virtual environment within this new directory. 

```shell
python -m venv .venv
```

* Activate the virtual environment called .venv
```shell
source .venv/bin/activate
```

* Install Django
```shell
pip install django
```

* Create a new Django project called pizzeria_project. Don't forget to add the period (or dot) . at the end!
```shell
django-admin startproject queries_project .
```

* Create the database
```shell
python manage.py migrate
```

* Run the Django development server
```shell
python manage.py runserver
```

* Quit the server with CONTROL-C. Create the app queries
```shell
python manage.py startapp queries
```

* Edit the settings file and add the queries app 
```python3
# settings.py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    "queries.apps.QueriesConfig",  # new
]
```

* Define the models
```python3
# models.py

from django.db import models

class Blog(models.Model):
    name = models.Model(max_length=100)
    tagline = models.TextField()

    def __str__(self):
        return self.name

class Author(models.Model):
    name = models.CharField(max_length=200)
    email = models.EmailField()

    def __str__(self):
        return self.name

class Entry(models.Model):
    blog = models.ForeignKey(Blog, on_delete=models.CASCADE)
    headline = models.CharField(max_length=255)
    body_text = models.TextField()
    pub_date = models.DateField()
    mod_date = models.DateField()
    authors = models.ManyToManyField(Author)
    number_of_comments = models.IntegerField()
    number_of_pingbacks = models.IntegerField()
    rating = models.IntegerField()
    
    def __str__(self):
        return self.headline
```

* Refresh the database
```shell
python manage.py makemigrations

python manage.py migrate 
```

* Start interactive shell session
```shell
python manage.py shell
```

* Creating objects
```shell
from queries.models import Blog
b = Blog(name="Beatles Blog", tagline="All the latest Beatles news.")
b.save()
```

* Saving changes to objects
```shell
b.name = "New name"
b.save()
```

* Saving ForeignKey and ManyToManyField fields
```shell
from queries.models import Blog, Entry
entry = Entry.objects.get(pk=1)
cheese_blog = Blog.objects.get(name="Cheddar Talk")
entry.blog = cheese_blog

from queries.models import Author
joe = Author.objects.create(name="Joe")
entry.authors.add(joe)
```

* Add multiple records to a ManyToManyField field
```shell
paul = Author.objects.create(name="Paul")
george = Author.objects.create(name="George")
ringo = Author.objects.create(name="Ringo")
entry.authors.add(paul, george, ringo)
```

* Retrieving objects
```shell
all_entries = Entry.objects.all()
Entry.objects.filter(pub_date__year=2023)
Entry.objects.all().filter(pub_date__year=2023)
```

* Chaining filters
This takes the initial QuerySet of all entries in the database, adds a filter, then an exclusion, then another filter. 
```shell
Entry.objects.filter(headline__startswith="My second").exclude(pub_date__gte=datetime.date.today()).filter(pub_date__gte=datetime.date(2005, 1, 30))
```
