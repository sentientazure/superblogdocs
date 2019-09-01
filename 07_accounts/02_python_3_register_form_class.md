The register page consists essentially of a form with username, password, first name, last name, and email fields. To build tis page, let's start by defining the URL `path()` for the register page. In your `urls.py`:

```python
urlpatterns = [
    [...]
    path('register/', views.register_view, name="register"),
]
```

In your `views.py`:

```python
def register_view(request):
    return render(request, "register.html")
```

Create the template `register.html`. Leave it empty for now.

In your `forms.py`:

```python
from django.contrib.auth.models import User

class RegisterForm(forms.ModelForm):
    class Meta:
        model = User
        fields = ['username', 'password', 'email', 'first_name', 'last_name']

        widgets = {
            'password': forms.PasswordInput(),
        }
```

##### `class RegisterForm(forms.ModelForm):`

Here we're defining a form and calling it "`RegisterForm`". It subclasses Django's `ModelForm`. This `ModelForm` functions a bit differently than the previous `Form`. This `ModelForm` is a form that is associated with a model. As we're building the register feature on our website, it'll be clear what the difference between them is. It's better to show you than to explain it plainfully here.

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
    path('login/', views.login_view, name="login"),
    path('register/', views.register_view, name="register"),
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
