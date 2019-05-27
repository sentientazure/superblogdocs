Now, let's make it so that a user in the list page can simply *click* on an article title and then be taken to its detail page.

In your `list.html`, change the following line:
```django
<li>{{ article.title }}</li>
```
to...
```django
<li>
    <a href="/articles/{{ article.id }}/">
        {{ article.title }}
    </a>
</li>
```
Now, the title text is wrapped inside an `<a>` tag, which makes it a link. The `href` of the `<a>` is `/articles/<ID>/`. This URL matches with the second `path()` we wrote in our `urls.py`.

Now, if you go to the list page at `127.0.0.1:8000`, the article titles should now be *links* you can *click*. If you click on one of them, you should be taken to the detail page of that article.

Notice that whenever you click on an article title in the list page, the URL you're taken to will have a different number with each article. And they all take you to different pages. However, all those different pages have been programmed *only once*. The second URL we defined (`'articles/<int:article_id>/'`) is dynamic. The second view, the `article_detail()` view, is also dynamic. Meaning that they can display different pages depending on the `id` they receive.

The next goal is to allow *users* to create their own articles. But before we can do that, we need to allow users to register and login to their own accounts on our website.