# Here comes... Markdown!
[Wikipedia](https://en.wikipedia.org/wiki/Markdown):

> Markdown is a lightweight markup language with plain text formatting syntax.

Markdown is a wonderful piece of technology that makes it very easy to write beautifully formatted content quite easily. Do you know how on websites like Reddit and apps like WhatsApp you can make your text bold by surrounding it in asterisks? For example, I could write *bold words* by typing them like this: \*bold words\*. Well, that's a simplified form of Markdown for you. The full Markdown we're implementing also allows you to write tables, headings, lists, images, and so much more!

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
{% block body %}
[...]
<p>{{ article.body }}</p>
[...]
{% endblock %}
```
to:
```django
{% load makemarkdown %}

{% block body %}
[...]
<p>{{ article.body|makemarkdown|safe }}</p>
[...]
{% endblock %}
```

Let's dissect this code...

##### `{% load makemarkdown %}`
This allows us to use the template tag called "makemarkdown". That's the function we defined previously in `articles/templatetags/makemarkdown.py`.

##### `{{ article.body|makemarkdown|safe }}`
This is what applies Markdown to the body of the article. What the `|` (pronounced "bar") character does here is it takes what's on its left side and sends it as an argument to the function on its right side. So `{{article.body}}` is sent as an argument to our function `makemarkdown`. We're receiving it as `value`. Then what our function `makemarkdown()` returns will be displayed here.

There is one issue with this implementation of Markdown: It leaves us vulnerable to Cross-Site Scripting (XSS) attacks. For example, if someone in their article wrote the following:
```
"><script>
  // Malicious JavaScript code goes here
</script>
```

our website would simply run the JavaScript code no matter what it was. So anyone could write a script that would run on every user's machine who opens that article. This leaves your users at the mercy of a clever programmer.

Let me show you a harmless example. Create a new article, and in the body write the following:
```
"><script>
  alert(document.cookie);
</script>
```

You might recognize that JavaScript line of code. If it runs as JavaScript, the webpage will popup a random string. So save that article and go to its detail page. If you're vulnerable to XSS, the popup will appear. That indicates that text written in an article that looks like JavaScript code will actually run on your website as unauthorized JavaScript code.

Now, keep this article, don't delete it. We'll come back to it later once we've patched this security flaw.

***[Bleach](https://pypi.org/project/bleach/) walks in and saves the day...***

Let's install Bleach and [Bleach-Whitelist]():
```bash
(superblog_env)$ pip install bleach
(superblog_env)$ pip install bleach-whitelist
```

Bleach (along with Bleack-Whitelist) sanitizes our HTML that Markdown produces. So Markdown takes Markdown syntax and converts it to HTML. But Markdown doesn't check for potentially malicious tags like `<script>`. So we'll take the HTML Markdown generates and give it to Bleach. Bleach will find any potentially malicious tags and escape them, which means they'll be displayed on the page as normal text, and won't be interpreted as HTML code. It'll make more sense once you try it. I'll walk you through testing it in a bit, once we implement Bleach.

In your `makemarkdown.py`, change the following function:
```python
@register.filter
@stringfilter
def makemarkdown(value):
    return markdown.markdown(value)
```
to:
```python
import bleach
from bleach_whitelist import markdown_tags, markdown_attrs

@register.filter
@stringfilter
def makemarkdown(value):
    return bleach.clean(markdown.markdown(value), markdown_tags, markdown_attrs)
```

Done! Bleach has been implemented.

Now, restart the server and go back to that article again. You should see the script displayed as normal text, and the popup doesn't appear. There you go! Now if anyone attempts to do XSS on your website, they'll be gravely disappointed.

That's it! Now if you write a new article using [Markdown Syntax](https://daringfireball.net/projects/markdown/syntax) and go to the detail page, you'll see it's beautifully styled and formatted!