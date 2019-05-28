In your `views.py`, change the `index()` view function to the following:
```python
from .models import Article

def index(request):
    articles_list = Article.objects.all()
    context = {
        'articles': articles_list
    }
    return render(request, "index.html", context)
```
You can see in the return line we added a new argument to the `render()` call, the context. You can think of the context as a delivery mechanism. It delivers Python data, like objects and lists and dictionaries and so on, to the template (the `.html` file we created that displays the page.) The context is a dictionary variable that has a key "articles", its value is the list of article objects we got from the database. ([read more about the context here]())

Now, in your `index.html` file, change the `title` from:
```html
<title>Document</title>
```
to...
```html
<title>List Page</title>
```
since this is a list page now. And replace:
```html
<h1>This is a large header from HTML!</h1>
```
with...
```django
{% for article in articles %}
    {{ article.title }}
{% endfor %}
```
Django's comes with a templating language, a programming language we can use inside the HTML template to give them more dynamic functionality. In the code block above, we're writing a for-loop inside the HTML template. This is only possible because Django comes with this language and Django can compile this non-HTML code into proper HTML code for the browser. ([read more about Django templating here]().)

Let's dissect the code block above...

##### `{% for article in articles %}`
The `{% %}` tells Django that we are gonna write some logic here that isn't HTML.

The `for x in y` is a normal for-loop similar to Python. It technically isn't Python code, but it's designed to be familiar. The `article` is a variable name we're creating here. It contains a single element from the list `articles`. The `articles` bit comes from the *key* in the *context* dictionary we wrote in the view function. Its value is a list of `Article`s that we're looping through in the template here.

##### `{{ article.title }}`
The `{{ }}` tells Django that we are gonna display the value of a variable.

The variable is what we write between the `{{ }}`. In the `article.title` bit, the `article` is the `article` from the for-loop we wrote above. If we wrote `{% for blah in articles %}` above, we'd have to write `{{ blah.title }}` here. The `.title` part of it refers to the `title` field in the `Article` model we defined in the `models.py` file.

##### `{% endfor %}`
This line is how Django knows to end the for-loop in the template. Python knows the beginning and end of a for-loop by the indentations. However, HTML templates do not read whitespace like Python does, so for Django to know when the for-loop begins and where it ends, it knows that by the `{% for x in y %}` as the beginning of the loop, and the `{% endfor %}` as the end of the for-loop.

Now, if you save your code and run the server, you should see something different appear on the browser. I see this:
![Article List Page 1](https://i.imgur.com/LGRVgl1.png)

Now it displays all the article titles in the database. The magical thing we've done here is that if you go to the admin site to create another article, then go back to this page (without modifying the code at all), you'll see the new article you wrote here.

But they don't look like a list. The article titles are all on the same line. That doesn't look like a list page to me. Let's change that.

In the `index.html`, change the following:
```django
{% for article in articles %}
    {{ article.title }}
{% endfor %}
```
to...
```django
<ul>
    {% for article in articles %}
        <li>{{ article.title }}</li>
    {% endfor %}
</ul>
```
It now looks like this:
![Article List Page 2](https://i.imgur.com/o7cRac0.png)

Looks more like a list to me. Let's also change the naming to be more appropriate...

In your `urls.py`, change the following line:
```python
path('', views.index),
```
to...
```python
path('', views.article_list),
```
then, in your `views.py`, change the function name from `index` to `article_list`. Then, in the return line of the view function, change the name of the template from `index.html` to `list.html`.

Your view function should now be:
```python
def article_list(request):
    articles_list = Article.objects.all()
    context = {
        'articles': articles_list
    }
    return render(request, "list.html", context)
```
Lastly, change the template name from `index.html` to `list.html`, in the `templates/` foler.