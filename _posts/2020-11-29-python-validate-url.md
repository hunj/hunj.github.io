---
layout: post
date: 2020-11-29
title: URL Validation in Python & Django
subtitle: 입력된 URL의 유효성을 검사하여 참/거짓 값을 반환하자.
tags:
- python
- django

---
파이썬으로 웹 관련 개발을 하다 보면 (유저가) 입력한 URL의 유효성을 검사해야 할 때가 있다. 대개 문자열로 들어오는 입력을 다음과 같이 비교하는 경우가 있다:

```python
def valid_url(to_validate: str) -> bool:
    if to_validate[:4] == "http":
        ...
```

물론 위와 같은 방법이 잘못된 건 아니지만 파이썬 혹은 장고에서 이미 제공되는 기본 내장 라이브러리를 이용하여 조금 더 간결하게 URL의 유효성을 검사해보자.

# Python

파이썬에는 `urllib` 이라는 내장 라이브러리가 있고 이 중 `parse` 모듈을 사용한다. ([참고](https://docs.python.org/3/library/urllib.parse.html#module-urllib.parse))

```python
from urllib.parse import urlparse

def valid_url(to_validate:str) -> bool:
    o = urlparse(to_validate)
    return True if o.scheme and o.netloc else False
```

# Django

장고의 내장 라이브러리 중에 입력을 검증해주는 `URLValidator` 라이브러리를 이용하면 된다.

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