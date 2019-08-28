# Detail Page

This will be another page. Because of that, we need another `path()` in `urls.py`, another view in `views.py`, and another template in `templates/`.

But before we jump into the code for this page, let's go back to the shell again and cover some more Queryset Basics...

### More Queryset Basics

In your Terminal, run the following command:

```bash
(superblog_env)$ python manage.py shell
```

Like last time, let's get all the `Article`s and loop through them. But this time, we're gonna print something different:

```bash
>>> from articles.models import Article
>>> articles = Article.objects.all()
>>> for article in articles:
...     print(article.id)
```

Here we're printing the *id*s of the articles. Every model in Django comes _built-in_ with a unique identifier field to that particular object called "id".

Here's the output I got:

```bash
1
2
3
4
```

These are the unique identifiers, `id`s, for my articles. Yours will likely be the same, but if they're not, that's alright. Nothing to panic about.

We can use these `id`s to _get_ a particular article. Here's how:

```bash
>>> Article.objects.get(id=1)
```

This is what I get:

```bash
<Article: Article object (1)>
```

I can print its title to know which article it is:

```bash
>>> article = Article.objects.get(id=1)
>>> article.title
```

Here's what I got:

```bash
'What is Lorem Ipsum?'
```

This means that the article with `id` 1 is the article with the title "What is Lorem Ipsum?"

This knowledge is important to know before going into this next step...

In your `urls.py` file, at the end of the `urlpatterns` list, add the following line:

```python
urlpatterns = [
    [...]
    path('articles/<int:article_id>/', views.article_detail, name="article-detail"),
]
```

This is the `path()` definition we're using for the detail page. We can define a URL path once, and it can be used for all the article detail pages of the website. Here's how:

The `<int:article_id>` part indicates that this part of the URL can change, we're expecting it to be an integer (`int`), we're calling that number `article_id`. This variable will be passed to the view. Let's create that view!

Your `urls.py` should look like this:

```python
from django.contrib import admin
from django.urls import path
from articles import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', views.article_list, name="article-list"),
    path('articles/<int:article_id>/', views.article_detail, name="article-detail"),
]
```

In your `views.py`, let's create a new view function:

```python
def article_detail(request, article_id):
    article_object = Article.objects.get(id=article_id)
    context = {
        'article': article_object
    }
    return render(request, "detail.html", context)
```

The `article_id` function parameter must match the name of the URL parameter `<int:article_id>`.

Create a template in `templates/` and call it "`detail.html`". Write the following inside:

```django
<!DOCTYPE html>
<html>
<head>
    <title>Detail Page</title>
</head>
<body>
    <p>{{ article.author }}</p>
    <p>{{ article.title }}</p>
    <p>{{ article.body }}</p>
</body>
</html>
```

Now the detail page is ready! Remember the article `id`s we printed in the shell previously? Assuming one of those `id`s is 1, go to the URL `127.0.0.1:8000/articles/1`. If you don't have that id, pick one of the `id`s you printed previously and put it in the URL instead of the 1 I put. You should see something like this:
![Article Detail Page 1](https://i.imgur.com/3UszEgQ.png)

The first line in this page comes from `<p>{{ article.author }}</p>` in the template code, the second line in the page is `<p>{{ article.title }}</p>`. And the rest comes from `<p>{{ article.body }}</p>`.

By this point, your `views.py` should look like this:

```python
from django.shortcuts import render
from django.http import HttpResponse
from .models import Article

def article_list(request):
    articles_list = Article.objects.all()
    context = {
        'articles': articles_list
    }
    return render(request, "list.html", context)

def article_detail(request, article_id):
    article_object = Article.objects.get(id=article_id)
    context = {
        'article': article_object
    }
    return render(request, "detail.html", context)
```
