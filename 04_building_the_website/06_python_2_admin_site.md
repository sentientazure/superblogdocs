# Admin Site
In your `admin.py` file, you should see the following:
```python
from django.contrib import admin

# Register your models here.
```
Replace everything with the following:
```python
from django.contrib import admin
from .models import Article

admin.site.register(Article)
```
On the second line we're importing the Article class we defined in our `models.py` file. Notice the `.` before `models` in that line. It means the `models` module in the same directory as this `admin.py` file.

The last line allows us access to the `Article`s on our website from the admin site. Next we're gonna go to the admin site to see our `Article`s.

Since this is the admin site, only the administrator of the website has access to it. If you run the server (`python manage.py runserver`) and go to `127.0.0.1:8000/admin`, you should be met with the following page:
![Admin login](https://i.imgur.com/wRF8e8O.png)

Now let's create a user account that has access to the admin site. Now stop the server from running (`CTRL-C`) and run:
```bash
(superblog_env)$ python manage.py createsuperuser
```
This will ask you to enter a username, an optional email, and a password (and again to confirm the password). Those credentials you will use to login to the admin site. Once you've done that, login to the admin site and you should see the following page:
![Admin site](https://i.imgur.com/qDnxGBL.png)

You can see on the left side `ARTICLES` and under that is `Articles`. The `ARTICLES` refers to the Django app name we created. The `Articles` underneath it refers to the model name we created in our `models.py`. Django automatically adds an "`s`" at the end of the model name, to make it plural.

If you click on `Articles` you should see the following page:
![Admin Articles](https://i.imgur.com/al327uL.png)

You can see on the far left side it says "0 articles". That's because this page will display all the articles on our website that are stored in our database here as a list. But since this is a brand new website and database, we don't have any articles. Let's create one!

Click on the "ADD ARTICLE +" button on the top right corner. You should see the following form:
![Admin Add Article](https://i.imgur.com/U3IkJRO.png)

This form is created by Django's Admin site. The input fields are made to match the `Article` model fields. You may notice that the `created` field isn't in the form, because of the `auto_now_add` set to `True`, it knows that it's automatically generated, so it doesn't add it to this form.

Create an article here.

Don't worry too much about what the article really is, no one will see this. This is for development purposes only. Once our website is fully developed and ready for the world to see, we will delete any fake articles we made along the way.

Click the "Save and add another" button in the bottom right to continue adding more and more articles. Once you're done, go back to the Article list page that we saw previously. I created four articles:
![Admin Article List](https://i.imgur.com/nkoNDdC.png)

Now if you click on one of them, you'll be taken to a page where you can see all of that article's details, and can edit it to your liking.

([read more about the Django admin site here](https://docs.djangoproject.com/en/2.2/ref/contrib/admin/))