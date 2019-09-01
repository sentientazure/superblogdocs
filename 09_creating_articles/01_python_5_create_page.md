Let's now allow the _users_ to create their own articles! This is called a create view. Which is a page where the user fills out a form and submits it to create an object in the database.

We'll start by defining the _form_ class for creating an `Article`. In your `forms.py`:

```python
from .models import Article

class ArticleForm(forms.ModelForm):
    class Meta:
        model = Article
        fields = ["title", "body", "author"]
```

This is a `ModelForm` associated with the `Article` model.

##### `fields = ["title", "body", "author"]`

In this line, we're including the fields `title`, `body`, and `author` from the `Article` model. We're not adding the `created` field, because it should automatically be set when the article is created. The user shouldn't have to enter the current date and time every time they write a new article.

In your `urls.py`:

```python
urlpatterns = [
    [...]
    path('articles/create/', views.article_create, name="article-create"),
]
```

In your `views.py`:

```python
from django.shortcuts import redirect
from .forms import RegisterForm, LoginForm, ArticleForm

def article_create(request):
    form = ArticleForm()
    if request.user.is_anonymous:
        return redirect("login")

    if request.method == "POST":
        form = ArticleForm(request.POST)
        if form.is_valid():
            article = form.save()
            return redirect("article-list")
    context = {
        "form": form
    }
    return render(request, 'create.html', context)
```

This is the create view. You can see we added a new if-statement. `if request.user.is_anonymous:`. This if-statement will be `True` if the user is not logged in. In which case, we don't want logged in users to access this view, so we redirect them to the login page.

Now let's add a button in the base template that takes us to the create page. In your `base.html` under the `Home` button add the `Write!` button:

```django
<ul class="navbar-nav mr-auto">
    <li class="nav-item">
        <a class="nav-link {{ home_tab_status }}" href="{% url 'article-list' %}">
            Home
        </a>
    </li>
    {% if request.user.is_authenticated %}
        <li class="nav-item">
            <a class="nav-link" href="{% url 'article-create' %}">
                Write!
            </a>
        </li>
    {% endif %}
</ul>
```

Having the button _inside_ the `{% if ... %}` means that only logged in users can write an article. Because the "Write!" button won't appear on the page if they're not logged in.

Create a new HTML file in your `templates/` folder, and call it `create.html`. The article create page will consist of a single form that has the title and the body of the article the user is creating. We're gonna style it the same way we styled the login and registration form pages:

```django
{% extends "base.html" %}

{% block title %}
Write!
{% endblock %}

{% block body %}
<div class="card my-4">
    <h5 class="card-header">Write!</h5>
    <div class="card-body">
        <form action="{% url 'article-create' %}" method="POST">
            {% csrf_token %}
            <div class="form-group">
                <label for="id_title">Title</label>
                <input type="text" class="form-control" id="id_title" placeholder="Enter Title" name="title">
            </div>
            <div class="form-group">
                <label for="id_body">Body</label>
                <textarea class="form-control" id="id_body" placeholder="Write here..." rows="5" name="body"></textarea>
            </div>
            <button type="submit" class="btn btn-primary">Submit</button>
        </form>
    </div>
</div>
{% endblock %}
```

We got the [textarea from Bootstrap](https://getbootstrap.com/docs/4.3/components/forms/#form-controls). The `rows` attribute takes a positive integer value. The larger the number, the larger the text area input field will be when you first open the page. I tried different numbers and felt that 5 was appropriate. You can try changing it and refreshing the page to better understand what it does.

Now, if you're logged in you'll see a button that says "Write!" that takes you to the article create form page, with a cancellation button that takes you to the list page.

If you write out an article in the create page and submit, you'll be taken to the article list page with your new article there in the list. You can click on it to see the full article in another page.

---

By this point, your `urls.py` should look like this:

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
    path('articles/create/', views.article_create, name="article-create"),
]
```

Your `base.html` should be like:

```django
{% load static %}

<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}{% endblock %}</title>

    <!-- Bootstrap core CSS -->
    <link href="{% static 'vendor/bootstrap/css/bootstrap.min.css' %}" rel="stylesheet">

    <!-- Custom styles for this template -->
    <link href="{% static 'css/blog-home.css' %}" rel="stylesheet">

</head>
<body>
    <!-- Navigation -->
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark fixed-top">
        <div class="container">
            <a class="navbar-brand" href="#">Start Bootstrap</a>
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarResponsive" aria-controls="navbarResponsive" aria-expanded="false" aria-label="Toggle navigation">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarResponsive">
                <ul class="navbar-nav mr-auto">
                    <li class="nav-item">
                        <a class="nav-link {{ home_tab_status }}" href="{% url 'article-list' %}">
                            Home
                        </a>
                    </li>
                    {% if request.user.is_authenticated %}
                        <li class="nav-item">
                            <a class="nav-link" href="{% url 'article-create' %}">
                                Write!
                            </a>
                        </li>
                    {% endif %}
                </ul>
                <ul class="navbar-nav ml-auto">
                    {% if request.user.is_authenticated %}
                        <li class="nav-item">
                            <a class="nav-link" href="{% url 'logout' %}">
                                Logout
                            </a>
                        </li>
                    {% else %}
                        <li class="nav-item">
                            <a class="nav-link {{ login_tab_status }}" href="{% url 'login' %}">
                                Login
                            </a>
                        </li>
                        <li class="nav-item">
                            <a class="nav-link {{ register_tab_status }}" href="{% url 'register' %}">
                                Register
                            </a>
                        </li>
                    {% endif %}
                </ul>
            </div>
        </div>
    </nav>
    <div class="container">
        {% block body %}{% endblock %}
    </div>

    <!-- Bootstrap core JavaScript -->
    <script src="{% static 'vendor/jquery/jquery.min.js' %}"></script>
    <script src="{% static 'vendor/bootstrap/js/bootstrap.bundle.min.js' %}"></script>
</body>
</html>
```
