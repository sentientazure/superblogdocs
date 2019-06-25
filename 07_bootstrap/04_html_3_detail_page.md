# Styling the Detail Page!

For the detail page, open up the `blog_post.html` template. From that template, we want the following code:
```django
<div class="row">

      <!-- Post Content Column -->
      <div class="col-lg-8">

        <!-- Title -->
        <h1 class="mt-4">Post Title</h1>

        <!-- Author -->
        <p class="lead">
          by
          <a href="#">Start Bootstrap</a>
        </p>

        <hr>

        <!-- Date/Time -->
        <p>Posted on January 1, 2019 at 12:00 PM</p>

        <hr>

        <!-- Post Content -->
        [...]

    </div>
</div>
```

As we did in the list page, we're gonna replace the static/example data with variables, and remove the "`<!-- Date/Time -->`" portion:
```django
<div class="row">
    <!-- Post Content Column -->
    <div class="col-lg-12">
        <!-- Title -->
        <h1 class="mt-4">{{ article.title }}</h1>
        <!-- Author -->
        <p class="lead">
            by {{ article.author.username }}
        </p>
        <hr>
        <!-- Post Content -->
        {{ article.body }}

    </div>

</div>
<!-- /.row -->
```

Paste the above block of code to replace the following block of code in your `detail.html`:
```django
<a href="{% url 'article-list' %}">
    <button>
        Back
    </button>
</a>
<p>{{ article.author }}</p>
<p>{{ article.title }}</p>
<p>{{ article.body }}</p>
```

Booyaah! Now your detail page looks pretty nice, doesn't it?
