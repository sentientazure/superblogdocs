Views are Python functions that receive a web request, do whatever logic necessary, and return a web response. That response is usually an HTML file that displays the webpage for the user. We're about to write our first view!

You should see the following already existing in the file:

```python
from django.shortcuts import render

# Create your views here.

```

Let's add the following:

```python
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello World!")
```

This is a view function that returns a web response with the text "Hello World!".

Now, open up the `urls.py` file in your project folder. You should see the following already there by default:

```python
urlpatterns = [
    path('admin/', admin.site.urls),
]
```

Add the following to it:

```python
from articles import views
urlpatterns = [
    [...]
    path('', views.index),
]
```

The first line imports the entire `views` package from the `articles` app. The line with `path` that we added means that if we go to the URL `127.0.0.1:8000`, it will execute the view function called _index_. Next we will create the view.

Your `urls.py` should look like this:

```python
from django.contrib import admin
from django.urls import path
from articles import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', views.index),
]
```

Now if you go to `127.0.0.1:8000` you should see the following page:
![Hello World!](https://i.imgur.com/qFh3OTR.png)
We just built a page by ourselves and now we're viewing it as a website in the browser. How did that happen exactly?

---

### Git

This is a good point to introduce you to the git workflow. [Git](https://git-scm.com/) is a universally loved and adored tool for developers to organize their software/web development work and collaboration. It does many things beautifully and elegantly. Here we're gonna make basic use of it. We're gonna make checkpoints in our code, called "commits". Those checkpoints have label messages describing what this checkpoint has over the prior checkpoints. The value of this is that if we ever mess up our code, we can go back to a previous checkpoint before we messed it up where we know it works. It also keeps track of the history of changes of our project, as we make checkpoints.

Let's make our first checkpoint!

In your Terminal, make sure you're where `manage.py` is, and run the following commands:

```bash
(superblog_env)$ git add .
(superblog_env)$ git commit -m "Hello World page!"
```

The first command stages the entire project for a checkpoint commit. The second command creates the checkpoint (or commit) itself, and gives it the label message "Hello World page!". This way we know that this checkpoint contains the hello world page.

Usually we'd make a commit everytime we added something to our project, no matter how small it is. Any atomic change should be committed. There's an [etiquette](https://github.com/trein/dev-best-practices/wiki/Git-Commit-Best-Practices) behind all this. I strongly recommend you learn this etiquette and apply it to the best of your ability. It'll save you heartache and much more.
