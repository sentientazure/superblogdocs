# Logout Page
Let's create a button in the base template that appears if the user is logged in, and once clicked will log the user out. Let's start by adding the button, then work our way back to the view.

In your `base.html`, below the "Login" button:
```django
<body>
    <a href="{% url 'register' %}">
        <button>
            Register
        </button>
    </a>
    <a href="{% url 'login' %}">
        <button>
            Login
        </button>
    </a>
    {% if request.user.is_authenticated %}
        <a href="{% url 'logout' %}">
            <button>
                Logout
            </button>
        </a>
    {% endif %}
    {% block body %}{% endblock %}
</body>
```
Here we're seeing two new things, let's dissect them!

##### `{% if request.user.is_authenticated %}`
This is how you'd write an if-statement in Django's templates. `request` is always accessible in the template code, without needing to send it in the context. `request.user` always refers to the logged in user object. The `.is_authenticated` bit is a field in Django's built-in `User` model, it returns `True` if this user is logged in currently, and `False` otherwise.

##### `{% endif %}`
This line is how Django knows to end the if-statement. Just to reiterate, Python knows the end of an if-statement by reading the indentations in the beginning of the lines. However, HTML doesn't read whitespace like Python does, so the only way for Django to know where the if-statement ends is by having this `{% endif %}` tag.

Then, in your `urls.py`:
```python
urlpatterns = [
    [...]
    path('logout/', views.logout_view, name="logout"),
]
```
In your `views.py`:
```python
from django.contrib.auth import login, authenticate, logout

def logout_view(request):
    logout(request)
    return redirect("login")
```
Django comes built-in with a function to logout the user. We simply need to call the function, `logout(request)`, and it logs the user out of the session. Then we're `redirect`ing the user to the login page.([read more about the `logout(...)` function here](https://docs.djangoproject.com/en/2.2/topics/auth/default/#django.contrib.auth.logout))

Now if you login, you'll see both the login and logout buttons. Ideally, when you're logged in you only see the logout button, and when you're logged out you only see the login button. Let's make that happen!

In your `base.html`, replace the following:
```django
<a href="{% url 'register' %}">
    <button>
        Register
    </button>
</a>
<a href="{% url 'login' %}">
    <button>
        Login
    </button>
</a>
{% if request.user.is_authenticated %}
    <a href="{% url 'logout' %}">
        <button>
            Logout
        </button>
    </a>
{% endif %}
```
with:
```django
{% if request.user.is_authenticated %}
    <a href="{% url 'logout' %}"><button>
        Logout
    </button></a>
{% else %}
    <a href="{% url 'register' %}">
        <button>
            Register
        </button>
    </a>
    <a href="{% url 'login' %}">
        <button>
            Login
        </button>
    </a>
{% endif %}
```
What we did here is if the user is logged in the logout button will be displayed, else (if the user is not logged in) the register and login buttons will appear.

Here we have a full-fledged if-statement within our template. This is possible because of Django's built-in templating language.

---

Now, your `urls.py` should look like this:
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
    path('logout/', views.logout_view, name="logout"),
]
```

Your `views.py` should be like this:
```python
from django.shortcuts import render, redirect
from django.http import HttpResponse
from django.contrib.auth import login, authenticate, logout

from .forms import RegisterForm, LoginForm
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
    form = LoginForm()
    if request.method == 'POST':
        form = LoginForm(request.POST)
        if form.is_valid():
            username = form.cleaned_data['username']
            password = form.cleaned_data['password']

            auth_user = authenticate(username=username, password=password)
            if auth_user is not None:
                login(request, auth_user)
                return redirect('article-list')

    context = {
        'form': form
    }
    return render(request, "login.html", context)

def register_view(request):
    form = RegisterForm()
    if request.method == 'POST':
        form = RegisterForm(request.POST)
        if form.is_valid():
            user = form.save(commit=False)

            user.set_password(user.password)
            user.save()

            login(request, user)
            return redirect("article-list")

    context = {
        "form": form,
    }
    return render(request, 'register.html', context)
```

And your `base.html` should be like this:
```django
<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}{% endblock %}</title>
</head>
<body>
    {% if request.user.is_authenticated %}
        <a href="{% url 'logout' %}"><button>
            Logout
        </button></a>
    {% else %}
        <a href="{% url 'register' %}">
            <button>
                Register
            </button>
        </a>
        <a href="{% url 'login' %}">
            <button>
                Login
            </button>
        </a>
    {% endif %}
    {% block body %}{% endblock %}
</body>
</html>
```