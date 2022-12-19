# Django:迁移-伪造-初始捕获

> 原文：<https://dev.to/k4ml/django-migrate---fake-initial-gotchas-2fih>

我们在升级 Django-1.0 以来的旧系统版本时遇到了一些问题，Django-1.0 后来逐渐升级到 1.4、1.8，最后升级到 1.11(最新的 1.x 版本)。注意，数据库迁移框架只在 Django-1.7 中登陆。在这个新的迁移框架中，您必须显式地生成一个迁移文件。在此之前，`syncdb`命令将根据您的模型定义动态生成表格(该表格并不存在)。对表的后续更改，如添加新列，必须通过外部方式手动处理。

直到 1.8，显式迁移文件还不是强制性的，所以`migrate`命令基本上表现出与旧的`syncdb`相同的行为。但是由于我们要更新到 1.11，我们必须为系统中的所有应用程序创建迁移文件。当我们运行`migrate`命令时，这个初始迁移基本上包括创建表，由于所有的迁移还没有被跟踪，它将尝试应用它，这显然会失败，因为所有的表已经存在。

为此，django 提供了两种选择:-

```
--fake¶
Tells Django to mark the migrations as having been applied or unapplied, but
without actually running the SQL to change your database schema.

This is intended for advanced users to manipulate the current migration state
directly if they’re manually applying changes; be warned that using --fake runs the
risk of putting the migration state table into a state where manual recovery will
be needed to make migrations run correctly.

--fake-initial¶
Allows Django to skip an app’s initial migration if all database tables with the
names of all models created by all CreateModel operations in that migration already
exist. This option is intended for use when first running migrations against a
database that preexisted the use of migrations. This option does not, however,
check for matching database schema beyond matching table names and so is only safe
to use if you are confident that your existing schema matches what is recorded in
your initial migration. 
```

Enter fullscreen mode Exit fullscreen mode

我们决定用`--fake-initial`来表示`migrate`。但是我们得到的错误表已经存在。起初这没有意义，正如我们在上面看到的，它会检测表是否已经存在，然后只是“假应用”迁移。我们知道这个表确实存在，那么为什么 Django 仍然想要创建这个表呢？

我遍历了`django/db/migrations/executor.py`中的迁移代码，并在这一部分放了一个`pdb.set_trace()`:-

```
 # Make sure all create model and add field operations are done
        for operation in migration.operations:
            import pdb;pdb.set_trace()
            if isinstance(operation, migrations.CreateModel):
                model = apps.get_model(migration.app_label, operation.name)
                if model._meta.swapped: 
```

Enter fullscreen mode Exit fullscreen mode

通过这一步，幸运的是错误很早就发生了，我可以看到它正确地检测到了表的存在:-

```
(Pdb) n
> /home/kamal/.local/lib/python3.4/site-packages/django/db/migrations/executor.py(337)detect_soft_applied()
-> if should_skip_detecting_model(migration, model):
(Pdb) n
> /home/kamal/.local/lib/python3.4/site-packages/django/db/migrations/executor.py(339)detect_soft_applied()
-> if model._meta.db_table not in existing_table_names:
(Pdb) l
334                         # We have to fetch the model to test with from the
335                         # main app cache, as it's not a direct dependency.
336                         model = global_apps.get_model(model._meta.swapped)
337                     if should_skip_detecting_model(migration, model):
338                         continue
339  ->                 if model._meta.db_table not in existing_table_names:
340                         return False, project_state
341                     found_create_model_migration = True
342                 elif isinstance(operation, migrations.AddField):
343                     model = apps.get_model(migration.app_label, operation.model_name)
344                     if model._meta.swapped:
(Pdb) n
> /home/kamal/.local/lib/python3.4/site-packages/django/db/migrations/executor.py(341)detect_soft_applied()
-> found_create_model_migration = True 
```

Enter fullscreen mode Exit fullscreen mode

直到到了这个迭代:-

```
> /home/kamal/.local/lib/python3.4/site-packages/django/db/migrations/executor.py(330)detect_soft_applied()
-> import pdb;pdb.set_trace()
(Pdb) operation.name
'Image'
(Pdb) n
> /home/kamal/.local/lib/python3.4/site-packages/django/db/migrations/executor.py(331)detect_soft_applied()
-> if isinstance(operation, migrations.CreateModel):
(Pdb) n
> /home/kamal/.local/lib/python3.4/site-packages/django/db/migrations/executor.py(332)detect_soft_applied()
-> model = apps.get_model(migration.app_label, operation.name)
(Pdb) n
> /home/kamal/.local/lib/python3.4/site-packages/django/db/migrations/executor.py(333)detect_soft_applied()
-> if model._meta.swapped:
(Pdb) n
> /home/kamal/.local/lib/python3.4/site-packages/django/db/migrations/executor.py(337)detect_soft_applied()
-> if should_skip_detecting_model(migration, model):
(Pdb) n
> /home/kamal/.local/lib/python3.4/site-packages/django/db/migrations/executor.py(339)detect_soft_applied()
-> if model._meta.db_table not in existing_table_names:
(Pdb) n
> /home/kamal/.local/lib/python3.4/site-packages/django/db/migrations/executor.py(340)detect_soft_applied()
-> return False, project_state 
```

Enter fullscreen mode Exit fullscreen mode

那么，为什么它检测到该表不存在呢？检查变量`existing_table_names`终于给了我一盏明灯。该表确实不存在！

```
(Pdb) model._meta.db_table not in existing_table_names
True
(Pdb) model._meta.db_table
'blog_image'
(Pdb) existing_table_names
['auth_group', 'auth_group_permissions', 'auth_permission', 'auth_user', 'auth_user_groups', 'auth_user_user_permissions', 'blog_article', 'django_admin_log', 'django_content_type', 'django_migrations', 'django_session'] 
```

Enter fullscreen mode Exit fullscreen mode

发生的情况是，它将表作为一个组来检测，因此如果有一个表不存在，那么将应用整个特定迁移，这意味着也创建已经存在的表。在我们的例子中，发生这种情况是因为当转储生产数据库以测试这种迁移时，我们排除了一些具有大数据的表以保持转储较小。这意味着产生的结构与迁移文件中描述的不匹配，在上面的文档中已经警告过了，唉。