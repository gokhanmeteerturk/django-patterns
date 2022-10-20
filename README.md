# django-patterns
Commonly occurring design patterns in Django (In a 'this works for me' sense, rather than GOF design patterns)

## Sections

- About Django Patterns
    - [Prerequisites](documentation/Prerequisites/README.md)
    - How to use the docs
    - [How to contribute](CONTRIBUTING.md)

- Settings
    - [Using local_settings.py and settings.py](documentation/Settings/1-using-local_settings-py-and-settings-py/README.md)
    - Using default values for project and app settings
    - [Handling SECRET_KEY correctly](documentation/Settings/2-handling-secret-key-correctly/README.md)
    - Relative paths in the configuration
    - Choosing a media server

- Urls
    - Naming your paths correctly
    - Choosing a solid url strategy for apps
    - Hashids and crc32 for urls

- Models
    - Avoiding fat views caused by thin models

- Queries
    - Using Prefetch Objects

- Templates
    - Utilizing template blocks (aka 'chunks')
    - ...
