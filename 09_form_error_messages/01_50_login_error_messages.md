Currently, if the user attempts to login and accidentally mistypes their password, they won't see a helpful message that tells them what went wrong and how to fix it. Usually in the internet, if you login with the wrong password you get a message like "Wrong username/password combination," let's put something like that on our website!

In your `login_view()` in `views.py`:
```python
def login_view(request):
    ...
    error_message = ""
    if request.method == 'POST':
        ...
        if form.is_valid():
            ...

        error_message = "Invalid username/password combination. Please try again."

    context = {
        ...
        "error_message": error_message,
    }
    return render(request, 'login.html', context)
```

Be aware of indentations. In this code, if the form is *invalid* the variable `error_message` will contain the message "Invalid username/password combination. Please try again." Next, we display the message.

In your `login.html`:
```django
...
{{ form.as_p }}
<p><strong>{{ error_message }}</strong></p>
...
```

Add that new line under `{{ form.as_p }}`. Now if you try to login using the wrong password or a username that doesn't exist, it will display that message below the input fields.