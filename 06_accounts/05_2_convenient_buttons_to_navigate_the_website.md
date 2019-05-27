I don't know about you, but I don't like it when I have to type the URL of the page I wanna go to *every single time* I wanna go to another page. It'd be much more convenient for me to just have buttons to press that'll take me where I want to go. Like websites generally do.

For example, if I'm in the article detail page and wanna go back to the list page, I'd have to go to the URL bar of the browser, delete everything after `127.0.0.1:8000/` from the URL, then hit Enter. Instead of all that, let's have a button from the article detail page to the article list page.

In your `detail.html`:
```django
<body>
    <a href="{% url 'article-list' %}">
        <button>
            Back
        </button>
    </a>
    ...
</body>
```
Now you should see a button that says "Back", and if you click on it you should be taken back to the article list page.

Let's do the same in the login page. What if I want to cancel logging in?

In both your `login.html` and `register.html`:
```django
<body>
    ...
    <a href="{% url 'article-list' %}">
        <button>
            Cancel
        </button>
    </a>
</body>
```
Now if you click the login button from the article list page, you can *cancel* and go back to the list page.