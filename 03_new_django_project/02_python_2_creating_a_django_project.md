Before we do anything with the project, we should first _activate_ the virtual environment that we just created:

```bash
$ source superblog_env/bin/activate
```

The second command is the one that _activates_ the virtual environment.

---

#### Windows

To activate the virtual environment in Windows, run the following command:

```bash
$ superblog_env\Scripts\activate
```

---

Once you do that, you should see the terminal prompt look like this:

```bash
(superblog_env)$
```

If you see in between parantheses `(` and `)` the name of the virtual environment you created, that means the virtual environment has been _activated_. What _activated_ means is that you're now working inside this virtual environment. Next we'll install Django. Since the virtual environment has been activated, the installation of Django will only be available from this virtual environment.

### Installing Django

```bash
(superblog_env)$ pip install django==2.2
```

The "`==2.2`" is how we specify the version of Django we're installing. Without specifying this, if we just say `pip install django`, it'll install the latest stable release of Django. But to make sure there's consistency, we're specifying Django verison 2.2.

### A brief intro to Django

This image illustrates Django's architecture known as [MVC](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller).

![Django architecture](https://i.imgur.com/KzO9oIA.png)

Part of Django's power is all the things that come straight out-of-the-box with it. It comes by default with an admin site that the administrator of the website can use to manage data, content, and users. This admin site is only accessible to the administrator of the website.

### Creating the Django project

The following command comes built-in with Django. It creates a new Django project for us, which we will call "superblog":

```bash
(superblog_env)$ django-admin startproject superblog
```

Now let's see what this Django project comes with. Inside the `superblog` folder, which is the Django project, you'll see the following file structure:

```
superblog/
    manage.py
    superblog/
        __init__.py
        settings.py
        urls.py
        wsgi.py
```

Let's quickly explain what each of these files means:

- `manage.py`: This file is used for many things, but primarily its used for running the project to see our website.
- `__init__.py`: This is an empty file that tells Python to treat this folder as a Python package.
- `settings.py`: This file contains the (you guessed it) settings of our project.
- `urls.py`: This file contains the URL paths that our website will handle. This'll make more sense when we get into it.
- `wsgi.py`: This is a file that allows your project to be deployed. Although we won't be getting into that this project, it's good to know for when you deploy your websites.

### Creating the Django app

Django allows us to separate parts of our website into Django _apps_. All of the functionality of the website is in these apps. An example to explain these Django apps and why Django has these apps is in an e-commerce site. An e-commerce site usually has accounts, the products, the cart, and payment. Each of those large components of the website could be put in its own app. So anything and everything related to handling accounts, registration, password resets, account deletion would be handled within a single Django app. Anythiing related to the products, categories of products, rating systems, search, recommended products, and so on could be handled within a single Django app dedicated to that part of the website.

Django apps are nothing magical though, they're just folders and files meant to keep your code organized. You could put the entire website, no matter how complex, into a single Django app. It would be a coder's nightmare, and fair reason for your fellow teammate coders to learn sorcery and curse you in your sleep for condemning them to such a fate. But technically it's possible.

So let's start by creating one now!

```bash
(superblog_env)$ cd superblog
(superblog_env)$ python manage.py startapp articles
```

Here we're executing the `manage.py` file, telling it we want to create an app (`startapp`) and calling the app `articles`.
Once this is done, you should see a folder called `articles` that's been created. Let's see what it contains:

```
superblog/
    manage.py
    superblog/
        __init__.py
        settings.py
        urls.py
        wsgi.py
    articles/
        __init__.py
        migrations/
        admin.py
        apps.py
        models.py
        tests.py
        views.py
```

Let's have a quick runthough of what each of these new files is:

- `admin.py`: In this file we can customize the admin site to our liking. ([read more](https://docs.djangoproject.com/en/2.2/ref/contrib/admin/))
- `apps.py`: This contains the configurations of this app. We won't touch this file at all.
- `migrations/`: This folder contains the migrations files. We won't need to touch this folder at all. ([read more](https://docs.djangoproject.com/en/2.2/topics/migrations/))
- `models.py`: This file contains the models of the application. ([read more](https://docs.djangoproject.com/en/2.2/topics/db/models/))
- `tests.py`: This file is for writing tests for this website. We won't touch this file at all.
- `views.py`: This is where we'll write our views. You'll know what views are later on. ([read more](https://docs.djangoproject.com/en/2.2/topics/http/views/))

First thing you must remember to do after creating a new Django app, is include it in the `INSTALLED_APPS` list in the `settings.py` file. Open the `settings.py` file in your favorite text editor or IDE. I'll be using [VS Code](https://code.visualstudio.com/download). I recommend [Sublime Text](https://www.sublimetext.com/3) if you're not familiar with text editors and IDEs in general. You'll see the following list in your `settings.py`:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

Include the app we just made at the bottom of the list:

```python
INSTALLED_APPS = [
    [...]
    'articles',
]
```

Your `INSTALLED_APPS` list should look like this:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'articles'
]
```

Django won't see your new app, or the code we'll write within it, if it isn't in the `INSTALLED_APPS` list in the settings file. That's how Django keeps track of the Django apps that this website uses.

### Running the website

The following command will run the Django server and then we'll be able to open it in the browser.

```bash
(superblog_env)$ python manage.py runserver
```

Here we're telling `manage.py` to run the server (`runserver`). You should see an output similar to the following:

```bash
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 17 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.

April 08, 2019 - 11:45:04
Django version 2.2, using settings 'superblog.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

If you see this, then the server is running and we can open it in the browser. Now, open your favorite web browser (we recommend Google Chrome because of their very useful developer tools), and go to the following URL:

```bash
http://127.0.0.1:8000/
```

Without going too much in-depth with how the internet works and the networking behind the scenes, the `127.0.0.1` always means your local machine, your computer or laptop. The number after the colon `:`, `8000`, that is the _port number_.

---

## Tangent: Programmer Humor

There's a lot of internet memes that are tech-related. For example, the [ProgrammerHumor](https://www.reddit.com/r/ProgrammerHumor/) subreddit is a community around that. You may have come across the following meme:
![No Place Like Home](https://i.imgur.com/XbG6gSA.jpg?1)
The joke here is that the IP address `127.0.0.1` always refers back to your computer, your home.
"There's no place like home"

---

Anyways... after running the server and opening the URL `127.0.0.1:8000` in the browser, you should see the following page:
![Website Running](https://i.imgur.com/UCHDKiO.png)
You get this webpage with Django the first time you create a new Django project and run the server.

Now that we've got the boilerplate out of the way, we can start building our website!
