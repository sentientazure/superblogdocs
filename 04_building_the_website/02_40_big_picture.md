When we ran the command `python manage.py runserver` we ran our Django project as a server that hosts a website. It's only accessible to us from our local machine, no one else can view this website.

When we opened the browser and typed `127.0.0.1:8000` in the URL bar and hit Enter, we made the browser make an *http request* to the server that hosts a website at the IP address and port `127.0.0.1:8000`.

The `path` we wrote in the `urls.py` file tells the server that if it receives a request at `127.0.0.1:8000/`, with nothing typed after the `/`, it will run the *view* function we called `index()`.

The *view* function is where the logic and processing happens. It is here that we decide which page we display. So in the definition of the `path` in the `urls.py` file, we linked the URL `127.0.0.1:8000/` with the view `index()`. Then, whatever the view `index()` returns will be displayed on the browser.

Right now, we're displaying whatever text we write inside the brackets of `HttpResponse(...)`. You could try changing that to some other text, save the file, and refresh the page. You'll see that the text on the page changes to be exactly what you wrote in the view function. ([read mode about the MVC model](https://blog.codinghorror.com/understanding-model-view-controller/))

### A step further
We can take this little program we have a step further and instead of displaying some boring text, we can display HTML code.

Let's change the `index()` view to the following:
```python
def index(request):
    return HttpResponse("<h1>This is a large header</h1>")
```
After you save the file, refresh the webpage on the browser and you should see:
![Header](https://i.imgur.com/kXAp4dA.png)