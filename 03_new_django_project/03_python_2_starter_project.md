So in the previous section we walked you through creating a new django project from scratch and running it. It's important to know how that works before moving forward. However, for the purposes of this project we're building, I've prepared for you a _starter repository_ you can clone. It's exactly the same as the project you've made in the previous section, but it has static files (CSS and JS) that we'll need for the coming chapters.

So delete the project we just made, but keep the virtual environment.

```bash
(superblog_env) $ cd ..
(superblog_env) $ rm -rf superblog
```

Then, go to the [starter repository](https://github.com/sentientazure/superblog.git) and fork it:

![Fork Repo](https://i.imgur.com/pD0Zwwg.gif)

Then copy the repository URL:

![Copy Repo URL](https://i.imgur.com/shlUTVn.gif)

Clicking this button will copy the URL of this repository. Next, clone it:

```bash
(superblog_env) $ git clone PASTE_URL_HERE
```

Once this is done cloning, you'll see a new folder called "superblog". That folder is the Django project, complete with the app folder and everything. If you peak inside the app folder you'll see the `templates/` folder and the `static/` folder. These will be explained extensively when we get to them.

---

### Trello

[Here](https://trello.com/b/nGiluPBM/superblog) is the trello board we'll be using to structure our approach to building this app. Copy the board to your account, and we'll walk through moving the cards to the appropriate lists throughout this project.

1. Open the `menu` on the top right corner of the screen
2. Click on "`More`"
3. Click on "`Copy Board`"
4. Give it the name "`Superblog`"
5. Click "`Create`"

Now you'll have a copy of the board over to your account. Moving the cards, adding new cards, and doing anything else on the board after copying it won't affect our public board.
