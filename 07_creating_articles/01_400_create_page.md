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
    ...
    path('create/', views.article_create, name="article-create"),
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

Now let's add a button in the article list page that takes us to the create page. In your `list.html`:
```django
{% if request.user.is_authenticated %}
    ...
    <a href="{% url 'article-create' %}">
        <button>
            Write!
        </button>
    </a>
{% else %}
```
Having the button *inside* the `{% if ... %}` means that only logged in users can write an article. Because the "Write!" button won't appear on the page if they're not logged in.

Create a new HTML file in your `templates/` folder, and call it `create.html`:
```django
<!DOCTYPE html>
<html>
<head>
    <title>Write!</title>
</head>
<body>
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
</body>
</html>
```

Now, if you're logged in, from the list page you'll see a button that says "Write!" that takes you to the article create form page, with a cancellation button that takes you back to the list page.

If you write out an article in the create page and submit, you'll be taken back to the article list page with your new article there in the list. You can click on it to see the full article in another page.