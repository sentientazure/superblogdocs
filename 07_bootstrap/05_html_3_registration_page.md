# Styling the Registration Page!

For styling the registration page, take a look at [Bootstrap's form example](https://getbootstrap.com/docs/4.3/components/forms/#overview). We'll be editing and using that in this section.

For example, let's take this code:

```django
<div class="form-group">
    <label for="exampleInputEmail1">Email address</label>
    <input type="email" class="form-control" id="exampleInputEmail1" aria-describedby="emailHelp" placeholder="Enter email">
    <small id="emailHelp" class="form-text text-muted">We'll never share your email with anyone else.</small>
</div>
```

This displays a nice-looking email input field. We wanna reuse that for the registration form, but edit it to fit our needs. For example, we'll redo the attribute values and remove the `<small>` element. Our email input field will be:

```django
<div class="form-group">
    <label for="id_email">Email Address</label>
    <input type="email" class="form-control" id="id_email" placeholder="Enter Email" name="email">
</div>
```

We'll use the `<small>` element for the help text for the username field.

Repeating the same editing process for the other fields, our form becomes:

```django
<form action="{% url 'register' %}" method="POST">
    {%  csrf_token %}
    <div class="form-group">
        <label for="id_username">Username</label>
        <input type="text" class="form-control" id="id_username" aria-describedby="usernameHelp" placeholder="Enter Username" name="username">
        <small id="usernameHelp" class="form-text text-muted">{{ form.username.help_text }}</small>
    </div>
    <div class="form-group">
        <label for="password">Password</label>
        <input type="password" class="form-control" id="id_password" placeholder="Enter Password" name="password">
    </div>
    <div class="form-group">
        <label for="id_email">Email Address</label>
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
```

##### `<small id="usernameHelp" class="form-text text-muted">{{ form.username.help_text }}</small>`

This line displays the help text for the username field of the form. The help text itself is written by the Django developers as part of the built-in `User` model. We're able to use it here because our registration form is a `ModelForm` associated with the `User` model. We're putting it in the `<small>` tag because it'll look appopriate when you see it in the page as small faded-colored text.

Open up the `blog_post.html` template, scroll down to "`<!-- Comments Form -->`". We're gonna edit and use that form for the registration page. The comments form looks like this from the `blog_post.html` template:

```django
<div class="card my-4">
    <h5 class="card-header">Leave a Comment:</h5>
    <div class="card-body">
        <form>
            <div class="form-group">
                <textarea class="form-control" rows="3"></textarea>
            </div>
            <button type="submit" class="btn btn-primary">Submit</button>
        </form>
    </div>
</div>
```

Copy that, remove the text area and change the opening `<form>` tag to fit our needs. Replace the old body block with this new body block. Your resulting body block in your `register.html` should be like:

```django
{% block body %}
<div class="card my-4">
    <h5 class="card-header">Register</h5>
    <div class="card-body">
        <form action="{% url 'register' %}" method="POST">
            {% csrf_token %}
            <div class="form-group">
                <label for="id_username">Username</label>
                <input type="text" class="form-control" id="id_username" aria-describedby="usernameHelp" placeholder="Enter Username" name="username">
                <small id="usernameHelp" class="form-text text-muted">{{ form.username.help_text }}</small>
            </div>
            <div class="form-group">
                <label for="password">Password</label>
                <input type="password" class="form-control" id="id_password" placeholder="Enter Password" name="password">
            </div>
            <div class="form-group">
                <label for="id_email">Email Address</label>
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
