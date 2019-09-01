Static files are files that are served to users that are not uploaded by users. For example, a website's logo image is a static file. However, a user's profile photo that they upload is not a static file.

To use bootstrap, which includes stylesheets and JavaScript files, we'll need to setup our Django project to serve static files, which will include any Bootstrap assets we'll have. ([read more](https://www.maxcdn.com/one/visual-glossary/static-content/) about static files)

To setup the project for static files, go into your `settings.py` and add these two lines at the very bottom:

```python
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'static')
```

Then, go into your `urls.py` and add the following:

```python
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    ...
]

urlpatterns+=static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
```

That's it! Our Django site is now ready to host and server static files.
