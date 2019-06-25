# Login Error Messages
Currently, if the user attempts to login and accidentally mistypes their password, they won't see a helpful message that tells them what went wrong and how to fix it. Usually in the internet, if you login with the wrong password you get a message like "Wrong username/password combination," let's put something like that on our website!

Change your `login_view()` to:
```python
def login_view(request):
    [...]
    error_message = ""
    if request.method == 'POST':
        [...]
        if form.is_valid():
            [...]

        error_message = "Invalid username/password combination. Please try again."

    context = {
        [...]
        "error_message": error_message,
    }
    return render(request, 'login.html', context)
```
It should look like this:
```python
def login_view(request):
    form = LoginForm()
    error_message = ""
    if request.method == 'POST':
        form = LoginForm(request.POST)
        if form.is_valid():
            username = form.cleaned_data['username']
            password = form.cleaned_data['password']

            auth_user = authenticate(username=username, password=password)
            if auth_user is not None:
                login(request, auth_user)
                return redirect('article-list')
        
        error_message = "Invalid username/password combination. Please try again."

    context = {
        'form': form,
        "error_message": error_message,
    }
    return render(request, "login.html", context)
```

Be aware of indentations. In this code, if the form is *invalid* the variable `error_message` will contain the message "Invalid username/password combination. Please try again." Next, we display the message.

In your `login.html`, above the submit button:
```django
{% block body %}
<div class="card my-4">
    <h5 class="card-header">Login</h5>
    <div class="card-body">
        <form action="{% url 'login' %}" method="POST">
            {% csrf_token %}
            [...]
            <p><strong>{{ error_message }}</strong></p>
            <button type="submit" class="btn btn-primary">Submit</button>
        </form>
    </div>
</div>
{% endblock %}
```

Add that new line under `{{ form.as_p }}`. Now if you try to login using the wrong password or a username that doesn't exist, it will display that message below the input fields.