Since the user created an account and logged in before writing an article, we already know the name of the author. The user shouldn't be asked to enter their name every time they write a new article. So we can take care of that for them.

Firstly, we need to delete all the existing articles. This is necessary because of the changes we're doing next. In the real world, when your website is in production and the data is very critical and important to keep and not delete on a whim, you'll learn ways to avoid purging your database as we're doing now. But for now, for the purposes of keeping you focused on what matters and getting the fundamentals across, and because our website is in development and we don't have any critical data, we're gonna delete all the (few) existing articles we've created so far. ([read more about migrations here](https://docs.djangoproject.com/en/2.2/topics/migrations/#more-advanced-migrations))

In the Terminal, run the shell:
```bash
(superblog_env)$ python manage.py shell
```
Then run the following to delete all existing `Article`s:
```bash
>>> from articles.models import Article
>>> for article in Article.objects.all():
...     article.delete()
```
You should see an output like:
```bash
(1, {'articles.Article': 1})
(1, {'articles.Article': 1})
(1, {'articles.Article': 1})
(1, {'articles.Article': 1})
(1, {'articles.Article': 1})
```

Now we can make the changes to the model. If we had attempted to make changes to the model before deleting existing articles, Django will complain and tell you that the existing articles have data that don't match the structure you're changing to. So it won't allow it.

In the `Article` model we defined in our `models.py`, the `author` field is defined as:
```python
author = models.CharField(max_length=125)
```
This means that the author of an article is limited to a name as a string only. There's a far better way to define the `author` field. Change the `Article` model to the following:
```python
from django.db import models
from django.contrib.auth.models import User

class Article(models.Model):
    ...
    author = models.ForeignKey(User, on_delete=models.CASCADE)
    ...
```
Let's dissect that new line...

##### `author = models.ForeignKey(User, on_delete=models.CASCADE)`
Here, we're not defining the `author` as a string only. We're defining the `author` as a relationship between the two models, `Article` and `User` (built-in).

In Django, there's three model relationships:
- One To One: i.e. One user can have only and exactly one article.
- One To Many: i.e. One user can have many articles, and each article belongs to a single user.
- Many To Many: i.e. One user can have many articles, and each article can belong to multiple users.

A One To Many field in Django is called a `ForeignKey`. That's what we're using here. Because it makes perfect sense that any particular article is written by only a single author, and any author can write multiple articles.

The `User` in this line of code refers to the model we're making a relationship with. And `User` is the name of the user model we're using.

([read more about model relationships here](https://docs.djangoproject.com/en/2.2/topics/db/models/#relationships))

The bit with `on_delete=models.CASCADE` is required by Django. It tells Django what to do with the articles of a `User` object if it gets deleted from the database. In this line we're saying, if a `User` object is deleted, delete the `Article` objects associated with it also.

[Click here](https://docs.djangoproject.com/en/2.1/ref/models/fields/#django.db.models.ForeignKey.on_delete) to see other deletion options that come with Django.

Now that we've just made changes to our model, we need to migrate to apply those changes to the structure of the database. In the Terminal:
```bash
(superblog_env)$ python manage.py makemigrations
Migrations for 'articles':
  articles/migrations/0002_auto_20190417_1603.py
    - Alter field author on article
```
Then run:
```bash
(superblog_env)$ python manage.py migrate
Operations to perform:
  Apply all migrations: admin, articles, auth, contenttypes, sessions
Running migrations:
  Applying articles.0002_auto_20190417_1603... OK
```

In the `ArticleForm` class in your `forms.py`, change the `fields` list from:
```python
fields = ["title", "body", "author"]
```
to:
```python
fields = ["title", "body"]
```

Now the article create form doesn't display an input field for the author. Next, we need to provide the value for `author` to the article when it's being created.

In your `views.py`, change the following view:
```python
def article_create(request):
    ...
        if form.is_valid():
            article = form.save()
            ...
    ...
```
to:
```python
def article_create(request):
    ...
        if form.is_valid():
            article = form.save(commit=False)
            article.author = request.user
            ...
    ...
```

We added the line `article.author = request.user`. The bit that's new to you is the `request.user` part. When the user is logged in, `request.user` always contains the logged in `User` object. In this line, we're taking that `User` object and storing it as the new article's `author` attribute. ([read more about the `request` object here](https://docs.djangoproject.com/en/2.2/ref/request-response/))

Now if you login and write a new article, you'll be able to do so completely without entering an author name. Then, in the list page after submitting the article, you can click on the article to see its detail page, and you'll see the name of the author displaying. It will display the `username` of your account.

Let's make it display the first and last names of the author, instead of the username. It won't be difficult, since we're making an association between the two models, we can access the `User` object's attributes from the `Article` object it's associated with. Here's how...

In your `detail.html`, replace the following line:
```django
<p>{{ article.author }}</p>
```
with:
```django
<p>{{ article.author.first_name }} {{ article.author.last_name }}</p>
```

Now if you write a new article and go to its detail page, you'll see it now displays the first and last names of your account.

Let me explain how that works:

In the `detail.html` template, we have the `Article` object in a variable named `article`. So if we write `article` we get the `Article` object. This `article` object has the following fields as we have defined the model:
- title
- body
- author (ForeignKey to `User`)
- created

To access the title, we write `article.title`. To access the body we write `article.body`. What do we access when we write `article.author`? Well, because this is a relationship field, not a `CharField` or a `TextField` for example, we access the `User` object that is stored as this `article`'s `author`. And once we have the `User` object, we can access that `User` object's attributes too!

Django's built-in `User` has many existing fields, including the following:
- username
- first_name
- last_name
- email

So, since we have in the `detail.html` template the `Article` object as `article`, we can write `article.author.first_name` to get the first name of the author of the article.

### Pop Quiz
What will appear on the detail page if we replace the following:
```django
<p>{{ article.author.first_name }} {{ article.author.last_name }}</p>
```
with:
```django
<p>{{ article.author.email }}</p>
```
?

Answer: After you've guessed by thinking it through mentally, actually replace that line with the new line, go to the detail page and see for yourself. Remember to bring the code back to the way it was before this pop quiz so that your code is consistent with this lesson.