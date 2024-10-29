# django-pythonanywhere
A template for continuous deployment of Django apps to PythonAnywhere (for Free Accounts using MySQL database) using GitHub actions

## Guide

1. Replace `{{ project_name }}` by Django project name.
2. Replace `{{ project_username }}` by your PythonAnywhere username.
3. [Generate a new SSH key](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#generating-a-new-ssh-key) in a PythonAnyhwere Bash console and [add it to your github account](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account?tool=webui#adding-a-new-ssh-key-to-your-account)
4. Create a virtual env in a PythonAnywhere Bash Console in `/home/{{ project_username }}/` directory.

_To create a Python 3.10 virtualenv called `myvirtualenv`:_
```
   mkvirtualenv myvirtualenv --python=/usr/bin/python3.10
   ```
5. Store your environment variables in a `/home/{{ project_username }}/{{ project_name }}/.env` file.

_In `/home/{{ project_username }}/{{ project_name }}` run:_
```
echo "export SECRET_KEY=sekritvalue" >> .env
```
6. Load up the environnment variables from `.env` file by adding the `source` command below to your virtualenv postactivate script:

_In `/home/{{ project_username }}/{{ project_name }}` run:_
```
echo 'set -a; source .env; set +a' >> ~/.virtualenvs/myvirtualenv/bin/postactivate
```
_In the code, access the variable from `os.getenv`:_
```
import os
SECRET_KEY = os.getenv("SECRET_KEY")
```
7. Clone the GitHub repo from a PythonAnyhwere Bash console in `/home/{{ project_username }}/` directory.

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
**Static files:**
|URL|Directory|
|----|-------|
|`/static/`|`/home/{{ project_username }}/{{ project_name }}/static/`|

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

# set environment variables (from `.env`)
from dotenv import load_dotenv
project_folder = os.path.expanduser(project_home)  # adjust as appropriate
load_dotenv(os.path.join(project_folder, '.env'))

# serve django via WSGI
from django.core.wsgi import get_wsgi_application
application = get_wsgi_application()

```
## GitHub secrets to add to the repository
 
- `${{ secrets.PA_USER }}`: same as `{{ project_username }}`

- `${{ secrets.PA_VIRTUALENV }}`: same as `{{ virtualenv_name }}` created in step 4
 
- `${{ secrets.PA_TOKEN }}`: The PythonAnywhere API uses token-based authentication. You can get your token from your Account page on the API Token tab

- `${{ secrets.PA_CONSOLE_ID }}`: The ID of the console instance in PythonAnywhere used to run the commands (pull changes, migrate, collectstatic, reload app)

- `${{ secrets.PA_SRC_DIR }}`: The path to the git repository in PythonAnywhere with the source code (`/home/{{ project_username }}/{{ project_name }}` for example)

- `${{ secrets.PA_SSH_KEY_PASSPHRASE }}`: The passphrase of the SSH key created in PythonAnywhere

For more details about the PythonAnywhere API: https://help.pythonanywhere.com/pages/API/

## How-to guides and help pages from PythonAnywhere

 The pages below helped create this guide:

- [Integrating a development environment with PythonAnywhere](https://help.pythonanywhere.com/pages/IntegratingWithPythonAnywhere/)

- [Deploying an existing Django project on PythonAnywhere](https://help.pythonanywhere.com/pages/DeployExistingDjangoProject/)

- [How to use a virtualenv in your web app (to get newer versions of django, flask etc)](https://help.pythonanywhere.com/pages/Virtualenvs/)

- [How to set environment variables for your web apps (for SECRET_KEY etc)](https://help.pythonanywhere.com/pages/environment-variables-for-web-apps/)

- [How to setup static files in Django](https://help.pythonanywhere.com/pages/DjangoStaticFiles/)
