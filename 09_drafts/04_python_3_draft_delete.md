# Draft Delete

Let's give our users the option to delete a draft completely. Let's start with the button. In your `draft_edit.html` template, add the following delete button at the bottom of the `card-body` div:

```django
{% block body %}
<div class="card my-4">
    <h5 class="card-header">Edit your draft!</h5>
    <div class="card-body">
        [...]
        <a href="{% url 'draft-delete' draft.id %}">
            <button type="submit" class="btn btn-danger mt-1">
                Delete
            </button>
        </a>
    </div>
</div>
{% endblock %}
```

Now let's create the URL. In your `urls.py`:

```python
urlpatterns = [
    [...]
    path('drafts/<int:article_id>/delete/', views.draft_delete, name="draft-delete"),
]
```

In your `views.py`:

```python
def draft_delete(request, article_id):
    article = Article.objects.get(id=article_id)
    if request.user == article.author and article.draft:
        article.delete()

    return redirect("draft-list")
```

In here, the first line gets the article that the user decided to delete. Second line makes sure that only the author can delete it, and only if it's a draft. Third line deletes it. Then redirects the user to the draft list page.
