# Article Create Page
Let's now allow the *users* to create their own articles!

We'll start by defining the *form* class for creating an `Article`. In your `forms.py`:
```python
from .models import Article

class ArticleForm(forms.ModelForm):
    class Meta:
        model = Article
        fields = ["title", "body", "author"]
```
This is a `ModelForm` associated with the `Article` model.

##### `fields = ["title", "body", "author"]`
In this line, we're including the  fields `title`, `body`, and `author` from the `Article` model. We're not adding the `created` field, because it should automatically be set when the article is created. The user shouldn't have to enter the current date and time every time they write a new article.

In your `urls.py`:
```python
urlpatterns = [
    [...]
    path('articles/create/', views.article_create, name="article-create"),
]
```
In your `views.py`:
```python
from .forms import ArticleForm

def article_create(request):
    form = ArticleForm()
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

Now let's add a button in the base template that takes us to the create page. In your `base.html`:
```django
{% if request.user.is_authenticated %}
    <a class="nav-item nav-link" href="{% url 'logout' %}">Logout</a>
    <a class="nav-item nav-link" href="{% url 'article-create' %}">Write!</a>
{% else %}
    <a class="nav-item nav-link" href="{% url 'register' %}">Register</a>
    <a class="nav-item nav-link" href="{% url 'login' %}">Login</a>
{% endif %}
```
Having the button *inside* the `{% if ... %}` means that only logged in users can write an article. Because the "Write!" button won't appear on the page if they're not logged in.

Create a new HTML file in your `templates/` folder, and call it `create.html`:
```django
{% extends "base.html" %}

{% block title %}
Write!
{% endblock %}

{% block body %}
<form action="{% url 'article-create' %}" method="POST">
    {% csrf_token %}
    {{ form.as_p }}
    <input type="submit">
</form>
<a href="{% url 'article-list' %}">
    <button>
        Cancel
    </button>
</a>
{% endblock %}
```

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
<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}{% endblock %}</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
</head>
<body>
    <nav class="navbar navbar-expand-lg navbar-light bg-light">
        <a class="navbar-brand" href="{% url 'article-list' %}">Superblog</a>
        <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNavAltMarkup" aria-controls="navbarNavAltMarkup" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNavAltMarkup">
            <div class="navbar-nav">
                {% if request.user.is_authenticated %}
                    <a class="nav-item nav-link" href="{% url 'logout' %}">Logout</a>
                    <a class="nav-item nav-link" href="{% url 'article-create' %}">Write!</a>
                {% else %}
                    <a class="nav-item nav-link" href="{% url 'register' %}">Register</a>
                    <a class="nav-item nav-link" href="{% url 'login' %}">Login</a>
                {% endif %}
            </div>
        </div>
    </nav>
    {% block body %}{% endblock %}
    <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
</body>
</html>
```