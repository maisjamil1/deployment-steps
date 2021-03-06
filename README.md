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
_____________________________________________________________________________
## Docker
- `touch Dockerfile` inside this file add:
```python
FROM python:3
ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1
RUN mkdir /code
WORKDIR /code
COPY requirements.txt /code/
RUN pip install -r requirements.txt
COPY . /code/
```
- `poetry export -f  requirements.txt  -o requirements.txt`
- `touch docker-compose.yml`inside this file add:
```python
version: '3.8'

services:
    web:
        build: .
        command: python manage.py runserver 0.0.0.0:8000
        volumes:
            - .:/code
        ports:
            - "8000:8000"
```
- go to `settings.py` add to--->`ALLOWED_HOSTS = ['0.0.0.0','localhost','127.0.0.1',]`
- `docker-compose up -d`
____________________________________________________________________________________
# Deployment
- Change ` docker-compose.yml`to:
```python
version: '3.8'

services:
    web:
        build: .
        command: gunicorn stop_and_shop_back.wsgi:application --bind 0.0.0.0:8000 --workers 4
        volumes:
            - .:/code
        ports:
            - "8000:8000"
```
- `docker-compose restart`
- go to `settings` add-->:
```python
INSTALLED_APPS = [
           .......

    'whitenoise.runserver_nostatic',
    ....
  
    'corsheaders',
]


MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',
          ......

    'whitenoise.middleware.WhiteNoiseMiddleware',
]
```
- add the .env variables to  `settings.py`  add :
```python 
import os
import environ

env = environ.Env(
    # DEBUG is Flase by default
    DEBUG = (bool, False)
)
environ.Env.read_env()
.....
....
STATIC_DIR = os.path.join(BASE_DIR, 'static')
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
STATIC_URL = '/static/'
STATICFILES_DIRS = [
    STATIC_DIR,
]

CORS_ORIGIN_WHITELIST = [
    'http://localhost:3000',
]

```
also in `settings.py` edit :
```python
SECRET_KEY = env.str('SECRET_KEY')

DEBUG = env.bool('DEBUG')

ALLOWED_HOSTS = tuple(env.list('ALLOWED_HOSTS'))

DATABASES = {
    'default': {
        'ENGINE': env.str('ENGINE'),
        'NAME': env.str('DATABASE_NAME'),
        'USER': env.str('DATABASE_USER'),
        'PASSWORD': env.str('DATABASE_PASSWORD'),
        'HOST': env.str('DATABASE_HOST'),
        'PORT': env.str('DATABASE_PORT')
    }
}


```
-create `stopANDshop_project/.env` inside it add:
```python
DEBUG=True
SECRET_KEY=x%_&eqokja%xb5dp)9j4h+aowi5pp0a*8_a%e8i=p&98+6i@og
ALLOWED_HOSTS=localhost,127.0.0.1,0.0.0.0
ENGINE=django.db.backends.postgresql
DATABASE_NAME=mkdmtmix
DATABASE_USER=mkdmtmix
DATABASE_PASSWORD=edKVZLiTm3qRAlA3yu6xPLYke_p8z0Yk
DATABASE_HOST=hattie.db.elephantsql.com
DATABASE_PORT=5432

```

-in the root create `static`file 
- `python manage.py collectstatic`
- `docker-compose down`
- `docker-compose up -d` or `docker-compose up -d --build`
- &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
# heroku
- `touch heroku.yml` inside it add :
```python
build:
    docker:
        web: Dockerfile
release:
    image: web
    command:
        - mkdir -p static
        - python manage.py collectstatic --noinput
run:
    web: gunicorn stop_and_shop_back.wsgi

```
- in `.env` file change -->`DEBUG=False`
- `docker-compose down`
- `docker-compose up -d --build`
- `git init` dont forget `.gitignore` file -->add `.vscode/` to the `.gitignore`
- `git add .`
- `git status`
- `git commit -am' '`

- `heroku create shopapp`
- `heroku stack:set container`
- `git push heroku master`
- go to heroku website add:
![img](img1.PNG)

ALLOWED_HOSTS----> localhost,127.0.0.1,0.0.0.0,.stopandshopapp.com,stopandshopapp.herokuapp.com
