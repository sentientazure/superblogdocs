Our next goal is to display on our website the list of articles for normal users, and allow them to click on an article to see the full article in a separate page. The list page would only display the title of each article, then when the user clicks on an article title, they can see the title, body, and author of the article in its own detail page.

## Queryset Basics
In your terminal, run the following command:
```bash
(superblog_env)$ python manage.py shell
```
You should see the following:
```bash
Python 3.6.4 (v3.6.4:d48ecebad5, Dec 18 2017, 21:07:28) 
[GCC 4.2.1 (Apple Inc. build 5666) (dot 3)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
(InteractiveConsole)
>>> 
```
This is the Python interpreter. But with this command, we can access our Django project's models. The following line of Python code will give you a list of *all* the articles in the database:
```bash
>>> from articles.models import Article
>>> Article.objects.all()
```
You should see this:
```bash
<QuerySet [<Article: Article object (1)>, <Article: Article object (2)>, <Article: Article object (3)>, <Article: Article object (4)>]>
```
Just as normal lists in Python work, you can loop through this list. Let's first store the list in a variable, then loop through this list and print out each article object:
```bash
>>> articles = Article.objects.all()
>>> for article in articles:
...     print(article)
```
Make sure you put four spaces before the print to indent it. Otherwise it won't work. You have to hit Enter again after the `print()` line to end of the for-loop. That's just how the Python interpreter knows that you're done writing the for-loop.

Once I did that, I saw:
```bash
Article object (1)
Article object (2)
Article object (3)
Article object (4)
```
That's the same list we saw in the admin site. Now, these prints aren't really telling us anything useful about the article. We don't know which article in this printed list is which article we created in the admin site. We can print a certain attribute of each article, for example the title. Let's write the for-loop again, but this time print out the *title*s of the articles:
```bash
>>> for article in articles:
...     print(article.title)
```
Once I ran that code, I saw:
```bash
What is Lorem Ipsum?
Where does it come from?
Why do we use it?
Where can I get some?
```
Those were the titles of the articles I created.

To exit the Python interpreter, type `quit()`.

([read more about Django querysets here](https://docs.djangoproject.com/en/2.2/topics/db/queries/))

What's the point of this? Why did we just print out the article titles in the terminal, when we already can see them in the admin site?

Well, the point of this is that now we know how to retrieve data from the database in Python. We can use that in the `views`, which is what we're gonna do next.
