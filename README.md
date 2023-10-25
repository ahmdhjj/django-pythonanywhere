# django-pythonanywhere
A template for easy deployment of Django apps to PythonAnywhere (for Free Accounts using MySQL database)

## Guide

1. Replace `{{ project_name }}` by Django project name.
2. Replace `{{ project_username }}` by your PythonAnywhere username.
3. [Generate a new SSH key](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#generating-a-new-ssh-key) in a PythonAnyhwere Bash console and [add it to your github account](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account?tool=webui#adding-a-new-ssh-key-to-your-account)
4. Create a virtual env in a PythonAnywhere Bash Console in `/home/{{ project_username }}/` directory. For example, to create a Python 3.10 virtualenv called `myvirtualenv`:
   ```
   mkvirtualenv myvirtualenv --python=/usr/bin/python3.10
   ```
5. Clone the GitHub repo from a PythonAnyhwere Bash console in `/home/{{ project_username }}/` directory.

## Web App Configuration in PythonAnywhere
Go to the Web tab in PythonAnywhere and make sure the fields are set correctly:

**Source code:**
```
/home/{{ project_username }}/{{ project_name }}
```

**Working directory:**
```
/home/{{ project_username }}/
```

**WSGI configuration file:**
```
/var/www/{{ project_username }}_pythonanywhere_com_wsgi.py
```
**Virtualenv:**
```
/home/myusername/.virtualenvs/{{ virtualenv_name }}
```
**WSGI configuration file contents:**
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
## Using GitHub Actions to deploy to PythonAnywhere
### GitHub Secrets
#### MySQL
- `${{ secrets.MYSQL_DATABASE }}`
 
- `${{ secrets.MYSQL_ROOT_PASSWORD }}`
 
- `${{ secrets.MYSQL_USER }}`
 
- `${{ secrets.MYSQL_PASSWORD }}`
 
 #### PythonAnywhere
 For more details about the PythonAnywhere API: https://help.pythonanywhere.com/pages/API/
 
- `${{ secrets.PA_USER }}`: same as `{{ project_username }}`

- `${{ secrets.PA_VIRTUALENV }}`: same as `{{ virtualenv_name }}` created in step 4
 
- `${{ secrets.PA_TOKEN }}`: The PythonAnywhere API uses token-based authentication. You can get your token from your Account page on the API Token tab
 
- `${{ secrets.PA_CONSOLE_ID }}`: The ID of the console instance in PythonAnywhere used to run the commands (pull changes, migrate, collectstatic, reload app)

- `${{ secrets.PA_SRC_DIR }}`: The git repository in PythonAnywhere with the source code

- `${{ secrets.PA_SSH_KEY_PASSPHRASE }}`: The passphrase of the SSH key created in PythonAnywhere
