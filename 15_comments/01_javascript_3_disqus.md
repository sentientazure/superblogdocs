# Disqus Comments

A blog platform like this wouldn't be very interesting without comments. Instead of building it ourselves from scratch, we're gonna use an external API for it. [Disqus](https://disqus.com) is a great one!

Follow these steps:

1. Make an account and login to [Disqus](https://disqus.com).
2. You'll be prompted to pick between two choices: "I want to comment on sites" or "I want to install Disqus on my site." Click "I want to install Disqus on my site".
3. You'll be asked to create a new site. The website name you enter here is not your actual website. It's just a unique name for Disqus to identify your website, so that it knows which comments on Disqus belong to which website using Disqus.
4. Pick a category for your website.
5. Pick the Basic free plan.
6. You'll be asked which platform your website is in. We've built our website from scratch. So scroll to the bottom and click "I don't see my platform listed, install manually with **Universal Code**".
7. You'll then see video instructions on how to install Disqus. We'll walk you through it anyway, but if our instructions here are outdated and don't work, follow the videos and instructions on Disqus.
8. In your `detail.html`, at the bottom of the body block:

   ```django
   {% block body %}
       [...]
       <div id="disqus_thread"></div>
       <script>

       /**
       *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
       *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/
       /*
       var disqus_config = function () {
       this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
       this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
       };
       */
       (function() { // DON'T EDIT BELOW THIS LINE
       var d = document, s = d.createElement('script');
       s.src = 'https://YOUR-URL-GOES-HERE.disqus.com/embed.js';
       s.setAttribute('data-timestamp', +new Date());
       (d.head || d.body).appendChild(s);
       })();
       </script>
       <noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
   {% endblock %}

   ```

9. In your `detail.html`, change the following lines:
   ```js
   /*
   var disqus_config = function () {
   this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
   this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
   };
   */
   ```
   to:
   ```django
   var disqus_config = function () {
   this.page.url = "{{ request.build_absolute_uri }}";  // Replace PAGE_URL with your page's canonical URL variable
   this.page.identifier = "{{ article.slug }}"; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
   };
   ```
10. In your `detail.html`, in the following line:
    ```js
    s.src = "https://YOUR-URL-GOES-HERE.disqus.com/embed.js";
    ```
    Replace `YOUR-URL-GOES-HERE` with the website name you entered when registering on Disqus. The code snippet you see on Disqus under the instructions video should have your website name in place of this.

And that's that! If you go to the detail page, the comment section should appear.
