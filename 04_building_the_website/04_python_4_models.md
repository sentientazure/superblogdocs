# Models
In this application, we will have *articles*. Those articles that users will create and view, need to be stored somewhere. They don't come out of nowhere with a wave of a wand by a flying fairy in the land of unicorns and rainbows. That's what a database is for. The database needs to be structured before we can store anything in it. Meaning we need to tell it what kind of information we're gonna store, and what type each bit of data is. Django, fortunatelly, makes it very easy for us.

What does an article have? When you go to an article, what information do we keep about each article? Well, each article has a title, and the body of the article. We also want to keep track of when it was written, both the date and the time of the day. We also want to know who wrote which article, so the author. For now, we can settle for these data points. Later on we may want to add more information on each article.

The way we tell the database the structure of the data we're storing is by writing a *model* class. According to [Django's official documentation](https://docs.djangoproject.com/en/2.2/topics/db/models/):

> A model is the single, definitive source of information about your data. It contains the essential fields and behaviors of the data you’re storing. Generally, each model maps to a single database table.

In your `models.py` file, write the following class:
```python
from django.db import models

class Article(models.Model):
    title = models.CharField(max_length=125)
    body = models.TextField()
    author = models.CharField(max_length=125)
    created = models.DateTimeField(auto_now_add=True)
```

Let's dissect this code block...
##### `class Article(models.Model):`
A model is a Python class that *subclasses* Django's `Model` class. That `Model` class exists in a module that comes with Django called `models`. We import the `models` module above and use its `Model` class. Django recognizes this class as a model because it subclasses Django's `Model` class. We called our model class `Article`. Our model should be called `Article`, singular, not `Articles`, plural. This is a Pythonic naming convention, where classes' names are singular not plural. You can read more about Python code conventions [here](https://www.python.org/dev/peps/pep-0008/).

##### `title = models.CharField(max_length=125)`
The type of data the article title has is text. It's also a short single-line text, it cannot be multiple paragraphs. Or at least we don't want it to be. This type of data is called a `CharField`, and it must have a maximum size. In this line, we're defining a field called `title` and we're setting it to a `CharField` with a maximum length of 125 characters. What this means is that an article in our website has a title that cannot be larger than 125 characters.

##### `body = models.TextField()`
Now the body of the article can be as large as the author wants it to be. We don't want to limit our users to writing short articles. So to define it to be a text without a maximum size, we use Django's `TextField`. We're calling this field the `body` of the article.

##### `author = models.CharField(max_length=125)`
We're defining an `author` field that will contain the name of the author writing the article. We also gave it a maximum size of 125 characters.

##### `created = models.DateTimeField(auto_now_add=True)`
This field, `created`, contains the date and time of when the article is created. We said `auto_now_add=True` and that means that this field will contain the date and time of when the article is created. This field will be automatically filled by Django the second the article is created.

([read more about the different model fields in Django here](https://docs.djangoproject.com/en/2.2/ref/models/fields/))

By default, Django interprets the current date and time according to the UTC timezone. If you live somewhere else in the world, you need to change the `TIME_ZONE` in `settings.py`, you can find it at the bottom of the file. Click [here](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) for a list of timezones. Enter the "*TZ database name*" from the list. For example, if you live in Chicago, then you'd write `America/Chicago` in your `TIME_ZONE` variable in `settings.py`.