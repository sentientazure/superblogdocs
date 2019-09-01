# Registration and Login Forms

These two pages consist essentially of forms. Login page is a form with username and password fields. Registration page is a form with username, password, first name, last name, and email fields.

Let's start by defining two URL `path()`s, one for login and one for register. In your `urls.py`:

```python
urlpatterns = [
    [...]
    path('register/', views.register_view, name="register"),
    path('login/', views.login_view, name="login"),
]
```

In your `views.py`:

```python
def login_view(request):
    return render(request, "login.html")

def register_view(request):
    return render(request, "register.html")
```

Create the two templates, `register.html` and `login.html`. Leave them empty for now.

We're gonna use Django's class-based forms. They'll make it easier and simpler for us to use forms on our website. In your `articles/` folder, create a file called `forms.py`. It should be alongside your `models.py` and `views.py`.

In your `forms.py`:

```python
from django import forms
from django.contrib.auth.models import User

class LoginForm(forms.Form):
    username = forms.CharField(required=True)
    password = forms.CharField(required=True, widget=forms.PasswordInput())

class RegisterForm(forms.ModelForm):
    class Meta:
        model = User
        fields = ['username', 'password', 'email', 'first_name', 'last_name']

        widgets = {
            'password': forms.PasswordInput(),
        }
```

In Django, we can define our forms as Python classes that subclass Django's built-in `Form` class, or the `ModelForm` class that we use in the `RegisterForm` class. Let's dissect this code!

##### `class LoginForm(forms.Form):`

Here we're defining a form that we will later use on our website, and we're calling this form "`LoginForm`". What makes this a form, not just a Python class, is that it subclasses Django's `Form` class.

##### `username = forms.CharField(required=True)`

Here we're defining, as part of this form, a field called "`username`", and the type of data this form field will have is a string, hence the familiar `CharField` bit. We're also saying that the username field in this form is _required_, hence the `required=True` bit.

##### `password = forms.CharField(required=True, widget=forms.PasswordInput())`

This is the same as the `username`, but we're calling it `password`. However, when the user is typing the password in the form, we don't want the actual text they're typing to appear on the page. Usually when you login to any website, when you type the password you see •••••• appear instead of what you're actually typing. The way we can do that is by giving this form field a _widget_. Django's forms have plenty of widgets for various input methods. One of them is the `PasswordInput()` widget. As you can assume, giving this widget to a `CharField`, as we're doing here, will display those dots (••••) instead of what the user types. ([read more about Django forms' widgets here](https://docs.djangoproject.com/en/2.2/ref/forms/widgets/))

##### `class RegisterForm(forms.ModelForm):`

Here we're defining another form and calling it "`RegisterForm`". It subclasses Django's `ModelForm`. This `ModelForm` functions a bit differently than the previous `Form`. This `ModelForm` is a form that is associated with a model. As we're building the register feature on our website, it'll be clear what the difference between them is. It's better to show you than to explain it plainfully here.

##### `class Meta:`

Since we're creating a `ModelForm`, we need to specify which model this form works with. The `Meta` class configures this.

##### `model = User`

This is a field in the `Meta` class. Here's where we define which model this `ModelForm` is associated with. We're importing Django's built-in `User` model, and using it here. ([read more about Django's built-in `User` model here](https://docs.djangoproject.com/en/2.2/ref/contrib/auth/#user-model))

##### `fields = ['username', 'password', 'email', 'first_name', 'last_name']`

In the `LoginForm` form above, we defined the fields of the form and their types one by one, which were `username` and `password`. In this form, the `RegisterForm`, we're associating it with a model, the `User` model. In this case, we only need to specify which fields from the model will be in the form, and Django's `ModelForm` knows the type of each field because they're defined, along with their types, in the User model.

In the `fields` list, we only need to specify the names of the fields as strings in a list.

##### `widgets = {'password': forms.PasswordInput(),}`

This is how we give the `password` field in this `ModelForm` the `PasswordInput()` widget.

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
    path('register/', views.register_view, name="register"),
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

def register_view(request):
    return render(request, "register.html")
```

We just finished defining the forms, now let's display them on the website!
