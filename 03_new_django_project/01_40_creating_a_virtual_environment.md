## Analogy for the virtual environment
Think of a virtual environment in your computer like a room in a house. The project in the virtual environment like the person in their bedroom. Each person has their own bedroom, so their needs are all met in the privacy of their room. You'd separate the needs of each person in the house by giving them their own bedrooms.

It's possible for multiple projects to share a virtual environment, just as its possible for multiple people to share a bedroom. But most of the time, each project in its own virtual environment, each person in their own bedroom.

## What *is* a virtual environment?
A virtual environment is a tool that helps to keep dependencies required by different projects separate by creating isolated python virtual environments for them. This is one of the most important tools that most of the Python developers use.

Imagine you're this brilliant, hyper-active, top-of-the-line web developer. Obviously you're building multiple websites and applications for various clients at the same time. Everybody wants a piece of you. Now imagine one of your clients is demanding you use an older version of Django, say `1.11`. Being the accomodating person you are, you allow it and start building the website using Django 1.11. All your other projects are using the latest stable version of Django, at the time of writing this it's `2.1`. Now you have two projects, each using a different version of Django. How's that gonna work? How are you gonna tell this project to use Django 1.11 and the other project to use Django 2.1?

This is where virtual environments can save the day. Becuase of the complexity of software projects in general, we can put each project in its own virtual environment so that the frameworks and libraries it depends on (its "dependencies") are held separately from the dependencies of other projects in your computer.

### Creating a virtual environment
Now let's create a virtual environment for this project, and set the version of Python in it to Python 3. The following command will do that:
```bash
$ virtualenv --python=python3 superblog_env
```
In this command we created a virtual environment (`virtualenv`) then set its Python version to Python 3 (`--python=python3`), and called it *superblog_env*.