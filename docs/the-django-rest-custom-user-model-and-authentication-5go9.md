# Django Rest 定制用户模型和认证

> 原文：<https://dev.to/callmetarush/the-django-rest-custom-user-model-and-authentication-5go9>

我最近使用 Django rest + React 框架(顺便说一下，这个框架很漂亮)为我的一个客户建立了一个网站，遇到了一个问题。

# 问题

如果您曾经使用过 Django Rest，那么您将会知道在定制用户模型时会出现的多个问题，特别是您所做的更改不会反映到保存中。

我将向您展示在 DRF (Django Rest 框架)中不用用户名构建自定义用户模型的最佳方式(在我看来)。

## 第一步

创建一个名为 user(或者你想叫它什么)的应用程序，并在 models.py 中创建一个扩展 AbstractBaseUser 的用户模型

```
 from django.db import models
    from django.contrib.auth.models import AbstractBaseUser,BaseUserManager
    import datetime

    class User(AbstractBaseUser):

        username = None
        email = models.EmailField(_('email address'), unique=True)
        name = models.CharField(max_length=100)
        date_of_birth = models.DateField(default=datetime.date.today)

        USERNAME_FIELD = 'email'
        REQUIRED_FIELDS = [ 'date_of_birth','name' ]

        def __str__(self):              # __unicode__ on Python 2
            return self.email 
```

## 第二步

现在我们在模型中添加一条非常重要的线

```
 objects = UserManager() 
```

这基本上告诉 Django 应该如何存储对象，以及根据权限使用什么属性。

## 第三步

我们添加用户管理器

```
 class UserManager(BaseUserManager):

    use_in_migrations = True

    def create_user(self, email, name, date_of_birth, password=None):
        user = self.model(
            email=self.normalize_email(email),
            date_of_birth=date_of_birth,
            name=name,
        )
        user.set_password(password)
        user.save(using=self._db)
        return user

    def create_staffuser(self, email, name, date_of_birth, password):
        user = self.create_user(
            email,
            password=password,
            date_of_birth=date_of_birth,         
            name=name,
        )
        user.staff = True
        user.save(using=self._db)
        return user

    def create_superuser(self, email, name, date_of_birth, password):
        user = self.create_user(
            email,
            password=password,
            date_of_birth=date_of_birth,
            name= "True",
        )
        user.staff = True
        user.admin = True
        user.save(using=self._db)
        return user 
```

## 第四步

生成 CustomRegisterSerializer

```
 from rest_auth.registration.serializers import RegisterSerializer
    class CustomRegisterSerializer(RegisterSerializer):

        email = serializers.EmailField(required=True)
        password1 = serializers.CharField(write_only=True)
        name = serializers.CharField(required=True)
        date_of_birth = serializers.DateField(required=True)

        def get_cleaned_data(self):
            super(CustomRegisterSerializer, self).get_cleaned_data()

            return {
                'password1': self.validated_data.get('password1', ''),
                'email': self.validated_data.get('email', ''),
                'name': self.validated_data.get('name', ''),
                'date_of_birth': self.validated_data.get('date_of_birth', ''),
            }

    class CustomUserDetailsSerializer(serializers.ModelSerializer):

        class Meta:
            model = User
            fields = ('email','name','date_of_birth')
            read_only_fields = ('email',) 
```

创建自定义视图，该视图将所有用户对象作为一个查询集

```
 from rest_auth.registration.views import RegisterView

    class CustomRegisterView(RegisterView):
        queryset = User.objects.all() 
```

最后更新 settings.py 来应用这些

```
ACCOUNT_USER_MODEL_USERNAME_FIELD = None
ACCOUNT_AUTHENTICATION_METHOD = 'email'

ACCOUNT_EMAIL_REQUIRED = True
ACCOUNT_UNIQUE_EMAIL = True
ACCOUNT_USERNAME_REQUIRED = False
ACCOUNT_USER_EMAIL_FIELD = 'email'
ACCOUNT_LOGOUT_ON_GET = True

AUTH_USER_MODEL = 'users.User'

REST_AUTH_SERIALIZERS = {
    "USER_DETAILS_SERIALIZER": "users.serializers.CustomUserDetailsSerializer",
}
REST_AUTH_REGISTER_SERIALIZERS = {
    "REGISTER_SERIALIZER": "users.serializers.CustomRegisterSerializer",
} 
```

只需将视图添加到 URL，您就可以开始了！

老实说，我相信 Django Rest + React 堆栈是目前游戏中最好的堆栈之一，但是 Django Rest 框架确实需要更多的工作来使它对大多数开发人员来说是完美的。

如果你需要帮助，打电话给我！或者有一些项目需要人们和:D 一起工作