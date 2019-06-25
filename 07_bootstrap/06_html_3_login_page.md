# Styling the Login Page!

Currently, your `login.html` body block looks like this:
```django
<form action="{% url 'login' %}" method="POST">
    {% csrf_token %}
    {{ form.as_p }}
    <p><strong>{{ error_message }}</strong></p>
    <input type="submit">
</form>
<a href="{% url 'article-list' %}">
    <button>
        Cancel
    </button>
</a>
```

We're gonna replace all of that. We'll use the same body block as the `register.html`. We'll remove the email, first name, and last name fields. Remove the `<small>` element for the username field. Change the header text and form url from register to login. Your end result should be like:
```django
{% block body %}
<div class="card my-4">
    <h5 class="card-header">Login</h5>
    <div class="card-body">
        <form action="{% url 'login' %}" method="POST">
            {% csrf_token %}
            <div class="form-group">
                <label for="id_username">Username</label>
                <input type="text" class="form-control" id="id_username" placeholder="Enter Username" name="username">
            </div>
            <div class="form-group">
                <label for="password">Password</label>
                <input type="password" class="form-control" id="id_password" placeholder="Enter Password" name="password">
            </div>
            <button type="submit" class="btn btn-primary">Submit</button>
        </form>
    </div>
</div>
{% endblock %}
```