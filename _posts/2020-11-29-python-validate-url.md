---
layout: post
date: 2020-11-29
title: URL Validation in Python & Django
subtitle: URL 유효성을 검사하여 True/False 반환
tags: python django

---
# Python

Using urllib library

```python
from urllib.parse import urlparse

def valid_url(to_validate:str) -> bool:
    o = urlparse(to_validate)
    return True if o.scheme and o.netloc else False
```

# Django

Using core validator's URLValidator

```python
from django.core.validators import URLValidator
from django.core.exceptions import ValidationError

def valid_url(to_validate:str) -> bool:
    validator = URLValidator()
    try:
        validator(to_validate)
        # url is valid here
        # do something, such as:
        return True
    except ValidationError as exception:
        # URL is NOT valid here.
        # handle exception..
        print(exception)
        return False
```