-  `mkdir shopapi`
- `cd shopapi`
- `poetry init -n`
- `poetry shell`
- `poetry add django djangorestframework gunicorn whitenoise django-cors-headers django-environ psycopg2-binary`
- create `shopapidb`on `elephantsql.com`
- `django-admin startproject shop_project .`
- `python manage.py  migrate`
- `python manage.py  runserver`


