Let's create a page where the user can edit a draft!

This feature covers both trello cards "_As an author, I can edit a draft_" and "_As an author, I can publish a draft._" Move them from Backlog to Doing.

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

Create `draft_edit.html` in your `templates/` folder. For this page, we'll copy the styles for the article create page with minor modifications:

```django
{% extends "base.html" %}

{% block title %}
Draft Edit Page
{% endblock %}

{% block body %}
<div class="card my-4">
    <h5 class="card-header">Edit your draft!</h5>
    <div class="card-body">
        <form action="{% url 'draft-edit' draft.id %}" method="POST">
            {% csrf_token %}
            <div class="form-group">
                <label for="id_title">Title</label>
                <input type="text" class="form-control" id="id_title" placeholder="Enter Title" name="title" value="{{ form.title.value }}">
            </div>
            <div class="form-group">
                <label for="id_body">Body</label>
                <textarea class="form-control" id="id_body" placeholder="Write here..." rows="5" name="body">{{ form.body.value }}</textarea>
            </div>
            <button type="submit" class="btn btn-primary" name="draft">Save as draft</button>
            <button type="submit" class="btn btn-primary">Publish</button>
        </form>
    </div>
</div>
{% endblock %}
```

##### `value="{{ form.title.value }}"`

This part of the title input field is what sets the draft's old title in the title input field so that the user can see what they wrote before in this draft.

##### `<textarea [...]>{{ form.body.value }}</textarea>`

The way to put the existing value for the `<textarea>` is by putting the old value between the opening and closing tags. Unlike the `<input>` element which takes the value as an attribute called `value`.

In your `draft_list.html` template, change the following:

```django
<div class="card-body">
    <h2 class="card-title">{{ draft.title }}</h2>
</div>
```

to:

```django
<div class="card-body">
    <h2 class="card-title">
        <a href="{% url 'draft-edit' draft.id %}">
            {{ draft.title }}
        </a>
    </h2>
</div>
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
            if 'draft' not in form.data:
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

##### `if 'draft' not in form.data:`

If this condition is true, then the `Article` object's `draft` boolean field will be set to `False`, meaning that it's now published. If this condition is false, the article's `draft` will remain `False`.

This condition will be true if the user clicks the "Publish" button from the page, as discussed and explained previously.

##### `article.published = now()`

In this line, the function call `now()`, from Django's built-in `timezone` module, gets us the current date and time in a format that's compatible with Django's `DateTimeField`. ([learn more about Django's `DateTimeField` here](https://docs.djangoproject.com/en/2.2/ref/models/fields/#datetimefield))

##### `return redirect("article-detail", article_id=article.id)`

In this line we're redirecting the user to the article detail page if they chose to publish their article. The `article-detail` URL receives a URL parameter called `article_id`. To pass it a value when using the `redirect(...)` function call, we send the URL parameter as an argument to the function call, as we did here.

---

Move the cards to Done, and commit changes to git.
