Let's create a button in the article list page that appears if the user is logged in, and once clicked will log the user out. Let's start by adding the button, then work our way back to the view.

In your `list.html`, inside the `<body>` tag:
```django
...
<body>
    {% if request.user.is_authenticated %}
        <a href="{% url 'logout' %}">
            <button>
                Logout
            </button>
        </a>
    {% endif %}
    ...
</body>
```
Here we're seeing two new things, let's dissect them!

##### `{% if request.user.is_authenticated %}`
This is how you'd write an if-statement in Django's templates. `request` is always accessible in the template code, without needing to send it in the context. `request.user` always refers to the logged in user object. The `.is_authenticated` bit is a field in Django's built-in `User` model, it returns `True` if this user is logged in currently, and `False` otherwise.

##### `{% url 'logout' %}`
This tag will return the URL path for the URL name "logout". We will define this URL `path()` next.

##### `{% endif %}`
This line is how Django knows to end the if-statement. Just to reiterate, Python knows the end of an if-statement by reading the indentations in the beginning of the lines. However, HTML doesn't read whitespace like Python does, so the only way for Django to know where the if-statement ends is by having this `{% endif %}` tag.

Then, in your `urls.py`:
```python
urlpatterns = [
    ...
    path('logout/', views.logout_view, name="logout"),
]
```
In your `views.py`:
```python
from django.contrib.auth import logout

def logout_view(request):
    logout(request)
    return redirect("login")
```
Django comes built-in with a function to logout the user. We simply need to call the function, `logout(request)`, and it logs the user out of the session. Then we're `redirect`ing the user to the login page.([read more about the `logout(...)` function here](https://docs.djangoproject.com/en/2.2/topics/auth/default/#django.contrib.auth.logout))

Now if you login, you'll see both the login and logout buttons. Ideally, when you're logged in you only see the logout button, and when you're logged out you only see the login button. Let's make that happen!

In your `list.html`, replace the following:
```django
{% if request.user.is_authenticated %}
    <a href="{% url 'logout' %}"><button>
        Logout
    </button></a>
{% endif %}
<a href="{% url 'register' %}">
    <button>
        Register
    </button>
</a>
<a href="{% url 'login' %}">
    <button>
        Login
    </button>
</a>
```
with:
```django
{% if request.user.is_authenticated %}
    <a href="{% url 'logout' %}"><button>
        Logout
    </button></a>
{% else %}
    <a href="{% url 'register' %}">
        <button>
            Register
        </button>
    </a>
    <a href="{% url 'login' %}">
        <button>
            Login
        </button>
    </a>
{% endif %}
```
Here we have a full-fledged if-statement within our template. This is possible because of Django's built-in templating language.