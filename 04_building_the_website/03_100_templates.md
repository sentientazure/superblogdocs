Create a folder *inside the `articles/` folder* called `templates/`. Make sure you name it ***exactly*** as "templates". That's because Django knows to look for HTML files in the folder called "templates". If you name it something different Django won't see the HTML files inside. Then create a file inside `templates/` called `index.html`. This is the HTML file that will be displayed on the page a little later. Let's write some HTML!

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
Now what the return line does is it just "*renders*" the HTML file and returns it to the browser, the browser then displays it. Whatever we wrote in `index.html` will be displayed on the browser. Your webpage would look like this:
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
        migrations/
        admin.py
        apps.py
        models.py
        tests.py
        views.py
```