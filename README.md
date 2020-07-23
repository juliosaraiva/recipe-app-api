# recipe-app-api
Recipe App Api source code

### Custom Django User Model
To custom *Django User Model*, we need to import these three classes: `AbstractUser`, `BaseUserManager` and `PermissionsMixin`.
```python
from django.db import models
from django.contrib.auth.models import (
  AbstractBaseUser,
  BaseUserManager,
  PermissionsMixin
)
```

After that, we need to create a class named UserManager that inherit from `BaseUserManager`, basically with two methods: `create_user` and `create_superuser`

```python
class UserManager(BaseUserManager):
    def create_user(self, email, password=None, **extra_fields):
        """Creates and saves a new user"""
        if not email:
            raise ValueError("Users must have an email address")
        user = self.model(email=self.normalize_email(email), **extra_fields)
        user.set_password(password)
        user.save(using=self._db)

        return user

    def create_superuser(self, email, password):
        """Creates and saves a new super user"""
        user = self.create_user(email, password)
        user.is_staff = True
        user.is_superuser = True
        user.save(using=self._db)

        return user
```

Create a class User that will inherit from `AbstractBaseUser` and `PermissionsMixin`.

```python
class User(AbstractBaseUser, PermissionsMixin):
    """Custom user model that supports using email instead of username"""
    email = models.EmailField(max_length=255, unique=True)
    name = models.CharField(max_length=255)
    is_active = models.BooleanField(default=True)
    is_staff = models.BooleanField(default=False)

    objects = UserManager()

    USERNAME_FIELD = 'email'
```
In this case, we are defining that the username will be the email address, so, we need to set the constant `USERNAME_FIELD` to *email*.

In the file `settings.py`, we will define the constant `AUTH_USER_MODEL` pointing to *'core.User'*