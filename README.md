# django-pythonanywhere
A template for easy deployment of Django apps to PythonAnywhere

1. Replace `{{ project_name }}` by Django project name.
2. Replace `{{ project_username }}` by your PythonAnywhere username.

Source code:
`/home/{{ project_username }}/{{ project_name }}`

Working directory:
`/home/{{ project_username }}/`

WSGI configuration file:
`/var/www/{{ project_username }}_pythonanywhere_com_wsgi.py`

WSGI configuration file contents:
```
# This file contains the WSGI configuration required to serve up your
# web application at http://{{ project_username }}.pythonanywhere.com/
# It works by setting the variable 'application' to a WSGI handler of some
# description.
#
# The below has been auto-generated for your Django project

import os
import sys

# add your project directory to the sys.path
project_home = '/home/{{ project_username }}/{{ project_name }}'
if project_home not in sys.path:
    sys.path.insert(0, project_home)

# set environment variable to tell django where your settings.py is
os.environ['DJANGO_SETTINGS_MODULE'] = '{{ project_name }}.settings'


# serve django via WSGI
from django.core.wsgi import get_wsgi_application
application = get_wsgi_application()

```
