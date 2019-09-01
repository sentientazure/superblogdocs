# Bootstrap

Let's start prettifying our little site. Bootstrap can help tremendously with that. In the starter repository that you've cloned at the beginning of this project, there's already Bootstrap files and styles in `articles/static/`. We'll be using a Bootstrap template that's already made and available online. Here are the links: [Blog Home](https://startbootstrap.com/templates/blog-home/) and [Blog Post](https://startbootstrap.com/templates/blog-post/). They're the same, but we need the `index.html` files that come from each download because we'll be using both of them to construct our blogging platform.

If you open `blog_home.html` (which is the `index.html` you get when you download the Blog Home template), you'll find the following:

```django
<head>

  [...]

  <!-- Bootstrap core CSS -->
  <link href="vendor/bootstrap/css/bootstrap.min.css" rel="stylesheet">

  <!-- Custom styles for this template -->
  <link href="css/blog-home.css" rel="stylesheet">

</head>

<body>

  [...]

  <!-- Bootstrap core JavaScript -->
  <script src="vendor/jquery/jquery.min.js"></script>
  <script src="vendor/bootstrap/js/bootstrap.bundle.min.js"></script>

</body>

</html>
```

Those are the links and scripts we need to put in our `base.html` template. However, we will change the `src`s and `href`s to use Django's `static` template tag.

Your `base.html` should end up like this:

```django
{% load static %}

<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}{% endblock %}</title>

    <!-- Bootstrap core CSS -->
    <link href="{% static 'vendor/bootstrap/css/bootstrap.min.css' %}" rel="stylesheet">

    <!-- Custom styles for this template -->
    <link href="{% static 'css/blog-home.css' %}" rel="stylesheet">

</head>
<body>
    [...]
    {% block body %}{% endblock %}

    <!-- Bootstrap core JavaScript -->
    <script src="{% static 'vendor/jquery/jquery.min.js' %}"></script>
    <script src="{% static 'vendor/bootstrap/js/bootstrap.bundle.min.js' %}"></script>
</body>
</html>
```

Don't forget the `{% load static %}` tag at the very top! This tag allows us to use the `{% static ... %}` template tag in Django.

Now, every template that extends the base template can use this Bootstrap template!
