[Wikipedia](https://en.wikipedia.org/wiki/Markdown):

> Markdown is a lightweight markup language with plain text formatting syntax.

We'll be using [Python's Markdown](https://python-markdown.github.io) package to implement Markdown for our website. [Click here](https://daringfireball.net/projects/markdown/syntax) for Markdown syntax rules. To install Markdown:

```bash
(superblog_env)$ pip install markdown
```

Create a folder in your `articles/` folder, called `templatetags/`. Inside, create a file called `makemarkdown.py`. Inside:

```python
import markdown
from django import template
from django.template.defaultfilters import stringfilter
from markdown.extensions.codehilite import CodeHiliteExtension

register = template.Library()

@register.filter
@stringfilter
def makemarkdown(value):
    return markdown.markdown(value)
```

Let's dissect this code!

##### `register = template.Library()`, `@register.filter`, `@stringfilter`
These lines register the function `makemarkdown()` as a template filter. We'll be using this in our `detail.html` next.

##### `markdown.markdown(value)`
`value` here is the body of the article, as you'll see in the next step. This line takes the body of the article that has Markdown syntax, and will translate it to styled HTML.

Next, in your `detail.html`, change the following:
```django
...
<p>{{ article.body }}</p>
...
```
to:
```django
{% load makemarkdown %}
...
<p>{{ article.body|makemarkdown|safe }}</p>
...
```

Let's dissect this code...

##### `{% load makemarkdown %}`
This allows us to use the template tag called "makemarkdown". That's the function we defined previously in `articles/templatetags/makemarkdown.py`.

##### `{{ article.body|makemarkdown|safe }}`
This is what applies Markdown to the body of the article. What the `|` (pronounced "bar") character does here is it takes what's on its left side and sends it as an argument to the function on its right side. So `{{article.body}}` is sent as an argument to our function `makemarkdown`. We're receiving it as `value`. Then what our function `makemarkdown()` returns will be displayed here.

Now, if your server is running, you have to restart the server, because the new template tag we wrote won't be recognized until the server is restarted.

That's it! Now if you write a new article using [Markdown Syntax](https://daringfireball.net/projects/markdown/syntax) and go to the detail page, you'll see it's beautifully styled and formatted!