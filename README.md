-  `mkdir shopapi`
- `cd shopapi`
- `poetry init -n`
- `poetry shell`
- `poetry add django djangorestframework gunicorn whitenoise django-cors-headers django-environ psycopg2-binary`
- create `shopapidb`on `elephantsql.com`
- `django-admin startproject shop_project .`
- `python manage.py  migrate`
- `python manage.py  runserver`
- `copy from elephantsql.com the name.user,pass,host,port`and go to settings add them:
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME':  'yrvzgvki',
        'USER': 'yrvzgvki',
        'PASSWORD': 'LSdR8gCSGrS5Qd1OuAWd2ahR6CLYq-M2',
        'HOST': 'hattie.db.elephantsql.com',
        'PORT': 5432,

    }
}
```
- `python manage.py migrate`
- `python manage.py runserver`
- `python manage.py createsuperuser`
- go to SQL Browser check if the user there-->`SELECT * FROM auth_user;`
- `python manage.py startapp shop`
- shop_project/`urls.py`add:
```python
from django.conf.urls import url
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/v1/shop/', include('shop.urls')),
]
```
- go to shop/urls.py-->add:
```python
from django.urls import path

from .views import ShopList

urlpatterns = [
    path('', ShopList.as_view(), name='shop'),
]
```
- go to shop/`views.py` add:
```python
from rest_framework.generics import ListCreateAPIView

from .models import Shop
from .serializers import ShopSerializer

class ShopList(ListCreateAPIView):
    queryset = Shop.objects.all()
    serializer_class = ShopSerializer
```
- create shop/`serializers.py` add to it:
```python
from rest_framework import serializers

from .models import Shop

class ShopSerializer(serializers.ModelSerializer):
    class Meta:
        model = Shop
        fields = ('title', 'tyPe','description','title','category','owner','phone','email','address','price','id')
```
- go to shop/`models.py`  add:
```python
from django.db import models

class Shop(models.Model):
    title = models.CharField(max_length=64)
    tyPe = models.CharField(max_length=64)
    description= models.TextField()
    category = models.CharField(max_length=64)
    owner = models.CharField(max_length=64)
    phone = models.CharField(max_length=64)
    email = models.CharField(max_length=64)
    address = models.CharField(max_length=64)
    price = models.CharField(max_length=64)

    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    def __str__(self):
        return self.title
```
-go to shop/`admin.py` add:
```python
from django.contrib import admin

from .models import Shop
# Register your models here.

admin.site.register(Shop)
```
go to `settings.py`add :
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    'rest_framework',


    'shop.apps.ShopConfig',
]

```
- `python manage.py makemigrations shop`
- `python manage.py  migrate`
- `python manage.py runserver`
-in the localhost add items to the db
- go to the `SQL Browser` check if the data are there `SELECT * FROM shop_shop;`
