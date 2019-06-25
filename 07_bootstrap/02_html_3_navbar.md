# Navbar
Let's put all those links in the base template, like for login, logout, register, and any other links we'll add in the future, in a nice-looking [navbar](https://getbootstrap.com/docs/4.3/components/navbar/).

If you open `blog_home.html`, you'll find the following navbar:
```html
  <!-- Navigation -->
  <nav class="navbar navbar-expand-lg navbar-dark bg-dark fixed-top">
    <div class="container">
      <a class="navbar-brand" href="#">Start Bootstrap</a>
      <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarResponsive" aria-controls="navbarResponsive" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarResponsive">
        <ul class="navbar-nav ml-auto">
          <li class="nav-item active">
            <a class="nav-link" href="#">Home
              <span class="sr-only">(current)</span>
            </a>
          </li>
          <li class="nav-item">
            <a class="nav-link" href="#">About</a>
          </li>
          <li class="nav-item">
            <a class="nav-link" href="#">Services</a>
          </li>
          <li class="nav-item">
            <a class="nav-link" href="#">Contact</a>
          </li>
        </ul>
      </div>
    </div>
  </nav>
```

We need to edit this code block to match our website's needs for a navbar. For example, instead of the links saying "Home", "About", and "Services" we want links for "Register", "Login", and "Logout". And instead of "Start Bootstrap", we want it to say "Superblog".

One last thing, you see in the example snippet above the nav link for Home has the class `active` in it. What the `active` class on a nav link does is it highlights that nav link, indicating to the user which page they're on. We want the nav link to be *`active`* if we click that link. The way we're gonna do that is by having a status variable in the context that will add that class to the appropriate nav link depending on which page we go to. You'll see how that works as we go on in this section.

At this point, your navbar should be like this:
```django
<!-- Navigation -->
<nav class="navbar navbar-expand-lg navbar-dark bg-dark fixed-top">
    <div class="container">
        <a class="navbar-brand" href="#">Start Bootstrap</a>
        <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarResponsive" aria-controls="navbarResponsive" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarResponsive">
            <ul class="navbar-nav ml-auto">
                {% if request.user.is_authenticated %}
                    <li class="nav-item">
                        <a class="nav-link {{ home_tab_status }}" href="{% url 'article-list' %}">Home</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link" href="{% url 'logout' %}">Logout</a>
                    </li>
                {% else %}
                    <li class="nav-item">
                        <a class="nav-link {{ login_tab_status }}" href="{% url 'login' %}">Login</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link {{ register_tab_status }}" href="{% url 'register' %}">Register</a>
                    </li>
                {% endif %}
            </ul>
        </div>
    </div>
</nav>
```

One last change I'd like to make: Do you see the class "`ml-auto`" to the `<ul>` tag? That class puts the `<li>` links to the right side of the navbar. The "`ml`" means "`margin-left`". Take a look at the website now, you'll see the links are on the right. I'd like to put some links to the left side and keep the others on the right side. So I copied the `<ul>` and gave this new copy the class "`mr-auto`" instead of "`ml-auto`", which means "`margin-right`". So I split that `<ul>` into:
```django
<ul class="navbar-nav mr-auto">
    <li class="nav-item">
        <a class="nav-link {{ home_tab_status }}" href="{% url 'article-list' %}">
            Home
        </a>
    </li>
</ul>
<ul class="navbar-nav ml-auto">
    {% if request.user.is_authenticated %}
        <li class="nav-item">
            <a class="nav-link" href="{% url 'logout' %}">
                Logout
            </a>
        </li>
    {% else %}
        <li class="nav-item">
            <a class="nav-link {{ login_tab_status }}" href="{% url 'login' %}">
                Login
            </a>
        </li>
        <li class="nav-item">
            <a class="nav-link {{ register_tab_status }}" href="{% url 'register' %}">
                Register
            </a>
        </li>
    {% endif %}
</ul>
```

After making those changes and putting the navbar code in the base template, your `base.html` should look like this:
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
    <!-- Navigation -->
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark fixed-top">
        <div class="container">
            <a class="navbar-brand" href="#">Start Bootstrap</a>
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarResponsive" aria-controls="navbarResponsive" aria-expanded="false" aria-label="Toggle navigation">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarResponsive">
                <ul class="navbar-nav mr-auto">
                    <li class="nav-item">
                        <a class="nav-link {{ home_tab_status }}" href="{% url 'article-list' %}">
                            Home
                        </a>
                    </li>
                </ul>
                <ul class="navbar-nav ml-auto">
                    {% if request.user.is_authenticated %}
                        <li class="nav-item">
                            <a class="nav-link" href="{% url 'logout' %}">
                                Logout
                            </a>
                        </li>
                    {% else %}
                        <li class="nav-item">
                            <a class="nav-link {{ login_tab_status }}" href="{% url 'login' %}">
                                Login
                            </a>
                        </li>
                        <li class="nav-item">
                            <a class="nav-link {{ register_tab_status }}" href="{% url 'register' %}">
                                Register
                            </a>
                        </li>
                    {% endif %}
                </ul>
            </div>
        </div>
    </nav>
    {% block body %}{% endblock %}

    <!-- Bootstrap core JavaScript -->
    <script src="{% static 'vendor/jquery/jquery.min.js' %}"></script>
    <script src="{% static 'vendor/bootstrap/js/bootstrap.bundle.min.js' %}"></script>
</body>
</html>
```

You can see the nav links have context variables in their classes. For example, for the Home link we put `{{ home_tab_status }}`, for the Login link we have `{{ login_tab_status }}`. To do the other half of this is in the views. Add the following to the contexts of your views:
```python
def article_list(request):
    articles_list = Article.objects.filter(draft=False)
    context = {
        "articles": articles_list,
        "home_tab_status": "active"
    }
    return render(request, "list.html", context)


def login_view(request):
    [...]

    context = {
        'form': form,
        "login_tab_status": "active",
        "error_message": error_message,
    }
    return render(request, 'login.html', context)


def register_view(request):
    [...]

    context = {
        'form': form,
        'register_tab_status': "active"
    }
    return render(request, 'register.html', context)
```

The way this works is that if the user goes to the login page, the context will have "`login_tab_status`" with value "`active`". This way that nav link for the Login will have the class "`active`" while the other nav links will not.
