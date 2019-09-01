The login page consists essentially of a form with username and password fields. To build this page, let's start by defining the URL `path()`. In your `urls.py`:

```python
urlpatterns = [
    [...]
    path('login/', views.login_view, name="login"),
]
```

In your `views.py`:

```python
def login_view(request):
    return render(request, "login.html")
```

Create the template `login.html`. Leave it empty for now.

We're gonna use Django's class-based forms. They make it easier and simpler for us to use forms on our website. In your `articles/` folder, create a file called `forms.py`. It should be alongside your `models.py` and `views.py`.

In your `forms.py`:

```python
from django import forms

class LoginForm(forms.Form):
    username = forms.CharField(required=True)
    password = forms.CharField(required=True, widget=forms.PasswordInput())
```

In Django, we can define our forms as Python classes that subclass Django's built-in `Form` class. Let's dissect this code!

##### `class LoginForm(forms.Form):`

Here we're defining a form that we will later use on our website, and we're calling this form "`LoginForm`". What makes this a form, not just a Python class, is that it subclasses Django's `Form` class.

##### `username = forms.CharField(required=True)`

Here we're defining, as part of this form, a field called "`username`", and the type of data this form field will have is a string, hence the familiar `CharField` bit. We're also saying that the username field in this form is _required_, hence the `required=True` bit.

##### `password = forms.CharField(required=True, widget=forms.PasswordInput())`

This is the same as the `username`, but we're calling it `password`. However, when the user is typing the password in the form, we don't want the actual text they're typing to appear on the page. Usually when you login to any website, when you type the password you see •••••• appear instead of what you're actually typing. The way we can do that is by giving this form field a _widget_. Django's forms have plenty of widgets for various input methods. One of them is the `PasswordInput()` widget. As you can assume, giving this widget to a `CharField`, as we're doing here, will display those dots (••••) instead of what the user types. ([read more about Django forms' widgets here](https://docs.djangoproject.com/en/2.2/ref/forms/widgets/))

[(Read more about Django forms here.)](https://docs.djangoproject.com/en/2.2/topics/forms/)

Your `urls.py` should look like this:

```python
from django.contrib import admin
from django.urls import path
from articles import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', views.article_list, name="article-list"),
    path('articles/<int:article_id>/', views.article_detail, name="article-detail"),
    path('login/', views.login_view, name="login"),
]
```

And your `views.py` should look like this:

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

def login_view(request):
    return render(request, "login.html")
```

That was the login form, next we'll create the register form.
