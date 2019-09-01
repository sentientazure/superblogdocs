Currently, this is your `list.html`:

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

Take a moment and think about which parts of this code exists already in the base template?

Well, that's the code that we're gonna remove from this list template. We're gonna keep the code that's specific to this list page, and remove everything else that's the same across the different pages on our website. So once we do that, the code will look like this:

```django
List Page

<ul>
    {% for article in articles %}
        <li>
            <a href="{% url 'article-detail' article.id %}">
                {{ article.title }}
            </a>
        </li>
    {% endfor %}
</ul>
```

Next, we need to facilitate the inheritance and put the code in the right place of the base template. So our list template will be:

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

What this'll do is it'll take whatever is written inside the `{% block title %}{% endblock %}` tags in the list template and place it in the base template in place of the `{% block title %}{% endblock %}` tags.

It'll then do the same for the `{% block body %}` tag. So the end result after the inheritance is that it'll automatically build the list template to include the code in the base template to be the same as it was before:

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

The main benefit of this, while it ended with the same resulting template/webpage, is that the code is more organized and cleaner and less repetitive and redundant.
