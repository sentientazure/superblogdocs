Now open the project folder in your favorite text editor or IDE. I'll be using [VS Code](https://code.visualstudio.com/download). I recommend [Sublime Text](https://www.sublimetext.com/3) if you're not familiar with text editors and IDEs in general.

### `urls.py`
You should see the following already there by default:
```python
urlpatterns = [
    path('admin/', admin.site.urls),
]
```
Add the following to it:
```python
from articles import views
urlpatterns = [
    ...
    path('', views.index),
]
```
The first line imports the entire `views` package from the `articles` app. The line with `path` that we added means that if we go to the URL `127.0.0.1:8000`, it will *execute the view function called **index***. Next we will create the view.

### `views.py`
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
Now if you go to `127.0.0.1:8000` you should see the following page:
![Hello World!](https://i.imgur.com/qFh3OTR.png)
We just built a page by ourselves and now we're viewing it as a website in the browser. How did that happen exactly?