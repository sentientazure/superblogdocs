# Cloning the starter project!

So in the previous section we walked you through creating a new django project from scratch and running it. It's important to know how that works before moving forward. However, for the purposes of this project we're building, I've prepared for you a *starter repository* you can clone. It's exactly the same as the project you've made in the previous section, but it has static files (CSS and JS) that we'll need for the coming chapters.

So delete the project we just made, but keep the virtual environment.
```bash
(superblog_env) $ cd ..
(superblog_env) $ rm -rf superblog
```

Then, to clone the starter repository:
```bash
(superblog_env) $ git clone https://github.com/sentientazure/superblog.git
```

Once this is done cloning, you'll see a new folder called "superblog". That folder is the Django project, complete with the app folder and everything. If you peak inside the app folder you'll see the `templates/` folder and the `static/` folder. These will be explained extensively when we get to them.
