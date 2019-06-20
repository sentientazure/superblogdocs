# About
It would be nice to know a little about who this author is. Let's create, in the author's profile page, an "About", where they can write a little bit about themselves.

For that to be possible, we need to store in the database the "about" of every author. So we need to create a model for it. In your `models.py`:
```python
class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    about = models.TextField(null=True, blank=True)
```

The `user` field is a One To One field with the `User` model. This is because a user only has one profile and one "about". The `about` field is a `TextField`. We're making it optional so not every user has to have an about. We make it optional by setting `null=True`, and `blank=True`.

`null=True` allows a `Profile` object in the database to have an empty `about` field. `blank=True` sets the `about` field as not required for forms.

Migrate the new model.

In your `admin.py`:
```python
from .models import Profile

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
from .forms import ProfileForm

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
from .models import Profile

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
from .models import Profile

class ProfileForm(forms.ModelForm):
    class Meta:
        model = Profile
        fields = ['about', ]
```

Create the two templates: `profile.html` and `profile_edit.html`. In your `profile.html`:
```django
{% extends "base.html" %}

{% block title %}
Profile
{% endblock %}

{% block body %}
<a href="{% url 'article-list' %}"><button>Back</button></a>
<a href="{% url 'profile-edit' %}"><button>Edit</button></a>
<p>First Name: {{ user.first_name }}</p>
<p>Last Name: {{ user.last_name }}</p>
<p>Email: {{ user.email }}</p>
{% if user.profile.about %}
    <p>About Me: {{ user.profile.about }}</p>
{% endif %}
{% endblock %}
```

In your `profile_edit.html`:
```django
{% extends "base.html" %}

{% block title %}
Write!
{% endblock %}

{% block body %}
<form action="{% url 'profile-edit' %}" method="POST">
    {% csrf_token %}
    {{ form.as_p }}
    <input type="submit" class="btn btn-primary">
</form>
<a href="{% url 'profile' %}">
    <button>
        Cancel
    </button>
</a>
{% endblock %}
```

Lastly, let's put a button in the navbar that takes you to the profile page. In your `base.html`, change the following:
```django
{% if request.user.is_authenticated %}
    <a class="nav-item nav-link" href="{% url 'logout' %}">Logout</a>
    [...]
{% else %}
```
to:
```django
{% if request.user.is_authenticated %}
    <a class="nav-item nav-link" href="{% url 'logout' %}">Logout</a>
    <a class="nav-item nav-link" href="{% url 'profile' %}">Profile</a>
    [...]
{% else %}
```

So your resulting `base.html` becomes:
```django
<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}{% endblock %}</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
</head>
<body>
    <nav class="navbar navbar-expand-lg navbar-light bg-light">
        <a class="navbar-brand" href="{% url 'article-list' %}">Superblog</a>
        <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNavAltMarkup" aria-controls="navbarNavAltMarkup" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNavAltMarkup">
            <div class="navbar-nav">
                {% if request.user.is_authenticated %}
                    <a class="nav-item nav-link" href="{% url 'logout' %}">Logout</a>
                    <a class="nav-item nav-link" href="{% url 'profile' %}">Profile</a>
                    <a class="nav-item nav-link" href="{% url 'draft-list' %}">Drafts</a>
                    <a class="nav-item nav-link" href="{% url 'article-create' %}">Write!</a>
                {% else %}
                    <a class="nav-item nav-link" href="{% url 'register' %}">Register</a>
                    <a class="nav-item nav-link" href="{% url 'login' %}">Login</a>
                {% endif %}
            </div>
        </div>
    </nav>
    {% block body %}{% endblock %}
    <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
</body>
</html>
```