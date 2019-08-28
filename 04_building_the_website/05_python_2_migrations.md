Now that we've written the model class, we need to apply this structure to the database. Django handles most of the leg work associated with handling a database, so you don't need to learn the language of databases (e.g. SQL). To apply the structure to the database, in your Terminal, run the following command (to stop the server from running, hit `CTRL-C`):

```bash
(superblog_env)$ python manage.py makemigrations
```

You should see this:

```bash
Migrations for 'articles':
  articles/migrations/0001_initial.py
    - Create model Article
```

That command creates the _migrations files_. You don't need to fully understand this part. But these files (you can find them in `articles/migrations/`) contain instructions for Django to modify the structure of the database.

Run the following command to apply the structure to the database:

```bash
(superblog_env)$ python manage.py migrate
```

If done correctly, you should see:

```bash
Operations to perform:
  Apply all migrations: admin, articles, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  Applying articles.0001_initial... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying auth.0010_alter_group_name_max_length... OK
  Applying auth.0011_update_proxy_permissions... OK
  Applying sessions.0001_initial... OK
```

Now our database is fully prepared to store articles! Woohoo!!

([read more about migrations in Django here](https://docs.djangoproject.com/en/2.2/topics/migrations/))
