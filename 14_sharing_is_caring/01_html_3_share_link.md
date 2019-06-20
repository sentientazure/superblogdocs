# Sharing Is Caring
Let's put a button at the bottom of the article detail page for sharing this article. A basic sharing functionality, in this case, means to copy the URL of the article so that the user can just paste it elsewhere to share it. So we're gonna have a share button that copies the URL of this article to clipboard, as if the user selected the URL from the URL bar and copied it. This button will copy it for them.

In your `detail.html`, at the bottom of the body block:
```django
{% block body %}
    [...]
    <button onclick="copyToClipboard('{{ request.build_absolute_uri }}')">
        Share Link
    </button>

    <script>
        const copyToClipboard = str => {
            const el = document.createElement('textarea');
            el.value = str;
            el.setAttribute('readonly', '');
            el.style.position = 'absolute';
            el.style.left = '-9999px';
            document.body.appendChild(el);
            el.select();
            document.execCommand('copy');
            document.body.removeChild(el);
            $("#poppy").popover("toggle");
        };
    </script>
{% endblock %}
```

It's all basic HTML and JavaScript. The `copyToClipboard()` function is what does the actual copying into clipboard. We have a button that says "Share Link" that if clicked will call this function to copy to clipboard. You can read the JavaScript code to try and understand how it works.

##### `{{ request.build_absolute_uri }}`

This will return the absolute URL of this current page. So if the article's title is "Hello World", this will return "http://127.0.0.1:8000/articles/hello-world/". Of course once deployed to a web server with a domain name, this URL will automatically be changed to "http://<YOUR_DOMAIN_NAME>.com/articles/hello-world/". For example: "http://example.com/articles/hello-world/".

[Read more about `request.build_absolute_uri` here](https://docs.djangoproject.com/en/2.2/ref/request-response/#django.http.HttpRequest.build_absolute_uri)