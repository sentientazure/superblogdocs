Let's display, in the detail page, the published date and time of an article.

In your `detail.html`, replace the following:

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

with:

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
        <!-- Date/Time -->
        <p>Published on {{ article.published }}</p>
        <hr>
        <!-- Post Content -->
        {{ article.body }}

    </div>

</div>
<!-- /.row -->
```

Voila! Now the published date and time display in the detail page. If you want, you can display the time since the article is published. For example, it would say "2 minutes ago" if you published the article two minutes ago.

To do that, replace the line:

```django
{{ article.published }}
```

with:

```django
{{ article.published|timesince }} ago
```

---

Commit changes to git.
