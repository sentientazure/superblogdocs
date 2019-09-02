In the starter project you cloned there's a folder called `templates/` that's been already created for you inside the `articles/` app folder. This folder had to be named "templates" exactly because that's where Django looks for HTML files. Even though we can write our HTML code in the view function's return, it won't be easy for us to write the entire HTML page, for every webpage on our website, inside the views alongside our Python code. It's much more organized and well-structured to contain our HTML code in their own HTML files, and use them from the views. Well, that's what we're doing here!

Create a file inside the `articles/templates/` folder called `index.html`. This is the HTML file that will be displayed on the page a little later. Let's write some HTML!

### HTML

In your `index.html`:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Document</title>
  </head>
  <body>
    <h1>This is a large header from HTML!</h1>
  </body>
</html>
```

In your `views.py`, change the view function to:

```python
from django.shortcuts import render

def index(request):
    return render(request, "index.html")
```

Now what the return line does is it just "_renders_" the HTML file and returns it to the browser, the browser then displays it. Whatever we wrote in `index.html` will be displayed on the browser. Your webpage would look like this:
![HTML](https://i.imgur.com/E0YSLA2.png)

The project file structure should now be:

```
superblog/
    manage.py
    superblog/
        __init__.py
        settings.py
        urls.py
        wsgi.py
    articles/
        __init__.py
        templates/
            blog_home.html
            blog_post.html
            index.html
        migrations/
        admin.py
        apps.py
        models.py
        tests.py
        views.py
```

---

### Git

This is a good point to commit changes. Let's create a checkpoint here for adding the template:

```bash
(superblog_env)$ git add .
(superblog_env)$ git commit -m "Added templates"
```
