# Login Page
In your `views.py`, replace:
```python
def login_view(request):
    return render(request, "login.html")
```
with:
```python
from .forms import RegisterForm, LoginForm

def login_view(request):
    form = LoginForm()
    context = {
        'form': form
    }
    return render(request, "login.html", context)
```
Don't forget to import the `LoginForm`. In your `login.html`:
```django
{% extends "base.html" %}

{% block title %}
Login Page
{% endblock %}

{% block body %}
<form action="{% url 'login' %}" method="POST">
    {% csrf_token %}
    {{ form.as_p }}
    <input type="submit">
</form>
{% endblock %}
```
Now, if you go to `127.0.0.1:8000/login`, you'll see the login form. If you fill it and submit it won't do anything. Just as we did with registration, we need to receive the form submission in the views.

In your `views.py`, change your `login_view()` to:
```python
from django.shortcuts import render, redirect
from django.contrib.auth import login, authenticate
from .forms import RegisterForm, LoginForm

def login_view(request):
    form = LoginForm()
    if request.method == 'POST':
        form = LoginForm(request.POST)
        if form.is_valid():
            username = form.cleaned_data['username']
            password = form.cleaned_data['password']

            auth_user = authenticate(username=username, password=password)
            if auth_user is not None:
                login(request, auth_user)
                return redirect('article-list')

    context = {
        'form': form
    }
    return render(request, "login.html", context)
```
Most of this code is familiar to you, from the registration view. But there's a bit of code there that's new to you. Let's dissect this code!

##### `username = form.cleaned_data['username']`
If you recall, this `LoginForm` we're using is *not* a `ModelForm`. It's a basic form. We can get the data the user entered in the form by using the dictionary `cleaned_data` that comes built-in to the form. This dictionary has keys that match the fields in the form that we defined. It has key `username`, its value is whatever the user entered as the username in the form. Same for password.

Here we're taking the username the user entered into the form and storing it in a variable called `username`.

##### `password = form.cleaned_data['password']`
Here we're taking the password the user entered into the form and storing it in a variable called `password`.

##### `auth_user = authenticate(username=username, password=password)`
This line checks the validity of the username/password combination the user entered. It does that by calling Django's built-in `authenticate()` function, and giving it the username and password the user typed. It returns `None` if the username/password combination is incorrect, and returns the `User` object if the combination is correct.

##### `if auth_user is not None:`
Here we're checking if the user enter a valid username/password combination. Because, again, the call to `authenticate()` returns *`None`* if the user entered an invalid username/password combination.

##### `login(request, auth_user)`
This line is familiar to you. It'll log the user in with the credentials they entered to the form.

##### `return redirect('article-list')`
This line will redirect the user to the article list page if they've successfully logged in.

Let's add a button in the base template to take us to the login page. In your `base.html`, under the "Register" button:
```django
<body>
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
    {% block body %}{% endblock %}
</body>
```

Now you can click on the login button and be taken to the login page.

Next we're gonna build a button the user can use to logout!