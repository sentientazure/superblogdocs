# About
It would be nice to know a little about who this author is. Let's create, in the author's profile page, a "Description", where they can write a little bit about themselves.

For that to be possible, we need to store in the database the "description" of every author. So we need to create a model for it. In your `models.py`:
```python
class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    description = models.TextField(null=True, blank=True)
```

The `user` field is a One To One field with the `User` model. This is because a user only has one profile and one "description". The `description` field is a `TextField`. We're making it optional so not every user has to have a description. We make it optional by setting `null=True`, and `blank=True`.

`null=True` allows a `Profile` object in the database to have an empty `description` field. `blank=True` sets the `description` field as not required for forms.

Migrate the new model.

In your `admin.py`:
```python
from .models import Article, Profile

admin.site.register(Profile)
```

Now let's create a way for the user to view their own profile, and edit their profile. In your `urls.py`:
```python
urlpatterns = [
    [...]
    path('profile/', views.profile, name="profile"),
    path('profile/edit/', views.profile_edit, name="profile-edit"),
    path('profiles/<str:username>/', views.author_profile, name="author-profile"),
]
```

In your `views.py`:
```python
from .forms import RegisterForm, LoginForm, ArticleForm, ProfileForm

def profile(request):
    user = request.user
    context = {
        'user': user
    }
    return render(request, "profile.html", context)

def profile_edit(request):
    profile = request.user.profile
    form = ProfileForm(instance=profile)
    if request.method == "POST":
        form = ProfileForm(request.POST, instance=profile)
        if form.is_valid():
            form.save()
            return redirect('profile')
    context = {
        'form': form,
    }
    return render(request, "profile_edit.html", context)
```

Also in your views, update the `register_view()` to be:
```python
from .models import Article, Profile

def register_view(request):
    [...]
        if form.is_valid():
            [...]

            user.set_password(user.password)
            user.save()

            Profile.objects.create(user=user)

            [...]

    [...]
```

So your `register_view(...)` becomes:
```python
def register_view(request):
    form = RegisterForm()
    if request.method == 'POST':
        form = RegisterForm(request.POST)
        if form.is_valid():
            user = form.save(commit=False)

            user.set_password(user.password)
            user.save()

            Profile.objects.create(user=user)

            login(request, user)
            return redirect("article-list")

    context = {
        "form": form,
    }
    return render(request, 'register.html', context)
```

This change is important because it'll automatically create a `Profile` object associated with the newly registered user. All your existing user accounts do not have a `Profile` object. You can create empty `Profile` objects in the admin site and associate them with `User` accounts.

In your `forms.py`:
```python
from .models import Article, Profile

class ProfileForm(forms.ModelForm):
    class Meta:
        model = Profile
        fields = ['description', ]
```

Create the two templates: `profile.html` and `profile_edit.html`. The way we're gonna style the `profile.html` template will be the same as the way we styled the `author_profile.html` template, with a button to `Edit` the profile:
```django
{% extends "base.html" %}

{% block title %}
Profile
{% endblock %}

{% block body %}
<div class="row">
    <div class="col-lg-12">
        <div class="card mb-4">
            <h2 class="card-header">{{ user.username }} <a href="{% url 'profile-edit' %}"><button class="btn btn-outline-success">Edit</button></a></h2>
            <div class="card-body">
                <p class="card-text">
                    First Name: {{ user.first_name }}
                </p>
                <p class="card-text">
                    Last Name: {{ user.last_name }}
                </p>
                <p class="card-text">
                    Email: {{ user.email }}
                </p>
                {% if user.profile.description %}
                    <p class="card-text">
                        Description: {{ user.profile.description }}
                    </p>
                {% endif %}
            </div>
        </div>
        {% if not articles %}
            You have not published any articles.
        {% else %}
            {% for article in articles %}
                <div class="card mb-4">
                    <div class="card-body">
                        <h2 class="card-title"><a href="{% url 'article-detail' article.slug %}">{{ article.title }}</a></h2>
                    </div>
                    <div class="card-footer text-muted">
                        Published on {{ article.published }}
                    </div>
                </div>
            {% endfor %}
        {% endif %}
    </div>
</div>
{% endblock %}
```

##### `{% if user.profile.description %}`
This page will display the user profile's `description` if he has written a description.

We also changed the message to be displayed if there's no published articles.

The `profile_edit.html` template styles will match the styles used for the draft edit page:
```django
{% extends "base.html" %}

{% block title %}
Profile Edit Page
{% endblock %}

{% block body %}
<div class="card my-4">
    <h5 class="card-header">Edit your description!</h5>
    <div class="card-body">
        <form action="{% url 'profile-edit' %}" method="POST">
            {% csrf_token %}
            <div class="form-group">
                <label for="id_description">Description</label>
                <textarea class="form-control" id="id_description" placeholder="Write here..." rows="5" name="description">{{ form.description.value }}</textarea>
            </div>
            <button type="submit" class="btn btn-primary">Save</button>
        </form>
    </div>
</div>
{% endblock %}
```

Now you can edit your profile and write a description. Now we need to display the description in other authors' profiles also, not just the logged in user's profile. This is done in the `author_profile.html` template. Edit the `card-body` div in your `author_profile.html` template to:
```django
<div class="card-body">
    [...]
    {% if user.profile.description %}
        <p class="card-text">
            Description: {{ user.profile.description }}
        </p>
    {% endif %}
</div>
```

So your `author_profile.html` should look like:
```django
{% extends "base.html" %}

{% block title %}
Profile
{% endblock %}

{% block body %}
<div class="row">
    <div class="col-lg-12">
        <div class="card mb-4">
            <h2 class="card-header">{{ user.username }} <a href="{% url 'profile-edit' %}"><button class="btn btn-outline-success">Edit</button></a></h2>
            <div class="card-body">
                <p class="card-text">
                    First Name: {{ user.first_name }}
                </p>
                <p class="card-text">
                    Last Name: {{ user.last_name }}
                </p>
                <p class="card-text">
                    Email: {{ user.email }}
                </p>
                {% if user.profile.description %}
                    <p class="card-text">
                        Description: {{ user.profile.description }}
                    </p>
                {% endif %}
            </div>
        </div>
        {% if not articles %}
            You have not published any articles.
        {% else %}
            {% for article in articles %}
                <div class="card mb-4">
                    <div class="card-body">
                        <h2 class="card-title"><a href="{% url 'article-detail' article.slug %}">{{ article.title }}</a></h2>
                    </div>
                    <div class="card-footer text-muted">
                        Published on {{ article.published }}
                    </div>
                </div>
            {% endfor %}
        {% endif %}
    </div>
</div>
{% endblock %}
```

Lastly, let's put a button in the navbar that takes you to the profile page. In your `base.html`, change the following:
```django
{% if request.user.is_authenticated %}
    <li class="nav-item">
        <a class="nav-link {{ draft_tab_status }}" href="{% url 'draft-list' %}">
            Drafts
        </a>
    </li>
    <li class="nav-item">
        <a class="nav-link" href="{% url 'article-create' %}">
            Write!
        </a>
    </li>
{% endif %}
```
to:
```django
{% if request.user.is_authenticated %}
    <li class="nav-item">
        <a class="nav-link {{ profile_tab_status }}" href="{% url 'profile' %}">
            Profile
        </a>
    </li>
    <li class="nav-item">
        <a class="nav-link {{ draft_tab_status }}" href="{% url 'draft-list' %}">
            Drafts
        </a>
    </li>
    <li class="nav-item">
        <a class="nav-link" href="{% url 'article-create' %}">
            Write!
        </a>
    </li>
{% endif %}
```

So your resulting `base.html` becomes:
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
                    {% if request.user.is_authenticated %}
                        <li class="nav-item">
                            <a class="nav-link {{ profile_tab_status }}" href="{% url 'profile' %}">
                                Profile
                            </a>
                        </li>
                        <li class="nav-item">
                            <a class="nav-link {{ draft_tab_status }}" href="{% url 'draft-list' %}">
                                Drafts
                            </a>
                        </li>
                        <li class="nav-item">
                            <a class="nav-link" href="{% url 'article-create' %}">
                                Write!
                            </a>
                        </li>
                    {% endif %}
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
    <div class="container">
        {% block body %}{% endblock %}
    </div>

    <!-- Bootstrap core JavaScript -->
    <script src="{% static 'vendor/jquery/jquery.min.js' %}"></script>
    <script src="{% static 'vendor/bootstrap/js/bootstrap.bundle.min.js' %}"></script>
</body>
</html>
```