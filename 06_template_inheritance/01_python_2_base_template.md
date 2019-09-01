At this point, we have two HTML templates. One for the list page, another for the detail page. As our project grows larger and more complex, consistency across the pages becomes more and more important. Django gives us a brilliant solution to this issue of consistency. It's a _base_ template file where other template files inherit from it. Just like the master slide in PowerPoint.

By this point, both your templates, the list and detail templates, have some code that's exactly the same. This block of code:

```django
<!DOCTYPE html>
<html>
<head>
    <title></title>
</head>
<body>

</body>
</html>
```

Although the two pages have this in common, they have different values for the `<title>` and `<body>` tags. Later on, for example, if we need to link a stylesheet to both pages, we need to link the stylesheet in both HTML files. That's redundant and excessive. If only there's a way to write all this code only once and reuse it. Hmmm.....

Django's got you covered! Django comes with template inheritance. Where you can write the code that's the same across different pages only once and reuse it. Just what we need! To make this happen, make a new file called "base.html" in your `templates/` folder, and write this code in it:

```django
<!DOCTYPE html>
<html>
<head>
    <title></title>
</head>
<body>

</body>
</html>
```

The stuff inside the `<title>` and `<body>` tags will change depending on which page we're going to. So to make that happen, change the above code to the following:

```django
<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}{% endblock %}</title>
</head>
<body>
{% block body %}

{% endblock %}
</body>
</html>
```

These `{% block ... %}` tags are the magic. But don't be intimidated, you'll be a sorcerer soon.
