# Bootstrap
Let's start prettifying our little site. Bootstrap can help tremendously with that. It's fairly easy to setup Bootstrap. Bootstrap offers CDNs which you can find [here](https://ge tbootstrap.com/docs/4.3/getting-started/introduction/#quick-start).

CDN is short for Content Delivery Network. You can read more about CDNs [here](https://www.cloudflare.com/learning/cdn/what-is-a-cdn/).

Bootstrap has one CDN link for CSS and three CDN links for JS. Copy them into your base template. Your `base.html` should be like this:
```django
<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}{% endblock %}</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
</head>
<body>
    [...]
    {% block body %}{% endblock %}
    <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
</body>
</html>
```

Now, every template that extends the base template can use Bootstrap!