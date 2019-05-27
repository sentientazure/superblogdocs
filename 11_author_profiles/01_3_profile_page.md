Let's build a page where you can see all the articles that an author has published. That'll be the author's profile page. Anywhere an author's name is displayed, we'll make it into a *link* that if clicked will take the user to that author's profile page, displaying all the published articles written by that author.

To start, let's write the URL, the view, then the template. In your `urls.py`:
```python
urlpatterns = [
    ...
    path('profiles/<str:username>/', views.author_profile, name="author-profile"),
]
```

Let's make a quick change in the models, change the following line:
```python
class Article(models.Model):
    ...
    author = models.ForeignKey(User, on_delete=models.CASCADE)
    ...
```
to:
```python
class Article(models.Model):
    ...
    author = models.ForeignKey(User, on_delete=models.CASCADE, related_name="articles")
    ...
```

No need to migrate this change because it doesn't change the database structure. We're using this change in the next step. In your `views.py`:
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
We're filtering the set of `Article`s whose `author` is `user`, with `draft` set to `False`. Which means give me the *published* articles written by this `user`.

We're using the `related_name` we just added in the models here in this line as the `articles` in `user.articles`. Writing `user.articles` is the same as writing `Article.objects.filter(author=user)`.

##### `.order_by("-published")`
This will order the set of articles by their `published` field value. Which means it'll give us the list with the most recent articles first. Removing the "`-`" will reverse the order.

Create a new template `author_profile.html`:
```django
<!DOCTYPE html>
<html>
<head>
    <title>Author Profile</title>
</head>
<body>
    <p>First Name: {{ user.first_name }}</p>
    <p>Last Name: {{ user.last_name }}</p>
    <p>Email: {{ user.email }}</p>

    {% for article in articles %}
        <a href="/articles/{{ article.slug }}/">
            {{ article.title }}
        </a>
        <p>{{ article.published|timesince }} ago</p>
    {% endfor %}
</body>
</html>
```

This will display the author's first and last names, their email, and their list of published articles ordered by most recent.

Let's make the author's name in the article detail page be a link to the profile page. In your `detail.html`, change the following:
```django
<p>Published by: {{ article.author.first_name }} {{ article.author.last_name }}</p>
```
to:
```django
<p>Published by: <a href="/profiles/{{ article.author.username }}/">{{ article.author.first_name }} {{ article.author.last_name }}</a></p>
```

Now in the article detail page, the author name is a link to that author's profile page, displaying all the articles they published.

What if the author whose profile we're visiting doesn't have any published articles? Instead of not displaying anything at all, it would be better if the user would see a message that indicates clearly that this author has no published articles. In your `author_profile.html`, change the following:
```django
...
{% for article in articles %}
    <a href="/articles/{{ article.slug }}/">
        {{ article.title }}
    </a>
    <p>{{ article.published|timesince }} ago</p>
{% endfor %}
...
```
to:
```django
...
{% if not articles %}
    <p>This user has not published any articles.</p>
{% else %}
    {% for article in articles %}
        <a href="/articles/{{ article.slug }}/">
            {{ article.title }}
        </a>
        <p>{{ article.published|timesince }} ago</p>
    {% endfor %}
{% endif %}
...
```