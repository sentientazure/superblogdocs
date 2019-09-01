# Styling the List Page!

For styling the list page, open up the `blog_home.html` template. In it, you'll find the following:

```django
<div class="row">
    <!-- Blog Entries Column -->
    <div class="col-md-8">
        [...]
    </div>
</div>
<!-- /.row -->
```

Copy those `<div>`s, but delete everything inside of the inner `<div>` except the following Page Heading:

```django
<h1 class="my-4">Page Heading
    <small>Secondary Text</small>
</h1>
```

and one of the blog posts:

```django
<!-- Blog Post -->
<div class="card mb-4">
    <img class="card-img-top" src="http://placehold.it/750x300" alt="Card image cap">
    <div class="card-body">
        <h2 class="card-title">Post Title</h2>
        <p class="card-text">Lorem ipsum dolor sit amet, consectetur adipisicing elit. Reiciendis aliquid atque, nulla? Quos cum ex quis soluta, a laboriosam. Dicta expedita corporis animi vero voluptate voluptatibus possimus, veniam magni quis!</p>
        <a href="#" class="btn btn-primary">Read More &rarr;</a>
    </div>
    <div class="card-footer text-muted">
        Posted on January 1, 2017 by
        <a href="#">Start Bootstrap</a>
    </div>
</div>
```

In your `list.html`, replace of the following:

```django
<ul>
    {% for article in articles %}
        <li>
            <a href="{% url 'article-detail' article.id %}">
                {{ article.title }}
            </a>
        </li>
    {% endfor %}
</ul>
```

with:

```django
<div class="row">

    <!-- Blog Entries Column -->
    <div class="col-md-8">
        <h1 class="my-4">Page Heading
            <small>Secondary Text</small>
        </h1>

        <!-- Blog Post -->
        <div class="card mb-4">
            <img class="card-img-top" src="http://placehold.it/750x300" alt="Card image cap">
            <div class="card-body">
                <h2 class="card-title">Post Title</h2>
                <p class="card-text">Lorem ipsum dolor sit amet, consectetur adipisicing elit. Reiciendis aliquid atque, nulla? Quos cum ex quis soluta, a laboriosam. Dicta expedita corporis animi vero voluptate voluptatibus possimus, veniam magni quis!</p>
                <a href="#" class="btn btn-primary">Read More &rarr;</a>
            </div>
            <div class="card-footer text-muted">
                Posted on January 1, 2017 by
                <a href="#">Start Bootstrap</a>
            </div>
        </div>
    </div>
</div>
<!-- /.row -->
```

Now if you refresh the list page, you'll see it displays a nice-looking post, sort of. But it's not really the list page we're after. That's largely because we're not looping and displaying the list of articles. Can you look at this new pretty block of code and guess where we'd have to put the for-loop? Which is the most appropriate place?

Take a minute and see if you can figure this out on your own...

You ready for the answer?

You sure?

Okay...

Change the block of code that we just pasted into the list page to the following:

```django
<div class="row">

  <!-- Blog Entries Column -->
  <div class="col-12">

    <h1 class="my-4">Super Blog
      <small>All Articles</small>
    </h1>

    {% for article in articles %}
      <div class="card mb-4">
        <div class="card-body">
          <h2 class="card-title"><a href="{% url 'article-detail' article.id %}">{{ article.title }}</a></h2>
        </div>
        <div class="card-footer text-muted">
          Published on {{ article.published }} by {{ article.author.username }}
        </div>
      </div>
    {% endfor %}

  </div>
</div>
<!-- /.row -->
```

Now if you refresh the list page, you'll see it displays the list of articles and they look much neater. Look at the code above and try to understand exactly why we put the for-loop where we put it. Just ask yourself this: _Which part of the page, which part of the code, do we need to repeat?_

Now the original block of code displays a single article displays static text. But when we're looping and displaying the different titles of each article, we want to remove the existing static/example text with variables.

So you can see above, we changed the following:

```django
<h2 class="card-title">Post Title</h2>
```

to:

```django
<h2 class="card-title"><a href="{% url 'author-profile' article.author.username %}">{{ article.title }}</a></h2>
```

We turned the title from the text "Post Title" to the variable `{{ article.title }}`, and made it into a link.

We did the same for the article author name and the article published date.

There's a one minor change you might not notice immediately... But for you to understand the rest of this section, you need to have a decent grasp of Bootstrap's magic: The Grid System. Please [click here](https://getbootstrap.com/docs/4.3/layout/grid/) and read a bit about it, until you have a decent grasp of what it is, then come back and continue from here.

The minor change is that we replaced the following code:

```django
<!-- Blog Entries Column -->
<div class="col-md-8">
```

with:

```django
<!-- Blog Entries Column -->
<div class="col-12">
```

This is because we want the width of the blog article to take up the full width of the page. Now if you refresh the list page, you'll see it takes the whole width of the screen. It does look a bit awkward. That's because we're not finished prettifying everything!

You might've figured out by now that the Grid System in Bootstrap works only within a "`container`" `<div>`. Let's add that! In your `base.html` template, change the following:

```django
{% block body %}{% endblock %}
```

to:

```django
<div class="container">
    {% block body %}{% endblock %}
</div>
```

Now, refresh the list page and you'll see it looking much nicer!
