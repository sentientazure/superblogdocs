In the `register_view()` view in your `views.py`:

```python
from .forms import RegisterForm

def register_view(request):
    form = RegisterForm()
    context = {
        'form': form
    }
    return render(request, "register.html", context)
```

Here we're importing the `RegisterForm` class, creating an object out of it, and sending it to the `register.html` template.

Next, in your `register.html` template:

```django
{% extends "base.html" %}

{% block title %}
Register
{% endblock %}

{% block body %}
{{ form.as_p }}
{% endblock %}
```

Now you can go to `127.0.0.1:8000/register` and you should see the following form appear:
![Register Page 1](https://i.imgur.com/93WYAkh.png)

Let's dissect this code!

##### `form = RegisterForm()`

Here we're making an object out of our form class. Then we're putting it in the context and sending it to the template.

##### `{{ form.as_p }}`

Here we're displaying the form in its entirety. The `.as_p` tells Django to display it as `<p>` tags. If you remove the `.as_p` it'll still display the form, but it'll look a bit ugly and messy.

The `form` in this line comes from the _key_ in the context.

If you try to fill it and hit Enter, it won't do anything. Now, in your `register.html`, replace:

```django
{{ form.as_p }}
```

with:

```django
<form action="{% url 'register' %}" method="POST">
    {% csrf_token %}
    {{ form.as_p }}
    <input type="submit">
</form>
```

What makes this a form that actually submits is the HTML `<form>` tag and the `<input type="submit">` which actually submits the form.

##### `{% csrf_token %}`

This bit is a security measure built-in to Django. Basically it prevents hackers from using bots to hack your website through your forms. [(Read more about CSRF tokens here.)](https://docs.djangoproject.com/en/2.2/ref/csrf/)

##### `method="POST"`

When the user goes to the URL by typing something in the URL bar in the browser and then hitting Enter, that'll make an HTTP request to our server, as explained previously. That request is of type GET. Every HTTP request has a type. This bit of code sets the HTTP request of the form to a POST request. This way, when the user fills the form and submits it, the HTTP request that the submission of the form will make is of type POST. This is because the user is POST-ing the registration data.

Your `register.html` should look like this:

```django
{% extends "base.html" %}

{% block title %}
Register Page
{% endblock %}

{% block body %}
<form action="{% url 'register' %}" method="POST">
    {% csrf_token %}
    {{ form.as_p }}
    <input type="submit">
</form>
{% endblock %}
```

Now let's receive this form submission in the view. Change your `register_view()` view in `views.py` to:

```python
from django.shortcuts import render, redirect
from django.contrib.auth import login
from .forms import RegisterForm

def register_view(request):
    form = RegisterForm()
    if request.method == 'POST':
        form = RegisterForm(request.POST)
        if form.is_valid():
            user = form.save(commit=False)

            user.set_password(user.password)
            user.save()

            login(request, user)
            return redirect("article-list")

    context = {
        "form": form,
    }
    return render(request, 'register.html', context)
```

Let's dissect this code...

##### `if request.method == 'POST':`

In the view code, we can find out what type of request we're receiving by checking the variable `request.method`. Here we're checking if it's a POST request. If it's a POST request, that means that the user has submitted the form, if it's a GET request, that means the user is just going to the registration page.

The reason we're checking is because the behavior of our view will be different depending on what the user is intending to do. When the user is intending to submit the registration form, we want to take the form data and create a `User` out of it. And that's what we're doing next:

##### `form = RegisterForm(request.POST)`

This line makes an object out of our registration form, just like we already did previously. But this time we're populating the form with `request.POST`. When the user fills out a form and submits it, and the form makes a POST request like ours does, we receive what the user entered in the form with `request.POST`.

##### `if form.is_valid():`

Django's forms come built-in with this `.is_valid()` method. It checks if the user entered valid data or not. It returns `True` if the user entered valid data, and returns `False` otherwise.

Valid means... if there's an email field in the form and the user entered something that's _not_ an email in the email field, then this is invalid and the call to `.is_valid()` will return False. This also applies if you have a phone number field for example and you've specified a valid format for a phone number, which may include a specific length also.

##### `user = form.save(commit=False)`

Calling `form.save()` will create a `User` object (because this is a `ModelForm` associated with the `User` model) with the username, password, email, first name, and last name that the user entered in the form, and save it directly to the database.

But calling it with commit set to false (i.e. `form.save(commit=False)`) will create the `User` object, but won't save it in the databse. It will also _return_ that `User` object. And here we're storing it in a variable called `user`.

This is important because of how Django handles user passwords. You see, Django doesn't store user passwords. Even the administrator of the website cannot see user passwords. Instead, Django encrypts the password text that the user enters when they register, and that encryption produces a _hash_, which is simply a giant string of random characters. Django stores that hash in the database. Then when the user logs in and enters their password text, Django encrypts that password they entered when attempting to login and compares the resulting hash with the hash previously stored in the database. This way the password is secure, and no one can login to an account without knowing the password.

So before storing the `User` object in the database, we want to change the object's `password` field from the text that the user typed in the form to the hash that Django generates.

If that didn't make sense to you, you don't need to fully understand how that works. You just need to know how to use the Django framework properly. ([read more about `.save(commit=False)`](https://docs.djangoproject.com/en/2.2/topics/forms/modelforms/#the-save-method))

##### `user.set_password(user.password)`

In this line we're setting the password for this user to be what the user typed in the form's password field. This method call, `.set_password(...)`, will encrypt the password that the user typed in the form.([read more about the `.set_password(...)` function here](https://docs.djangoproject.com/en/2.2/ref/contrib/auth/#django.contrib.auth.models.User.set_password))

##### `user.save()`

This line actually saves the user object in the database. It is particularly _this line_ that registers the account in the database.

##### `login(request, user)`

When the user registers, you don't want to send them to the login page just to enter their information again. It's better to just log them in once they register. This line logs the user in. It's a function that's built-in to Django. It saves the user’s ID in the session, using Django’s session framework. ([read more about Django's `login()` function here](https://docs.djangoproject.com/en/2.2/topics/auth/default/#django.contrib.auth.login))

##### `return redirect("article-list")`

Once the user submits the form to register, we want the user to be taken to the article list page. This line does that. It `redirect`s the user to another page by giving it the URL name. ([read more about the `redirect(...)` function here](https://docs.djangoproject.com/en/2.2/topics/http/shortcuts/#redirect))

Now you can try it out. Go to `127.0.0.1:8000/register` and fill out the registration form. Once you do, you should be taken to the list page.

After, if you go to the admin site, you should be in the login page with the following red box appearing:
![Admin Login After Registration](https://i.imgur.com/vMW9EZl.png)

I registered on my website with the username "SentientMshary". Whatever username you entered in the registration form should appear there.

This box says you're "not authorized to access this page." That's because this is the admin page, and only admin user can access it. The registration page we just created creates normal user accounts with limited access.

Let's add a button in the base template that takes us to the registration page, so we can easily navigate the website. This button, because it's in the base template, will appear in all our pages. In your `base.html`:

```django
<body>
    <a href="{% url 'register' %}">
        <button>
            Register
        </button>
    </a>
    {% block body %}{% endblock %}
</body>
```

Now you can see a button that says "Register", clicking on it will take you to the registration page.
