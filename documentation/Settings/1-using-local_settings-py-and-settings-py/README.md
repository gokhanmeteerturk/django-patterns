<sub>:arrow_left: [go back to **django-patterns**](../../../README.md)</sub>

## Using local_settings.py and settings.py

There is an industry standard for handling local settings for different environments running the same Django project. People usually create a local settings file and import it at the end of the settings.py file to override settings with local ones. Developers keep this file (often named as `local_settings.py`) out of the VSC, (for example, by adding its name to .gitignore file). And every development and production machine has its own local copy.

First, a local_settings file is created:
```python
# local_settings.py content
ALLOWED_HOSTS = ['*']
DEBUG = True
DATABASES = {
    ...
}
```

Then the original settings.py is modified to import these local settings:
```python
# settings.py
# ...
# ...
# Added to the end of file:
try:
    from local_settings import *
except ImportError:
    pass
```
Usually, a local_settings_template.py file is pushed to the version control system (a dedacted clone of local_settings.py) to give the devs an idea of what local settings they should configure.

For complex projects, however, this approach will have several disadvantages. Settings files will need to have some logic as well, and local settings file can't reference to the main settings file.

Another approach is to make local_settings import settings.py at the beginning, but this solution still has very similar disadvantages, therefore will not be explored here.

## A better approach

Another approach that is becoming increasingly popular is having a settings directory(or module) and setting the environment variable `DJANGO_SETTINGS_MODULE`.
Since Django Secret and other credentials are (hopefully) already stored as environment variables (in virtual environments), adding one more environment variable to the list shouldn't hurt.

For this to work, you should first have this structure for your project:
```bash
project_folder/
  |_...
  |_settings/
      |_development_settings.py
      |_production_settings.py
      |_some_other_settings.py
```
Then you should set the environment variable for each machine. For linux for example:
```bash
export DJANGO_SETTINGS_MODULE=settings.development_settings
export PYTHONPATH=/full/path/to/project_folder
```
## Leveraging the power of python-dotenv
If you are using `python-dotenv`, you may want to store these environment variables inside your .env file instead.

For this, you should tell manage.py and wsgi.py to read from dotenv variables:

```python
# manage.py
import sys
import os
import dotenv

def main():

    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'settings.development_settings') # make sure this is compatible with your PYTHONPATH!
    
    dotenv.load_dotenv(
        os.path.join(os.path.dirname(__file__), '.env')
    )
    
    if os.getenv("DJANGO_SETTINGS_MODULE"):
        os.environ["DJANGO_SETTINGS_MODULE"] = os.getenv("DJANGO_SETTINGS_MODULE")

    try:
        ...

...
    
```
And for the wsgi server:

```python
import os
from django.core.wsgi import get_wsgi_application
import dotenv

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'settings.development_settings')

dotenv.load_dotenv(
    os.path.join(os.path.dirname(__file__), '.env')
)

if os.getenv("DJANGO_SETTINGS_MODULE"):
    os.environ["DJANGO_SETTINGS_MODULE"] = os.getenv("DJANGO_SETTINGS_MODULE")

application = get_wsgi_application()
```


---

#### References and Helpful Resources
1. Popular .env solution [python-dotenv](https://pypi.org/project/python-dotenv/)
2. A [stackoverflow question](https://stackoverflow.com/questions/1626326/how-to-manage-local-vs-production-settings-in-django) with different approaches to having multiple(dev/prod) settings 

