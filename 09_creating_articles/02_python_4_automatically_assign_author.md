Since the user created an account and logged in before writing an article, we already know the name of the author. The user shouldn't be asked to enter their name every time they write a new article. So we can take care of that for them.

---

### Preparations

Firstly, to avoid certain issues that will popup from adding a new attribute to the model, we'll delete all the existing articles. The new structure will have an attribute that the old/existing `Article`s don't. This mismatch will cause Django to complain, because it doesn't know what value to fill the new attribute for the old `Article`s.

Deleting the existing articles is necessary because of the changes we're doing next. In the real world, when your website is in production and the data is very critical and important to keep and not delete on a whim, you'll learn ways to avoid purging your database as we're doing now. But for now, for the purposes of keeping you focused on what matters and getting the fundamentals across, and because our website is in development and we don't have any critical data, we're gonna delete all the (few) existing articles we've created so far. ([read more about migrations here](https://docs.djangoproject.com/en/2.2/topics/migrations/#more-advanced-migrations))

---

### Django Shell

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

---

### Article Author

In the `Article` model we defined in our `models.py`, the `author` field is defined as:

```python
author = models.CharField(max_length=125)
```

This means that the author of an article is limited to a name as a string only. There's a far better way to define the `author` field. Change the `Article` model to the following:

```python
from django.db import models
from django.contrib.auth.models import User

class Article(models.Model):
    title = models.CharField(max_length=125)
    body = models.TextField()
    author = models.ForeignKey(User, on_delete=models.CASCADE)
    created = models.DateTimeField(auto_now_add=True)
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
```

You should see an output similar to this:

```bash
Migrations for 'articles':
  articles/migrations/0002_auto_20190417_1603.py
    - Alter field author on article
```

Then run:

```bash
(superblog_env)$ python manage.py migrate
```

You should see an output similar to this:

```bash
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
    [...]
        if form.is_valid():
            article = form.save()
            return redirect("article-list")
    [...]
```

to:

```python
def article_create(request):
    [...]
        if form.is_valid():
            article = form.save(commit=False)
            article.author = request.user
            article.save()
            return redirect("article-list")
    [...]
```

Your create view should look like this:

```python
def article_create(request):
    form = ArticleForm()
    if request.method == "POST":
        form = ArticleForm(request.POST)
        if form.is_valid():
            article = form.save(commit=False)
            article.author = request.user
            article.save()
            return redirect("article-list")
    context = {
        "form": form
    }
    return render(request, 'create.html', context)
```

We added the line `article.author = request.user`. The bit that's new to you is the `request.user` part. When the user is logged in, `request.user` always contains the logged in `User` object. In this line, we're taking that `User` object and storing it as the new article's `author` attribute. ([read more about the `request` object here](https://docs.djangoproject.com/en/2.2/ref/request-response/))

Now if you login and write a new article, you'll be able to do so completely without entering an author name. Then, in the list page after submitting the article, you can click on the article to see its detail page, and you'll see the name of the author displaying. It will display the `username` of your account.
