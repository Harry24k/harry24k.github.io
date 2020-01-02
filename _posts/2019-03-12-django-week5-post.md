---
layout: post
title: Django Week 5 - Bootstrap 테마 활용하기
tags: [Django]
image:
  path: /images/abstract-1.jpg
  feature: abstract-1.jpg
comments: true
---

이번 문서에서는 Bootstrap에서 제공하는 테마(Theme)를 활용하여 Django에 입혀보고, 손쉽게 꾸밀 수 있도록해보겠습니다.

- - -

**"PYTHON & DJANGO를 활용한 웹 서비스 개발 CAMP"**을 수강하며 들은 내용을 정리 및 각색한 것입니다.

본 문서는 **python=3.6.2 & django=2.1.5** 기준으로 작성되었습니다.

- - -

## 1. 원하는 테마 찾기

### 1) 무료 테마사이트 접속하기

[https://startbootstrap.com/themes/](https://startbootstrap.com/themes/)

위 주소로 이동하면 여러가지의 테마들이 존재하는데, 이 중 마음에 드는 것을 골라주시면 됩니다.

저는 그 중 아래 테마를 활용하도록 하겠습니다.

https://startbootstrap.com/themes/freelancer/

![bootstrap]({{site.url}}/images/django-week5-post/bootstrap.png){:.center-image}

**"FREE DOWNLOAD"** 버튼을 통해 해당 테마를 다운받아 줍니다.

물론 테마가 마음에 들지 않는다면, 다른 테마를 골라 다운 받아주시면 됩니다!

### 2) 테마 압축 풀기

이제 받은 파일의 압축을 풀어보도록 하겠습니다.

그러면 안에 css, img, js, vendor 등의 파일이 나오게 될텐데요.

이 테마 폴더들은 잠시 후 장고를 세팅한 후 다시 돌아와서 보도록하겠습니다.

## 2. Django 세팅

### 1) Django 프로젝트 및 앱 생성

우선 파이참에서 프로젝트를 생성한 뒤에 아래 명령을 이용해 장고를 세팅해줍니다.

> 터미널 명령 : pip install django

> 터미널 명령 : django-admin startproject config .

> 터미널 명령 : python manage.py migrate

> 터미널 명령 : python manage.py createsuperuser

> 터미널 명령 : python manage.py startapp theme

#### config/settings.py 수정


```python
{% raw %}
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'theme',
]
{% endraw %}
```

### 2) Django 기본적인 틀 만들기

이제 앱까지 만들었으니, 아주 기본적인 틀을 만들어 테마를 입힐 수 있게 하겠습니다.

우선 theme이라는 앱에서 보여주는 페이지는 week3에서 했던 것과 같이 템플릿을 생성한 후 안에 달라지는 내용만 구성할 것입니다.

#### theme/views.py 수정


```python
{% raw %}
from django.views.generic.base import TemplateView

class MainpageView(TemplateView):
    template_name = 'theme/main.html'
{% endraw %}
```

#### theme/urls.py 생성


```python
{% raw %}
from django.urls import path

from .views import *
urlpatterns = [
    path('', MainpageView.as_view(), name='mainpage'),
]

{% endraw %}
```

#### theme/templates/theme/main.html 생성


```python
{% raw %}
{% extends 'index.html' %}
{% endraw %}
```

바로 여기에 써져있는 index.html이 우리가 받은 테마파일의 index.html 파일이 될 것입니다.

물론 여기에 block title, endblock 혹은 block content, endblock를 사용하여 더 복잡하게 꾸밀 수 있습니다. (이는 3장을 참고해주세요)

#### config/urls.py 수정


```python
{% raw %}
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('theme.urls'))
]

{% endraw %}
```

#### config/settings.py 수정


```python
{% raw %}
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
{% endraw %}
```

## 3. 테마 적용하기

이제 테마를 적용할 차례입니다.

### 1) 파일 복사하기

먼저 해당 Django의 프로젝트 폴더로 이동합니다.

그 안에는 현재 config, theme 폴더와 manage.py 등이 있을 겁니다.

이제 여기에 templates 폴더와 static 폴더를 만들어줍니다.

그 후 아까 받았던 테마 폴더로 이동하여 index.html은 templates 폴더에 넣어줍니다.

테마 폴더의 css, img, js, vendor 폴더는 static 폴더로 이동해줍니다.

### 2) Django 수정하기

자 이제 파이참으로 돌아와서보면 static 폴더와 templates 폴더가 생성되어있을 겁니다!

이제 홈페이지를 실행해봅시다.

> 터미널 명령 : python manage.py runserver

네 그렇습니다. 제대로된 페이지가 뜨지 않습니다.

이는 static 파일로 인해 제대로된 페이지가 뜨지 않는 것입니다.

우선 index.html을 수정해줘야합니다.

#### templates/index.html 열기


```html
{% raw %}
<!DOCTYPE html>
<html lang="en">

<head>

  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
  <meta name="description" content="">
  <meta name="author" content="">

  <title>Freelancer - Start Bootstrap Theme</title>

  <!-- Bootstrap core CSS -->
  <link href="vendor/bootstrap/css/bootstrap.min.css" rel="stylesheet">

  <!-- Custom fonts for this template -->
  <link href="vendor/fontawesome-free/css/all.min.css" rel="stylesheet" type="text/css">
  <link href="https://fonts.googleapis.com/css?family=Montserrat:400,700" rel="stylesheet" type="text/css">
  <link href="https://fonts.googleapis.com/css?family=Lato:400,700,400italic,700italic" rel="stylesheet" type="text/css">

  <!-- Plugin CSS -->
  <link href="vendor/magnific-popup/magnific-popup.css" rel="stylesheet" type="text/css">

  <!-- Custom styles for this template -->
  <link href="css/freelancer.min.css" rel="stylesheet">

</head>

{% endraw %}
```

자 여기서 우리는 static 폴더와 index.html을 연결해주어야합니다.

아래와 같이 우선 load staticfiles을 맨 위에 붙여줍니다.


```python
{% raw %}
{% load staticfiles %}
<!DOCTYPE html>
<html lang="en">

<head>

  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
  <meta name="description" content="">
  <meta name="author" content="">

  <title>Freelancer - Start Bootstrap Theme</title>

  <!-- Bootstrap core CSS -->
  <link href="vendor/bootstrap/css/bootstrap.min.css" rel="stylesheet">

  <!-- Custom fonts for this template -->
  <link href="vendor/fontawesome-free/css/all.min.css" rel="stylesheet" type="text/css">
  <link href="https://fonts.googleapis.com/css?family=Montserrat:400,700" rel="stylesheet" type="text/css">
  <link href="https://fonts.googleapis.com/css?family=Lato:400,700,400italic,700italic" rel="stylesheet" type="text/css">

  <!-- Plugin CSS -->
  <link href="vendor/magnific-popup/magnific-popup.css" rel="stylesheet" type="text/css">

  <!-- Custom styles for this template -->
  <link href="css/freelancer.min.css" rel="stylesheet">

</head>

{% endraw %}
```

그 후 이제 static 파일에 해당하는 모든 부분을 바꿔주어야합니다.

예를 들면 아래와 같습니다.


```python
{% raw %}
<link href="vendor/bootstrap/css/bootstrap.min.css" rel="stylesheet">
{% endraw %}
```

를


```python
{% raw %}
<link href="{% static 'vendor/bootstrap/css/bootstrap.min.css' %}" rel="stylesheet">
{% endraw %}
```

로 바꿔줘야한다는 것이죠.

판단 기준은 아까 우리가 옮겼던 파일 안에 있으면 무조건 다 위와 같이 해줘야합니다.

css/~ 이거나 vendor/~ 이거나 img/~ 이거나 js/~이면 모두 위와 같이 바꿔주셔야합니다.

그럼 이제 index.html은 static 폴더와 연결이 되어있을 것이지만 하나가 남았습니다.

바로 settings.py를 수정하는 것입니다.

#### config/settings.py 수정


```python
{% raw %}
# STATIC_URL = '/static/'의 밑에 아래 삽입
STATICFILES_DIRS = (
    os.path.join(BASE_DIR, 'static'),
)
{% endraw %}
```

그리고 터미널 명령을 하나 실행해줍니다.

> 터미널 명령 : python manage.py collectstatic

yes를 누르면 static 파일이 모두 정리가 되어 static 폴더로 이동하게 됩니다.

그리고 이제 우리는 제대로된 페이지를 확인할 수 있게 됩니다!
