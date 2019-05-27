Let's now allow our users to write an article and save it as a draft that only they can see, and they can still edit it until they decide to publish it. Once published a draft is no longer editable.

The way the user will be able to save a draft, is by having a button on the article create form that says "Save as draft". Once clicked, that article will then be saved as a draft.

To be able to tell apart, in our database, an article saved as a draft and an article that is submitted and published, we will have a field in the `Article` model called `draft`. This field will be either true or false. If it's true, then we know this article is saved as a draft, if it's false then we know this is a published article.

In your `models.py`:
```python
class Article(models.Model):
    ...
    draft = models.BooleanField(default=False)
    published = models.DateTimeField(null=True)
```

##### `draft = models.BooleanField(default=False)`
Django's models have a field called `BooleanField`. It is as it sounds. Its value can be either true or false. We're setting the default here to `False`. That's because all the existing `Article`s in the database don't have a value for the newly created field `draft`. So they will have their `draft` field's value be set to the default. And so they'll continue their status as published.

##### `published = models.DateTimeField(null=True)`
Since the user can write an article, save it as a draft, and not publish it yet, we want to keep track of when an article is published. This way any reader can know how old an article is from the publish date.

We set `null=True`, which means that this field can be *empty* in the database. Without setting this, Django won't allow you to migrate, because it'll ask you to set a default value for existing articles. But this is ok, because if an article is created and saved as a draft, it doesn't have a publish date and time, so we keep it empty until its published. 

After changing the structure of the models, we must migrate:
```bash
(superblog_env)$ python manage.py makemigrations
(superblog_env)$ python manage.py migrate
```

Let's create a button in the `create.html` page. Replace the following form:
```django
<form action="{% url 'article-create' %}" method="POST">
    {% csrf_token %}
    {{ form.as_p }}
    <input type="submit">
</form>
```
with:
```django
<form action="{% url 'article-create' %}" method="POST">
    {% csrf_token %}
    {{ form.as_p }}
    <input type="submit" value="Publish">
    <input type="submit" name="draft" value="Save as draft">
</form>
```

You can see now we have *two* submit buttons. Each of them has a different `value` attribute, and that attribute is the text that will be displayed on the button. You can go to the article create page to see. The old "submit" button now says "Publish". The new submit button says "Save as draft". They both also have a `name` attribute. We'll need those in the next step. The views. ([learn more about the `<input>` HTML tag here](https://www.w3schools.com/tags/tag_input.asp))

In your `article_create()` view, change the if-statement inside from:
```python
def article_create(request):
    ...
        if form.is_valid():
            article = form.save(commit=False)
            article.author = request.user
            ...
    ...
```
to:
```python
def article_create(request):
    ...
        if form.is_valid():
            article = form.save(commit=False)
            article.author = request.user
            if 'draft' in form.data:
                article.draft = True
            else:
                article.published = now()
            ...
    ...
```

##### `if 'draft' in form.data`
This condition will be `True` if the user clicked on the "Save as draft" button, it will be `False` if the user clicked on the "Publish" button. Below we'll walk through exactly how this works.

##### `article.draft = True`
Here we're setting the `article`'s `draft` to `True`. This will only happen if the condition above it is `True`.

##### `article.published = now()`
If the user clicks on the "Publish" button, we set the `article`'s `published` to the current date and time, using Django's timezone's `now()`.

Let me show you what `form.data` contains. (You can see for yourself by printing it in the view and creating a new article. If you just wanna see what it is without actually creating a new article, then comment out the code inside the `if form.is_valid()` statement and just add a print statement.)

```js
{
  'csrfmiddlewaretoken': [
    '7jFq3fPYi6Vbf3E85NFRLPf5cfw7S2aGWChC2qg6O5GFC20sNBu2YLEDEVpfJURT'
  ],
  'title': [
    'The title of the article'
  ],
  'body': [
    'The entire body of the article is written here.'
  ],
  'draft': [
    'Save as draft'
  ]
}
```

This is what `form.data` is if I entered "The title of the article" in the title of the article and "The entire body of the article is written here." in the body of the article and pressed the "Save as draft" button.

If I do the exact same and press on the "Publish" button, this is what it will look like:

```js
{
  'csrfmiddlewaretoken': [
    'Aur6wjP936L525NdlzhfdACvaZDBsridpN3ivughz5wzp49x3n6qqw13CFwJjjZq'
  ],
  'title': [
    'The title of the article'
  ],
  'body': [
    'The entire body of the article is written here.'
  ]
}
```

You can see here that `form.data` doesn't have the `draft` key in it, if you press the "Publish" button.

You see, the reason the keys "title", "body", and "csrfmiddlewaretoken" are there is because those are the fields in the form. The `csrfmiddlewaretoken` is there because of the following line in our `create.html`:
```django
{% csrf_token %}
```

It's for security, Django handles it entirely for us. You can also see that in the two examples above, the value of this token changes every time you submit a form.

The `title` and `body` keys are there because in the following line:
```django
{{ form.as_p }}
```

we're rendering a form. We're getting that form from the context in the view, the `article_create()` view. The form in that context we're getting from the following line:
```python
form = ArticleForm()
```

Which is the `ArticleForm` that we defined in our `forms.py`. That form is a `ModelForm` associated with the `Article` model. The `fields` list in it has `["title", "body"]`. That's exactly where the `title` and `body` from the dictionary above come from.

However, the key "draft" from when we created the article and pressed the "Save as draft" button comes from somewhere else. In the `create.html` template we have the following line:
```django
<input type="submit" name="draft" value="Save as draft">
```

The `name` attribute is where that "draft" key comes from.

Alright, so let's create a draft. You can see the draft is visible in the article list. It's not supposed to. If you go to that `Article` object in the admin site, you'll see it *is* saved as a draft. You'll see it has a `draft` field, a checkbox, and it's checked. Which means its set to `True`.

Now we want the article list page to display articles that have been published only. In your `article_list()` view, replace the following line:
```python
articles_list = Article.objects.all()
```
with:
```python
articles_list = Article.objects.filter(draft=False)
```

Now, instead of getting *`.all()`* the `Article`s, we're `.filter()`-ing only the published articles. Those are the `Article`s with `draft` set to `False`.

Now if you go to the article list page, you won't see the draft we just made.

But now... How can I, as the logged in author of a draft, see my drafts? How can I edit and then publish the draft I just made? Well... I, as the developer, have to build that feature. Let's!

Let's create a button in the article list page that takes us to a page that displays all of our drafts. This button will not appear for users who are not logged in, because they're not allowed to write articles, let alone drafts.

In your `list.html` template:
```django
...
{% if request.user.is_authenticated %}
    ...
    <a href="{% url 'draft-list' %}">
        <button>
            Drafts
        </button>
    </a>
{% else %}
...
```

Next, let's create the url. In your `urls.py`:
```python
urlpatterns = [
    ...
    path('drafts/', views.draft_list, name="draft-list"),
]
```

In your `views.py`:
```python
def draft_list(request):
    drafts_list = Article.objects.filter(draft=True, author=request.user)
    context = {
        'drafts': drafts_list
    }
    return render(request, "drafts_list.html", context)
```

##### `Article.objects.filter(draft=True, author=request.user)`
This line will get us the list of articles whose fields are as follows:
- `draft` is `True`
- `author` is the logged in user

This will get us the drafts made by the logged in user. If other users wrote drafts, they won't appear on this page because this query will only retrieve the drafts made by the logged in user. As explained previously, `request.user` is a variable that contains the currently logged in `User` object.

Create a template in your `templates/` folder and call it `drafts_list.html`:
```django
<!DOCTYPE html>
<html>
<head>
    <title>Drafts Page</title>
</head>
<body>
    <a href="{% url 'article-list' %}">
        <button>
            Back
        </button>
    </a>
    <ul>
        {% for draft in drafts %}
            <li>
                {{ draft.title }}
            </li>
        {% endfor %}
    </ul>
</body>
</html>
```