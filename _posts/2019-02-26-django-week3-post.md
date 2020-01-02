---
layout: post
title: Django Week 3 - 북마크 페이지 만들기
tags: [Django]
image:
  path: /images/abstract-1.jpg
  feature: abstract-1.jpg
comments: true
---

이번 문서에서는 Django를 통해 북마크를 관리할 수 있는 홈페이지를 만들어보겠습니다.

사실 이번 문서는 저번 BMI 관리 페이지와 내용만 다를 뿐 똑같습니다.

따라서, 복습이라 가정하고 설명은 최소화하겠습니다.

- - -

**"PYTHON & DJANGO를 활용한 웹 서비스 개발 CAMP"**을 수강하며 들은 내용을 정리 및 각색한 것입니다.

본 문서는 **python=3.6.2 & django=2.1.5** 기준으로 작성되었습니다.

- - -

## 1. 프로젝트 및 앱 생성

> 터미널 명령 : pip install django

> 터미널 명령 : django-admin startproject config .

> 터미널 명령 : python manage.py migrate

> 터미널 명령 : python manage.py createsuperuser

> 터미널 명령 : python manage.py startapp bookmark

## 2. models.py 수정


```python
{% raw %}
from django.db import models

# Create your models here.
class Bookmark(models.Model) :
    # 필드의 종류
    # 1. DB에 어떤 형태/제약 사항으로 데이터를 저장하느냐
    # 2. 프론트에서 어떤 형태/제약 사항으로 입력을 받을 것이냐
    site_name = models.CharField(max_length=50)
    url = models.URLField()

    def __str__(self) :
        return self.site_name + " : " + self.url

    # Todo : get_absolute_url mtehod

    # site_name의 오름차순 형태로 데이터 베이스 정렬
    class Meta :
        ordering = ['site_name']

# 모델의 내용을 데이터베이스에 반영
# 2가지 명령어를 사용해서
{% endraw %}
```

## 3. settings.py에 App 등록


```python
{% raw %}
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'bookmark',
]
{% endraw %}
```

## 4. 데이터베이스 초기화

> 터미널 명령 : python manage.py makemigrations bmi

> 터미널 명령 : python manage.py migrate bmi

## 5. admin.py 수정


```python
{% raw %}
from django.contrib import admin
from .models import *
# Register your models here.

class BookmarkAdmin(admin.ModelAdmin):
    list_display = ['id', 'site_name', 'url']

admin.site.register(Bookmark, BookmarkAdmin)

{% endraw %}
```

> 터미널 명령 :python manage.py runserver

## 6. List 보여주는 페이지 만들기

### 1) views.py 수정


```python
{% raw %}
#파이썬 모듈

#장고 모듈
from django.shortcuts import render
from django.views.generic.list import ListView

# 3rd party 모듈
from .models import *

class BookmarkList(ListView) :
    model = Bookmark
    template_name = 'bookmark/list.html'
{% endraw %}
```

### 2) templates/bookmark/list.html 만들기


```html
{% raw %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Bookmark List</title>
</head>
<body>
{% for object in object_list %}
    {{object.id}} {{object.site_name}} {{object.url}}<br>
{% endfor %}
</body>
</html>
{% endraw %}
```

### 3) bookmark 안에 urls.py 만들기


```python
{% raw %}
from django.urls import path

from .views import BookmarkList
urlpatterns = [
    path('', BookmarkList.as_view(), name='bookmark_list'),
]
{% endraw %}
```

### 4) config의 urls.py 수정


```python
{% raw %}
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('bookmark.urls'))
]
{% endraw %}
```

## 7. 수정/삭제/생성 추가

### 1) list.html 수정


```html
{% raw %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Bookmark List</title>
</head>
<body>
<a href="{% url 'bookmark_create' %}">생성</a>
<table>
{% for object in object_list %}
    <tr>
        <td>{{object.id}} </td>
        <td><a href="{% url 'bookmark_detail' object.id %}">{{object.site_name}}</a></td>
        <td><a href="{{object.url}}" target="_blank">{{object.url}}</a></td>
        <td><a href="{% url 'bookmark_update' object.id %}">수정</a></td>
        <td><a href="{% url 'bookmark_delete' object.id %}">삭제</a></td>
    </tr>
{% endfor %}
</table>
</body>
</html>
{% endraw %}
```

target = "_blank" : 새창에서 열기

### 2) views.py 수정


```python
{% raw %}
#파이썬 모듈

#장고 모듈
from django.shortcuts import render
from django.views.generic import *

# 3rd party 모듈
from .models import *

class BookmarkList(ListView) :
    model = Bookmark
    template_name = 'bookmark/list.html'

class BookmarkUpdate(UpdateView) :
    model = Bookmark
    fields = ['site_name', 'url']
    template_name = 'bookmark/bookmark_update.html'

class BookmarkDelete(DeleteView):
    model = Bookmark
    template_name = 'bookmark/bookmark_delete.html'
    success_url = '/'

class BookmarkCreate(CreateView):
    model = Bookmark
    fields = ['site_name', 'url']
    template_name = 'bookmark/bookmark_create.html'

class BookmarkDetail(DetailView):
    model = Bookmark
    template_name = 'bookmark/bookmark_detail.html'
{% endraw %}
```

### 3) models.py 수정


```python
{% raw %}
from django.db import models
from django.urls import reverse_lazy
# Create your models here.
class Bookmark(models.Model) :
    # 필드의 종류
    # 1. DB에 어떤 형태/제약 사항으로 데이터를 저장하느냐
    # 2. 프론트에서 어떤 형태/제약 사항으로 입력을 받을 것이냐
    site_name = models.CharField(max_length=50)
    url = models.URLField()

    def __str__(self) :
        return self.site_name + " : " + self.url

    def get_absolute_url(self):
        return reverse_lazy('bookmark_detail', args = [self.id])

    # site_name의 오름차순 형태로 데이터 베이스 정렬
    class Meta :
        ordering = ['site_name']

# 모델의 내용을 데이터베이스에 반영
# 2가지 명령어를 사용해서
{% endraw %}
```

### 4) urls.py 수정


```python
{% raw %}
from django.urls import path

from .views import *
urlpatterns = [
    path('', BookmarkList.as_view(), name='bookmark_list'),
    path('update/<int:pk>/', BookmarkUpdate.as_view(), name='bookmark_update'),
    path('delete/<int:pk>/', BookmarkDelete.as_view(), name='bookmark_delete'),
    path('create/', BookmarkCreate.as_view(), name='bookmark_create'),
    path('detail/<int:pk>/', BookmarkDetail.as_view(), name='bookmark_detail'),
]
{% endraw %}
```

### 5) 각 html 추가

bookmark_create.html


```html
{% raw %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Bookmark Create</title>
</head>
<body>
<form action="" method = "post">
    {{form.as_p}}
    {% csrf_token %}
    <input type="submit" value="추가완료">
</form>
</body>
</html>
{% endraw %}
```

bookmark_update.html


```html
{% raw %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Bookmark Update</title>
</head>
<body>
<form action="" method = "post">
    {{form.as_p}}
    {% csrf_token %}
    <input type="submit" value="수정완료">
</form>
</body>
</html>
{% endraw %}
```

bookmark_delete.html


```html
{% raw %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Bookmark Delete</title>
</head>
<body>
<span>Do you want to delete {{object}}?</span>
<form action="" method = "post">
    {% csrf_token %}
    <input type="submit" value="삭제완료">
</form>
</body>
</html>
{% endraw %}
```

bookmark_detail.html


```html
{% raw %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Bookmark Detail</title>
</head>
<body>
{{object.site_name}}<br>
{{object.url}}<br>
<a href="{% url 'bookmark_list' %}">홈으로</a>
</body>

</html>
{% endraw %}
```

## 8. 홈페이지 꾸미기

여태까지는 생성, 삭제, 수정하는 모든 페이지를 각각 모두 새로 생성했습니다.

하지만 실제 홈페이지에서는 메뉴바와 같은 항상 있어야할 것들은 어느 페이지에나 존재합니다.

일일히 다 페이지에 메뉴바를 넣어주면, 나중에 수정할 때 과장해서 100개 이상의 html을 수정해야할 수도 있습니다.

따라서 base.html을 이용해 좀 더 쉽게 이러한 일을 방지해보겠습니다.

### 1) templates 폴더 생성

프로젝트 폴더 안에 templates라는 폴더 만듭니다. (사실, 꼭 templates이라는 이름 아니어도 됩니다)

그 폴더 안에 base.html 만들고 아래와 같이 입력해줍니다.


```html
{% raw %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Bookmark{% block title %}{% endblock %}</title>
    <!--블록을 만들거고 이 블록의 이름은 title이야-->
</head>
<body>
{% block content %}
{% endblock %}
</body>
</html>
{% endraw %}
```

이제 나중에 다른 html에서

{% raw %}
{% block title %}
"타이틀"
{% endblock %}

{% block content %}
"내용"
{% endblock %}
{% endraw %}

과 같이 입력해주면, base.html에서 해당 부분만 안에 있는 내용으로 바뀌게 됩니다!

이를 활용하여 list.html을 수정해보겠습니다.


```python
{% raw %}
{% extends 'base.html' %}
{% block title %}-List{% endblock %}

{% block content %}
<a href="{% url 'bookmark_create' %}">생성</a>
<table>
{% for object in object_list %}
    <tr>
        <td>{{object.id}} </td>
        <td><a href="{% url 'bookmark_detail' object.id %}">{{object.site_name}}</a></td>
        <td><a href="{{object.url}}" target="_blank">{{object.url}}</a></td>
        <td><a href="{% url 'bookmark_update' object.id %}">수정</a></td>
        <td><a href="{% url 'bookmark_delete' object.id %}">삭제</a></td>
    </tr>
{% endfor %}
</table>
{% endblock %}
{% endraw %}
```

하지만, 이대로 실행하면 base.html을 찾지 못해 에러가 발생합니다.

이를 수정하기 위해서는 settings.py를 수정해야합니다.


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

위의 BASE_DIR은 프로젝트 폴더를 일컫는 변수이며, 그 안에 있는 templates를 본 홈페이지 템플릿에 연동하겠다라는 것입니다.

이제는 홈페이지가 실행이 되며, base.html에 list.html의 내용이 추가 된 것을 볼 수 있습니다.

마찬가지로 다른 html들도 이를 활용하여 base 기반으로 안의 바뀔 내용만 코딩하여 손쉽게 만들 수 있습니다.

### 2) 예쁜 모양 만들기

https://getbootstrap.com/

> ** 부트스트랩 :** 부트스트랩은 웹사이트를 쉽게 만들 수 있게 도와주는 HTML, CSS, JS 프레임워크이다. 하나의 CSS 로 휴대폰, 태블릿, 데스크탑까지 다양한 기기에서 작동한다. 다양한 기능을 제공하여 사용자가 쉽게 웹사이트를 제작, 유지, 보수할 수 있도록 도와준다. (출저 : 구글)

html을 꾸밀 때는 부트스트랩을 자주 활용하게 됩니다.

만약 처음 부트스트랩을 접하게 되었다면 아래 주소를 참고하시면 됩니다.

https://getbootstrap.com/docs/4.2/getting-started/introduction/

부트스트랩을 사용하기 위해선, base.html에 위 사이트의 CSS(1줄), JS(3줄) 아래와 같이 head에 붙여줍니다.


```html
{% raw %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Bookmark{% block title %}{% endblock %}</title>
    <!--블록을 만들거고 이 블록의 이름은 title이야-->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.2.1/css/bootstrap.min.css" integrity="sha384-GJzZqFGwb1QTTN6wy59ffF1BuGJpLSa9DkKMp0DgiMDm4iYMj70gZWKYbI706tWS" crossorigin="anonymous">
    <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.6/umd/popper.min.js" integrity="sha384-wHAiFfRlMFy6i5SRaxvfOCifBUQy1xHdJ/yoi7FRNXMRBu5WHdZYu1hA6ZOblgut" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.2.1/js/bootstrap.min.js" integrity="sha384-B0UglyR+jN6CkvvICOB2joaf5I4l3gm9GU6Hc1og6Ls7i6U/mkkaduKaBhlAXv9k" crossorigin="anonymous"></script>
    
</head>
<body>
{% block content %}
{% endblock %}
</body>
</html>
{% endraw %}
```

이제 부트스트랩을 사용할 수 있게 되었습니다!

여러분은 이제 컨테이너(container-fluid), 네비게이션(navbar) 등 이미 생성된 것들을 활용하여 예쁘게 페이지를 구성할 수 있게 되었습니다,

충분히 활용해보시길 바랍니다!
