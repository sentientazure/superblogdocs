# Base File
At this point, we have two HTML templates. One for the list page, another for the detail page. As our project grows larger and more complex, consistency across the pages becomes more and more important. Django gives us a brilliant solution to this issue of consistency. It's a *base* template file where other template files inherit from it.

Let's have our two template files inherit from a base file!

Create a template called "`base.html`" in your `templates/` folder. In this file, we'll have all the HTML code that is exactly the same in all other HTML files. This way we only have to write it once. So in your `base.html`:
```django
<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}{% endblock %}</title>
</head>
<body>
    {% block body %}{% endblock %}
</body>
</html>
```

Next, change your `list.html` from:
```django
<!DOCTYPE html>
<html>
<head>
    <title>List Page</title>
</head>
<body>
    <ul>
        {% for article in articles %}
            <li>
                <a href="{% url 'article-detail' article.id %}">
                    {{ article.title }}
                </a>
            </li>
        {% endfor %}
    </ul>
</body>
</html>
```
to:
```django
{% extends "base.html" %}

{% block title %}
List Page
{% endblock %}

{% block body %}
<ul>
    {% for article in articles %}
        <li>
            <a href="{% url 'article-detail' article.id %}">
                {{ article.title }}
            </a>
        </li>
    {% endfor %}
</ul>
{% endblock %}
```

Now let's do the same for the detail template. Change your `detail.html` to:
```django
{% extends "base.html" %}

{% block title %}
Detail Page
{% endblock %}

{% block body %}
<p>{{ article.author }}</p>
<p>{{ article.title }}</p>
<p>{{ article.body }}</p>
{% endblock %}
```