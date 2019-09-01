Let's start prettifying our little site. Bootstrap can help tremendously with that. Bootstrap is a front-end library that Twitter built to keep their front-end designs consistent across devices and screens. Later on they made it public, and everyone started using it. And everyone is still using it. There's now countless pre-designed bootstrap website templates available online. Many are free, many are not, some are very expensive, some are very advanced and expensive. Here we're gonna use a free standard one.

In the starter repository that you've cloned at the beginning of this project, there's already Bootstrap files and styles in `articles/static/`. We'll be using a Bootstrap template that's already made and available online. Here are the links: [Blog Home](https://startbootstrap.com/templates/blog-home/) and [Blog Post](https://startbootstrap.com/templates/blog-post/). They're the same, but we need the `index.html` files that come from each download because we'll be using both of them to construct our blogging platform.

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

Those are the links and scripts we need to put in our `base.html` template. These files are already stored in our static folder, we just need to serve them properly. To do that, we need to use Django's `static` template tag ([read more](https://docs.djangoproject.com/en/2.2/ref/templates/builtins/#static)). To be able to use this tag, we need to `load` it ([read more](https://docs.djangoproject.com/en/2.2/ref/templates/builtins/#load)).

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

Don't forget the `{% load static %}` tag at the very top!

Now, every template that extends the base template can use this Bootstrap template!
