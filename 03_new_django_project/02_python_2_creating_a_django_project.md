# Creating a Django Project
### Activating the virtual environment
Before we do anything with the project, we should first *activate* the virtual environment that we just created:
```bash
$ source superblog_env/bin/activate
```
The second command is the one that *activates* the virtual environment.

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
If you see in between parantheses `(` and `)` the name of the virtual environment you created, that means the virtual environment has been *activated*. What *activated* means is that you're now working inside this virtual environment. Next we'll install Django. Since the virtual environment has been activated, the installation of Django will only be available from this virtual environment.
### Installing Django
```bash
(superblog_env)$ pip install django==2.2
```
### Creating the Django project
The following commands comes with Django, and it created a Django project for us, which we will call "superblog":
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
- `wsgi.py`: This is a file that allows your project to be deployed.
### Creating the Django app
Django allows us to separate parts of our website into Django *apps*. All of the functionality of the website is in these apps. So let's start by creating one now!
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
        templates/
        migrations/
        admin.py
        apps.py
        models.py
        tests.py
        views.py
```
Let's have a quick runthough of what each of these new files is:
- `admin.py`: Django comes with an out-of-the-box ready website for the administrator. In this file, we can customize that website to our liking. You'll see what we can do with it later.
- `apps.py`: This contains the configurations of this app.
- `migrations/`: This folder contains the migrations files.
- `models.py`: This file contains the models of the application.
- `tests.py`: This file is for writing tests for this website.
- `views.py`: This is where we'll write our views. You'll know what views are later on.

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
    'articles'
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
If you see this, then the server is running and we can open it in the browser. Now, open your favorite web browser (we recommend Google Chrome or Safari because of their very useful developer tools), and go to the following URL:
```bash
http://127.0.0.1:8000/
```
Without going too much in-depth with how the internet works and the networking behind the scenes, the `127.0.0.1` always means your local machine, your computer or laptop. The number after the colon `:`, `8000`, that is the *port number*.

----
## Tangent: Programmer Humor
There's a lot of internet memes that are tech-related. For example, the [ProgrammerHumor](https://www.reddit.com/r/ProgrammerHumor/) subreddit is a community around that. You may have come across the following meme:
![No Place Like Home](https://i.imgur.com/XbG6gSA.jpg?1)
The joke here is that the IP address `127.0.0.1` always refers back to your computer, your home.
"There's no place like home"

----
Anyways... after running the server and opening the URL `127.0.0.1:8000` in the browser, you should see the following page:
![Website Running](https://i.imgur.com/UCHDKiO.png)
You get this webpage with Django the first time you create a new Django project and run the server.

Now that we've got the boilerplate out of the way, we can start building our website!