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

