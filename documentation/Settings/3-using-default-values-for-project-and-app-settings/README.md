<sub>:arrow_left: [go back to **django-patterns**](../../../README.md)</sub>

## Using default values for project and app settings

No matter how confident you are with the availability of constants and environment variables in your project, it would help if you didn't take that availability for granted.

Django settings tend to get more complicated and complex as the project grows. A constant hardcoded to settings.py today may turn into a dynamic setting or get imported from the local environment tomorrow. Depending on your setup, it may also need more than one default value.

When that happens, you won't want to find all usages of the setting variable and check if your code makes correct assumptions for the defaults.

> **❌ Bad Practice**
> ```python
> # don't do this:
> import settings
> 
> MY_ICON = settings.DEFAULT_ICON
> # 'TouchAppIcon'
> ```

Bad practice:


> **✅ Good practice**
>
> ```python
> 
> MY_ICON = getattr(settings, 'DEFAULT_ICON' , 'TouchAppIcon')
> ```

### Creating a helper module

I have seen this several times, but not as often as I would like to.
One may argue that this is necessary since getattr does what you need with little effort.
But creating a settings module is a cool approach towards having a more OOP-oriented solution for settings management.

```python
# file name: app_conf.py
# assuming you have an app called polls, as in Django's official tutorial.
# also assuming you are trying to keep your apps reusable
from polls import settings as polls_settings
# instead of: from django.conf import settings

class PollsSettingsView(object): # may also inherit from a BaseSettingsView or a mixin
   class Defaults(object):
      MY_CONSTANT = 10
      pass

   def __init__(self):
      self.defaults = PollsSettingsView.Defaults()

   def __getattr__(self, name):
      return getattr(polls_settings, name, getattr(self.defaults, name))
```
Use this like:
```python
from polls.app_conf import PollsSettingsView

polls_settings = PollsSettingsView()
print(polls_settings.MY_CONSTANT)
# Outputs 10 if no 'MY_CONSTANT' in app's settings.py


polls_settings.defaults.MY_CONSTANT = 5
print(polls_settings.MY_CONSTANT)
# Outputs 5 if no 'MY_CONSTANT' in app's settings.py
```

---

#### References and Helpful Resources
1. Check out other examples on [this SO page]([https://github.com/gokhanmeteerturk/django-patterns](https://stackoverflow.com/questions/5601590/how-to-define-a-default-value-for-a-custom-django-setting))
2. For complex projects, take a look at [Django Dynamic Preferences](https://github.com/agateblue/django-dynamic-preferences)
3. [Constance](https://github.com/jazzband/django-constance) is also a nice solution for migrating static settings to dynamic ones.
