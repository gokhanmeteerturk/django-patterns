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

Definitely not in your public repos. If your Django project will be available publicly, it is a good practice to not include it at all. Django will not start without a secret key, so the absence of it won't be a security issue. (It will throw ImproperlyConfigured exception)

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

## Storing SECRET_KEY on different platforms:

#### Kubernetes
You can create `Secrets` from config files(eg: yaml files) using kubectl 
#### Heroku:
You can set heroku config with:
```
heroku config:set SECRET_KEY=.... # Without quotes.
```
Note: django-configurations looks for DJANGO_SECRET_KEY by default, and not SECRET_KEY.

#### Docker Swarm
There is Docker Secrets for Swarm. There are [several options](https://docs.docker.com/engine/swarm/secrets/#how-docker-manages-secrets) you can go with.
#### Github
Github Actions has encrypted secrets support. There are several ways of using this feature, but you should either use github cli or the web client.
See [creating encrypted secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets#creating-encrypted-secrets-for-a-repository) section of the Github Docs for more information.
```bash
gh secret set SECRET_KEY
```
A cool detail about Github's approach is that it uses Libsodium to ensure safety of the data transfer.

## Django's Secret Key Fallback Setting

If your SECRET_KEY is not compromised, but you still want to change it just in case, you can use SECRET_KEY_FALLBACKS constant to store your old secret key to maintain backwards compability with cookies, password reset hashes etc. This setting is added in Django 4.1 (which will create some overhead for each fallback key. So use it only if you really need to.)

## DON'T: Leaving SECRET_KEY In Repo For Easier Local Development

I've seen this on many repos. To make configuration of the local/development deployments less of a hassle, many projects leave the initial SECRET_KEY created by Django in settings.py. They just override this setting on any production instance, either by importing a local settings file which shadows the original variable with environmental variable or doing the same in settings.py by checking 'if' there is an environment variable and using the original if there isn't one.

This will put all your development machines at risk. Especially in more complex applications, completely making cryptography pointless for all development machines will create severe security risks. Don't use the same secret key in more then one machine and don't distribute secret keys over any network even if it will be used for development stage only.

---

#### References and Helpful Resources
1. Popular .env solution [python-dotenv](https://pypi.org/project/python-dotenv/)
2. [Source](https://serverfault.com/questions/547301/why-cant-django-configurations-pick-up-a-secret-key-environment-variable-from-a) for the note on heroku deployment with django-configurations
3. Django documentation on [SECRET_KEY_FALLBACK](https://docs.djangoproject.com/en/4.1/ref/settings/#secret-key-fallbacks)

