# Profile Page

Let's build a page where you can see all the articles that an author has published. That'll be the author's profile page. Anywhere an author's name is displayed, we'll make it into a _link_ that if clicked will take the user to that author's profile page, displaying all the published articles written by that author.

To start, let's write the URL, the view, then the template. In your `urls.py`:

```python
urlpatterns = [
    [...]
    path('profiles/<str:username>/', views.author_profile, name="author-profile"),
]
```

Let's make a quick change in the models, change the following line:

```python
class Article(models.Model):
    [...]
    author = models.ForeignKey(User, on_delete=models.CASCADE)
    [...]
```

to:

```python
class Article(models.Model):
    [...]
    author = models.ForeignKey(User, on_delete=models.CASCADE, related_name="articles")
    [...]
```

No need to migrate this change because it doesn't change the database structure. `related_name` is something we can use to get the list of `Article` objects associated with a certain `User` object. You'll see how we'll use it next. (Read more about `related_name` [here](https://docs.djangoproject.com/en/2.2/ref/models/fields/#django.db.models.ForeignKey.related_name)).

In your `views.py`:

```python
from django.contrib.auth.models import User

def author_profile(request, username):
    user = User.objects.get(username=username)
    articles = user.articles.filter(draft=False).order_by("-published")
    context = {
        'user': user,
        'articles': articles,
    }
    return render(request, 'author_profile.html', context)
```

##### `user = User.objects.get(username=username)`

In this line we're getting the `User` object with the `username` we're receiving from the URL.

##### `user.articles.filter(draft=False)`

We're filtering the set of `Article`s whose `author` is `user`, with `draft` set to `False`. Which means give me the _published_ articles written by this `user`.

We're using the `related_name` we just added in the models here in this line as the `articles` in `user.articles`. Writing `user.articles` is the same as writing `Article.objects.filter(author=user)`.

##### `.order_by("-published")`

This will order the set of articles by their `published` field value. Which means it'll give us the list with the most recent articles first. Removing the "`-`" will reverse the order.

Create a new template `author_profile.html`:

```django
{% extends "base.html" %}

{% block title %}
Author Profile
{% endblock %}

{% block body %}
<div class="row">
    <div class="col-lg-12">
        <div class="card mb-4">
            <h2 class="card-header">{{ user.username }}</h2>
            <div class="card-body">
                <p class="card-text">
                    First Name: {{ user.first_name }}
                </p>
                <p class="card-text">
                    Last Name: {{ user.last_name }}
                </p>
                <p class="card-text">
                    Email: {{ user.email }}
                </p>
            </div>
        </div>
        {% if not articles %}
            This user has not published any articles.
        {% else %}
            {% for article in articles %}
                <div class="card mb-4">
                    <div class="card-body">
                        <h2 class="card-title"><a href="{% url 'article-detail' article.slug %}">{{ article.title }}</a></h2>
                    </div>
                    <div class="card-footer text-muted">
                        Published on {{ article.published }}
                    </div>
                </div>
            {% endfor %}
        {% endif %}
    </div>
</div>
{% endblock %}
```

This will display the author's username, first and last names, their email, and their list of published articles ordered by most recent.

##### `{% if not articles %}`

This line checks if the list of `Article`s we get from the context is empty. If it's empty, we'll display a message saying there's no articles.

Let's make the author's name in the article detail and list pages be a link to the profile page. In your `detail.html`, change the following:

```django
by {{ article.author.username }}
```

to:

```django
by <a href="{% url 'author-profile' article.author.username %}">{{ article.author.username }}</a>
```

In your `list.html`, change the following:

```django
Published on {{ article.published }} by {{ article.author.username }}
```

to:

```django
Published on {{ article.published }} by <a href="{% url 'author-profile' article.author.username %}">{{ article.author.username }}</a>
```

Now in the article detail page, the author name is a link to that author's profile page, displaying all the articles they published.
