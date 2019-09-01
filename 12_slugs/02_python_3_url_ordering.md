The slugs we're now using in place of IDs introduced a problem. If you go to the create page at `127.0.0.1:8000/articles/create/`, you should see the following error:

![Article matching query does not exist.](https://i.imgur.com/ZSYtGbg.png)

This is because the first `path()` that matches with `/articles/create/` is this one:

```python
path('articles/<slug:article_slug>/', views.article_detail, name="article-detail"),
```

Django sees the "create" in "`/articles/create/`" as a _slug_. Then the `article_detail()` view will run, looking for an article with the slug "create". Which doesn't exist. That's why this error is popping up.

To fix that, we can re-order our list of URL patterns. Currently, the list looks like this:

```python
urlpatterns = [
    path('admin/', admin.site.urls),
    path('articles/', views.article_list, name="article-list"),
    path('articles/<slug:article_slug>/', views.article_detail, name="article-detail"),
    path('register/', views.register_view, name="register"),
    path('login/', views.login_view, name="login"),
    path('logout/', views.logout_view, name="logout"),
    path('articles/create/', views.article_create, name="article-create"),
    path('drafts/', views.draft_list, name="draft-list"),
    path('drafts/<slug:article_slug>/', views.draft_edit, name="draft-edit"),
    path('drafts/<slug:article_slug>/delete/', views.draft_delete, name="draft-delete"),
    path('profile/', views.profile, name="profile"),
    path('profile/edit/', views.profile_edit, name="profile-edit"),
    path('profiles/<str:username>/', views.author_profile, name="author-profile"),
]
```

Let's re-organize it to fix this issue, and also to have the list be easy to navigate with human eyes. Let's change the list to the following:

```python
urlpatterns = [
    path('admin/', admin.site.urls),
    path('articles/', views.article_list, name="article-list"),
    path('articles/create/', views.article_create, name="article-create"),
    path('articles/<slug:article_slug>/', views.article_detail, name="article-detail"),

    path('drafts/', views.draft_list, name="draft-list"),
    path('drafts/<slug:article_slug>/', views.draft_edit, name="draft-edit"),
    path('drafts/<slug:article_slug>/delete/', views.draft_delete, name="draft-delete"),

    path('register/', views.register_view, name="register"),
    path('login/', views.login_view, name="login"),
    path('logout/', views.logout_view, name="logout"),
]
```

This way, if we're going to `/articles/create/`, it'll match with the `article_create()` view first. Which is what we want.
