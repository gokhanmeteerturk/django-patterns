<sub>:arrow_left: [go back to **django-patterns**](../../../README.md)</sub>

## Handling SECRET_KEY correctly

Django's SECRET_KEY is used to secure signed data. Not just by the Django itself. Most Django libraries use SECRET_KEY if they need to generate hashes or tokens. If an attacker has your SECRET_KEY, they can modify cookies sent by your application. May even be able to execute remote code or guess next hash/token to be generated in some cases.

Django's own documentation states:
> Running Django with a known SECRET_KEY defeats many of Django’s security protections, and can lead to privilege escalation and remote code execution vulnerabilities.

If your SECRET_KEY is already compromised, you need to change it ASAP. This can be any valid bytecode or string(Though it [should follow some rules](https://github.com/django/django/blob/0dd29209091280ccf34e07c9468746c396b7778e/django/core/checks/security/base.py#L207), eg. should have a minimum number of unique characters.)

You can generate a new, valid one by using Django's own function. (PS: Changing your django key will invalidate all cookies, password reset tokens, [and more](https://docs.djangoproject.com/en/4.1/ref/settings/#std-setting-SECRET_KEY))

```python
from django.core.management.utils import get_random_secret_key

print(get_random_secret_key())

# Output: &rm@t)4gpo0*vm#m-ct_@=(p-8!gi_k&z*128*8x&p#02gu^$=
```

But how/where to store this SECRET_KEY so that it is not compromised at all?




## Where To Keep My Secret Key?

Definitely not in your public repos. If your Django project will be available publicly, it is a good practice to not include it at all. Django will not start without a secret key, so the absence of it won't be a security issue. 

Just as all other secrets, industry standard for storing a SECRET_KEY for a Django project is keeping it in local&virtual environment. dotenv is a popular solution to make this easier. And your settings file can simply import the secret key from environment.

[python-decouple](https://github.com/henriquebastos/python-decouple/
) is a great example of how you can integrate 'secrets in environment' approach with your application settings:
```js
// .env file content:
SECRET_KEY = ...
```
```python
# settings.py:

from decouple import config
SECRET_KEY = config("SECRET_KEY")

```
### Heroku:
```
heroku config:set SECRET_KEY=.... # Without quotes.
```

## Django's Secret Key Fallback Setting

If your SECRET_KEY is not compromised, but you still want to change it just in case, you can use SECRET_KEY_FALLBACKS constant to store your old secret key to maintain backwards compability with cookies, password reset hashes etc. This setting is added in Django 4.1 (which will create some overhead for each fallback key. So use it only if you really need to.)




---

#### References and Helpful Resources
1. Popular .env solution [python-dotenv](https://pypi.org/project/python-dotenv/)
2. 

