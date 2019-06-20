# Displaying The Published Date

Let's display, in the detail page, the published date and time of an article.

In your `detail.html`, replace the following line:
```django
<p>{{ article.author.first_name }} {{ article.author.last_name }}</p>
```
with:
```django
<p>Published by: {{ article.author.first_name }} {{ article.author.last_name }}</p>
<p>{{ article.published }}</p>
```

Voila! Now the published date and time display in the detail page. If you want, you can display the time since the article is published. For example, it would say "2 minutes ago" if you published the article two minutes ago.

To do that, replace the line:
```django
<p>{{ article.published }}</p>
```
with:
```django
<p>{{ article.published|timesince }} ago</p>