For the registration, no changes necessary in the views.

In your `register.html`, replace the following line:
```django
{{ form.as_p }}
```
with:
```django
{% for field in form %}
    <div class="fieldWrapper">
        {{ field.label_tag }} {{ field }}
        {% if field.help_text %}
            <span class="help">{{ field.help_text|safe }}</span>
        {% endif %}
        {% if field.errors %}
            {% for error in field.errors %}
                <br><strong>{{ error|escape }}</strong><br>
            {% endfor %}
        {% endif %}
    </div>
{% endfor %}
```

What we're doing here is unwrapping the `{{ form.as_p }}`, bit to an extent, so that we can manually display things how we like to. This is necessary here because we want to display the errors of each field underneath the input field directly. But this is useful in general because it gives us more power in how our form behaves.

Most of this code was copied directly from Django's official documentation about this topic. [Click here](https://docs.djangoproject.com/en/2.2/topics/forms/#looping-over-the-form-s-fields) to see the original code from the documentation.

Let's dissect this new code...

##### `{% for field in form %}`
`form` as we receive it from the context, an instance of Django's `Form` class, contains a list of the fields within it. Here we're looping through that list so we can display all of them.

##### `{{ field.label_tag }}`
This is the text that appears to the left side of the input field. For the username field it says "Username", for the email field it says "Email address," and so on.

##### `{{ field }}`
This is the input field itself.

##### `{% if field.help_text %}`
You know in the registration form, next to the username input field you see the following message "Required. 150 characters or fewer. Letters, digits and @/./+/-/_ only."? That's the "help text" that is referred to in this line. This if-statement is checking if a help text exists for this particular field.

##### `<span class="help">{{ field.help_text|safe }}</span>`
This line displays the help text. The `|safe` bit just tells Django not filter out text that may appear as HTML code. The `|safe` in general, indepedent of this form, is used for security reasons. [Click here](https://docs.djangoproject.com/en/2.2/ref/templates/builtins/#safe) to read more about the `|safe`.

##### `{% if field.errors %}`
If this field in the form contains any errors, this condition will be true.

##### `{% for error in field.errors %}`
The errors in a field would be stored as a list. Here we're looping through that list to display them all. In this particular form, there's probably not gonna be more than one error. But even that one error would be stored in a list. This is useful to know in general.

##### `<br><strong>{{ error|escape }}</strong><br>`
This displays the error message itself in **bold**. [Click here](https://docs.djangoproject.com/en/2.2/ref/templates/builtins/#escape) to read more about the `|escape`.

Now if you try to register with a username that already has an account, for example, you'll see the appropriate error messages.