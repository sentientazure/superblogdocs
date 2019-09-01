Currenlty, this is your `detail.html`:

```django
<!DOCTYPE html>
<html>
<head>
    <title>Detail Page</title>
</head>
<body>
    <p>{{ article.author }}</p>
    <p>{{ article.title }}</p>
    <p>{{ article.body }}</p>
</body>
</html>
```

Let's extract out the unique material from this code. This is it:

```django
Detail Page

<p>{{ article.author }}</p>
<p>{{ article.title }}</p>
<p>{{ article.body }}</p>
```

Next we put those two parts into the appropriate blocks, and extend `base.html`, we get:

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

And there you have it! Better structured template code for your website!
