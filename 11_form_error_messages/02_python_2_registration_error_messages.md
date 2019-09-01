# Registration Error Messages

For the username field, replace the following in your `register.html`:

```django
<div class="form-group">
    <label for="id_username">Username</label>
    <input type="text" class="form-control" id="id_username" aria-describedby="usernameHelp" placeholder="Enter Username" name="username">
    <small id="usernameHelp" class="form-text text-muted">{{ form.username.help_text }}</small>
</div>
```

with:

```django
<div class="form-group">
    <label for="id_username">Username</label>
    {% if form.username.errors %}
        {% for error in form.username.errors %}
            <br><strong>{{ error|escape }}</strong>
        {% endfor %}
    {% endif %}
    <input type="text" class="form-control" id="id_username" aria-describedby="usernameHelp" placeholder="Enter Username" name="username">
    <small id="usernameHelp" class="form-text text-muted">{{ form.username.help_text }}</small>
</div>
```

We just added this:

```django
{% if form.username.errors %}
    {% for error in form.username.errors %}
        <br><strong>{{ error|escape }}</strong>
    {% endfor %}
{% endif %}
```

What this does is it checks if the username field in the form has any errors. If so, it displays them in bold text. Let's do the same for the password and email fields!

Your resulting `register.html` body block should look like this:

```django
{% block body %}
<div class="card my-4">
    <h5 class="card-header">Register</h5>
    <div class="card-body">
        <form action="{% url 'register' %}" method="POST">
            {% csrf_token %}
            <div class="form-group">
                <label for="id_username">Username</label>
                {% if form.username.errors %}
                    {% for error in form.username.errors %}
                        <br><strong>{{ error|escape }}</strong>
                    {% endfor %}
                {% endif %}
                <input type="text" class="form-control" id="id_username" aria-describedby="usernameHelp" placeholder="Enter Username" name="username">
                <small id="usernameHelp" class="form-text text-muted">{{ form.username.help_text }}</small>
            </div>
            <div class="form-group">
                <label for="password">Password</label>
                {% if form.password.errors %}
                    {% for error in form.password.errors %}
                        <br><strong>{{ error|escape }}</strong>
                    {% endfor %}
                {% endif %}
                <input type="password" class="form-control" id="id_password" placeholder="Enter Password" name="password">
            </div>
            <div class="form-group">
                <label for="id_email">Email Address</label>
                {% if form.email.errors %}
                    {% for error in form.email.errors %}
                        <br><strong>{{ error|escape }}</strong>
                    {% endfor %}
                {% endif %}
                <input type="email" class="form-control" id="id_email" placeholder="Enter Email" name="email">
            </div>
            <div class="form-group">
                <label for="id_first_name">First Name</label>
                <input type="first_name" class="form-control" id="id_first_name" placeholder="Enter First Name" name="first_name">
            </div>
            <div class="form-group">
                <label for="id_last_name">Last Name</label>
                <input type="last_name" class="form-control" id="id_last_name" placeholder="Enter Last Name" name="last_name">
            </div>
            <button type="submit" class="btn btn-primary">Submit</button>
        </form>
    </div>
</div>
{% endblock %}
```
