---
layout: post
title: Django Week 1 - Django의 기초
tags: [Django]
image:
  path: /images/abstract-1.jpg
  feature: abstract-1.jpg
comments: true
---

이번 문서에서는 Django를 시작하는 법과 기초적인 홈페이지를 만드는 것에 대해 알아보도록 하겠습니다.

- - -

**"PYTHON & DJANGO를 활용한 웹 서비스 개발 CAMP"**을 수강하며 들은 내용을 정리 및 각색한 것입니다.

본 문서는 **python=3.6.2 & django=2.1.5** 기준으로 작성되었습니다.

- - -

## 0. 준비물

아래 프로그램 설치가 필요합니다.

- 파이썬(Python) : [https://www.python.org/downloads/](https://www.jetbrains.com/pycharm/download/#section=windows)
- 파이참(Pycharm Community) : [https://www.jetbrains.com/pycharm/download/#section=windows](https://www.jetbrains.com/pycharm/download/#section=windows)

## 1. 새로운 파이참 프로젝트 생성

File > New Projects를 클릭 후 프로젝트명을 입력 후 Create!

이후 아래를 Terminal(파이참 하단바에 위치)에 입력합니다,

장고 설치
> 터미널 명령 : pip install django 

config 뒤에 띄어쓰기+마침표를 찍으므로써, config 폴더가 중복되지 않도록 생성합니다.

폴더가 많을수록 접근하기 불편하여 아래와 같이 하는 것이며, 앞으로의 모든 파일은 이 명령을 기준으로 합니다.
> 터미널 명령 : django-admin startproject config . 

그럼 이제 프로젝트 폴더 안에 config 폴더가 생성된 것을 확인 할 수 있습니다!

## 2. 웹페이지 생성

처음 만들면 아주 기초적인 파일만 생성되기 때문에, '\_\_init\_\_.py', 'settings.py', 'urls.py', 'wsgi.py'만 있을 것입니다.

웹페이지를 만들기 위해서는 'views.py'라는 파일을 만들어주어야 합니다.

config 폴더에서 오른쪽 클릭을 하여 New > Python File 을 클릭한 후 views라고 입력합니다.

### 1) views.py 만들기

생성된 views.py에는 내용이 아무것도 없을 것입니다.

아래처럼 채워줍니다.


```python
from django.http import HttpResponse # Http로 응답 받음

# 관례적으로 하나의 매개변수를 받아야함, 변수 명이 request일 필요는 없음
def index(request) : #request를 받는 함수 
    return HttpResponse("Hello Django") # Hello Django를 페이지에 출력
```

위를 만드므로써 접속하면 Hello Django를 출력하는 페이지를 만들었습니다!

지금 view는 함수(def)로 만들었기 때문에 **함수형 뷰**라고 부르게 됩니다.

하지만, 아직 접속은 안될 것입니다.

### 2) urls.py에 view 추가하기

접속하려면 config > urls.py 에 주소를 입력해주어야합니다.

views.py에 있는 index라는 함수를 보여주기 위해 아래와 같이 urls.py를 변경해주어야합니다.


```python
from .views import * # views.py에서 import

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', index), # 메인페이지 접속시 index 실행
]
```

## 3. 웹페이지 구동

이제 우리의 페이지를 확인해볼 차례입니다.

Terminal에 아래 명령을 입력해줍니다.

 > 터미널 명령 : python manage.py runserver

[http://127.0.0.1:8000/](http://127.0.0.1:8000/)의 주소로 접속하면 홈페이지를 확인할 수 있습니다!

## 4. 동적 반응 웹페이지 구축

아직까지는 조금 심심합니다.

이제 조금 살을 붙여보도록 하겠습니다.

### 1) views.py에 추가


```python
def get_test(request) :
    message = "안녕하세요, {}님"
    
    #받은 request에서 mn이라는 변수를 얻어옴
    name = request.GET.get("mn", "유저") # 유저는 default

    return HttpResponse(message.format(name))
```

위와 같이 설정하면 url 주소에 [http://127.0.0.1:8000/&mn="유저"](http://127.0.0.1:8000/&mn="유저")로 입력할 시, 출력화면이 "유저"에 따라 달라지게 됩니다.


```python
def get_test(request) :
    message = "안녕하세요, {}님 ({})" # 2개 이상의 양식 스트림
    name = request.GET.get("mn", "유저")
    gender = request.GET.get("gd", "중성")

    return HttpResponse(message.format(name, gender))
```

마찬가지로 url 주소에 여러 개의 변수를 전달할 수도 있게 됩니다.

### 2) urls.py에 view 추가


```python
from .views import *

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', index), # 메인페이지 접속시 index 실행
    path('get/', get_test), # get/으로 접속시 get_test 실행
]
```

마지막으로 urls.py에 추가를 해주면 홈페이지에서 바뀐 내용을 확인할 수 있습니다.

## 5. 덧셈 계산기

이제 그럼 더 나아가 전달을 주소로 받지 않고, html에서 받도록 한 후에 덧셈을 한 값을 출력해보도록 하겠습니다.

### 1) views.py에 추가


```python
# 아래 데코레이터 없이 submit 버튼을 눌렀을 경우 에러 발생 (csrf 방어)
from django.views.decorators.csrf import csrf_exempt

# 데코레이터 : 함수를 실행하기 전에 별도의 기능 실행
# 아래 함수에 대해서는 csrf 사용 안 함.
@csrf_exempt
def add(request) :
    html = """
    <form action='' method='post'>
        <label>숫자 1</label><input type='text' name='num1'><br>
        <label>숫자 2</label><input type='text' name='num2'>
        <input type='submit' value='ADD'>
    </from><br>

    Results : {}
    """
    num1 = request.POST.get('num1', '0')
    num2 = request.POST.get('num2', '0')

    # Results의 괄호 안에 덧셈한 값을 전달
    # <주의> html에 값을 전달할 때 또는 받을 때는 항상 str이므로 형변환이 필요하다.
    html = html.format(int(num1) + int(num2))
    return HttpResponse(html)
```

### 2) urls.py에 view 추가


```python
from .views import *

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', index), # 메인페이지 접속시 index 실행
    path('get/', get_test), # /get/으로 접속시 get_test 실행
    path('add/', add), # /add/로 접속시 add 실행
]
```

이제 덧셈을 할 수 있게 되었다!

### 3) utils.py를 통한 python과의 연동

위에서는 views.py에서 덧셈을 진행하였는데, 이를 따로 utils.py로 분리할 수 있다!

utils.py를 config 폴더안에 만든 뒤 아래를 입력합니다.


```python
def add_function(num1, num2) :
    return num1 + num2
```

그 후 views.py를 아래와 같이 바꿔봅니다.


```python
# 아래 데코레이터 없이 submit 버튼을 눌렀을 경우 에러 발생 (csrf 방어)
from django.views.decorators.csrf import csrf_exempt
from utils.py import add_function

# 데코레이터 : 함수를 실행하기 전에 별도의 기능 실행
# 아래 함수에 대해서는 csrf 사용 안 함.
@csrf_exempt
def add(request) :
    html = """
    <form action='' method='post'>
        <label>숫자 1</label><input type='text' name='num1'><br>
        <label>숫자 2</label><input type='text' name='num2'>
        <input type='submit' value='ADD'>
    </from><br>

    Results : {}
    """
    num1 = request.POST.get('num1', '0')
    num2 = request.POST.get('num2', '0')

    # Results의 괄호 안에 덧셈한 값을 전달
    # <주의> html에 값을 전달할 때 또는 받을 때는 항상 str이므로 형변환이 필요하다.
    output = add_function(int(num1), int(num2))
    
    html = html.format(output)
    return HttpResponse(html)
```

다시 사이트를 확인하면 변한 것을 없지만, add_function을 통해 좀 더 깔끔하게 python 파일을 분리할 수 있습니다.
