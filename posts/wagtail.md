Install  Wagtail on FreeBSD 11.1
----

```
pkg install python3.6 py36-sqlite3-3.6.6_7  

```

```python 
pip install wagtail
wagtail start mysite
cd mysite
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
```