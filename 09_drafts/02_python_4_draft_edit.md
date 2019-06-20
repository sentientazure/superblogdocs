# Draft Edit
Let's create a page where the user can edit a draft!

In your `urls.py`:
```python
urlpatterns = [
    [...]
    path('drafts/<int:article_id>/', views.draft_edit, name="draft-edit"),
]
```

In your `views.py`:
```python
def draft_edit(request, article_id):
    article = Article.objects.get(id=article_id)
    form = ArticleForm(instance=article)
    context = {
        'form': form,
        'draft': article
    }
    return render(request, "draft_edit.html", context)
```

##### `form = ArticleForm(instance=article)`
When the user goes into the draft edit page, we want the draft `title` and `body` to already be there in the form, so that the user doesn't have to re-write everything every time they want to edit the draft.

That's exactly what this line does. The `instance=article` bit is what fills the form with the `title` and `body` of the `article` object. Django can easily do this because the form, `ArticleForm` is a `ModelForm` associated with the `Article` model. So the fields match.

Create `draft_edit.html` in your `templates/` folder:
```django
{% extends "base.html" %}

{% block title %}
Write!
{% endblock %}

{% block body %}
<form action="{% url 'draft-edit' draft.id %}" method="POST">
    {% csrf_token %}
    {{ form.as_p }}
    <input type="submit" name="publish" value="Publish">
    <input type="submit" name="draft" value="Save as draft">
</form>
<a href="{% url 'draft-list' %}">
    <button>
        Cancel
    </button>
</a>
{% endblock %}
```

In your `draft_list.html` template, change the following:
```django
<li>
    {{ draft.title }}
</li>
```
to:
```django
<li>
    <a href="{% url 'draft-edit' draft.id %}">
        {{ draft.title }}
    </a>
</li>
```

##### `<a href="{% url 'draft-edit' draft.id %}">`
We want to make it such that if the user clicks on the title of the draft they're taken to the draft edit page.

Now we can go to the draft edit page, let's handle submitting the edit form.

Change your `draft_edit()` view to:
```python
from django.utils.timezone import now

def draft_edit(request, article_id):
    article = Article.objects.get(id=article_id)
    form = ArticleForm(instance=article)
    if request.method == "POST":
        form = ArticleForm(request.POST, instance=article)
        if form.is_valid():
            article = form.save()
            if 'publish' in form.data:
                article.draft = False
                article.published = now()
                article.save()
                return redirect("article-detail", article_id=article.id)
            return redirect('draft-list')
    context = {
        'form': form,
        'draft': article
    }
    return render(request, "draft_edit.html", context)
```

##### `form = ArticleForm(request.POST, instance=article)`
This line will, instead of creating a new `Article` object, update the existing `Article` object sent to it in the `instance=article` bit.

##### `if 'publish' in form.data:`
If this condition is true, then the `Article` object's `draft` boolean field will be set to `False`, meaning that it's now published. If this condition is false, the article's `draft` will remain `False`.

This condition will be true if the user clicks the "Publish" button from the page, as discussed and explained previously.

##### `article.published = now()`
In this line, the function call `now()`, from Django's built-in `timezone` module, gets us the current date and time in a format that's compatible with Django's `DateTimeField`. ([learn more about Django's `DateTimeField` here](https://docs.djangoproject.com/en/2.2/ref/models/fields/#datetimefield))

##### `return redirect("article-detail", article_id=article.id)`
In this line we're redirecting the user to the article detail page if they chose to publish their article. The `article-detail` URL receives a URL parameter called `article_id`. To pass it a value when using the `redirect(...)` function call, we send the URL parameter as an argument to the function call, as we did here.