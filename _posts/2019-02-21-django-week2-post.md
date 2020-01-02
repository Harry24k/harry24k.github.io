---
layout: post
title: Django Week 2 - BMI 관리 사이트 생성
tags: [Django]
image:
  path: /images/abstract-1.jpg
  feature: abstract-1.jpg
comments: true
---

이번 문서에서는 Django를 통해 입력, 수정, 삭제 등을 할 수 있는 사람들의 BMI 정보를 관리하는 홈페이지를 만들어보겠습니다.

- - -

**"PYTHON & DJANGO를 활용한 웹 서비스 개발 CAMP"**을 수강하며 들은 내용을 정리 및 각색한 것입니다.

본 문서는 **python=3.6.2 & django=2.1.5** 기준으로 작성되었습니다.

- - -

## 1. 파이참 프로젝트 만들기

File > New Projects를 클릭 후 프로젝트명을 입력 후 Create!

이후 아래를 Terminal(파이참 하단바에 위치)에 입력합니다,

## 2. 장고 설치하기

> 터미널 명령 : **pip install django**

## 3. 장고 프로젝트 만들기

> 터미널 명령 : **django-admin startproject config .**

마지막에 점을 찍음으로써 config 폴더 안에 config를 만들지 않습니다.

## 4. DB 초기화

이제 여기서부터는 저번 시간에 다루지 않은 내용입니다.

Django에서는 관리자를 위한 데이터베이스(DB) 시스템을 지원하며, 다양한 프로그램 사용 가능합니다.

본문에서는 SQLITE을 사용하여 회원관리/로그인 등을 위한 기본 테이블 생성할 것입니다.

우선 DB를 생성하기 위해 아래 명령을 입력합니다.

> 터미널 명령 : **python manage.py migrate**

## 5. 관리자 계정 만들기

이제 사이트를 관리할 수 있는 관리자를 추가해야합니다.

> 터미널 명령 : **python manage.py createsuperuser**

이후에는 아이디, 비밀번호가 나오는데 입력되지 않는 것 같아도 입력되고 있으므로 당황하지 말고 입력하여 Enter를 누르면 됩니다!

## 6. 서버 실행

사이트 생성이 잘되었는지 확인을 위해 서버를 실행해보겠습니다.

> 터미널 명령 : **python manage.py runserver**

아마 아무런 문제 없이 잘 되었을거라 생각합니다.

사실 여태까지는 하나의 웹페이지를 생성하기 위한 작업이었습니다.

이제부터는 사이트에 들어갈 기능들을 추가하기 위해 기능 단위로 구분된 단위 프로그램인 "앱"을 생성하겠습니다.

## 7. 앱 만들기

앱이란 기능 단위로 구분된 단위 프로그램이며, 추후 다른 웹사이트에서도 재사용이 가능한 이점이 있습니다.

> 터미널 명령 : **python manage.py startapp bmi**

위 명령에서 bmi은 앱이름(app name)으로써 자유롭게 지정가능합니다.

위 명령어를 실행시 앱이름으로 된 폴더가 프로젝트 폴더 안에 생성되는 것을 확인할 수 있습니다.

해당 폴더 안에는 '\_\_init\_\_.py'를 포함하여 'admin.py', 'apps.py', 'models.py', 'tests.py', 'views.py' 등이 존재할 것입니다.

이러면 앱을 생성하는 것이 완료되었습니다!

## 8. 앱 추가하기

하지만, 앱을 웹페이지에 추가하기 위해서는 생성 후에 추가 작업이 필요합니다.

앱을 만들면 항상 아래 작업을 해주어야 합니다.

config > settings.py에 아래와 같이 INSTALLED_APPS에 앱이름을 추가하여 줍니다.


```python
{% raw %}
# Application definition

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'bmi',
]
{% endraw %}
```

위와 같이 수정하면 이제 웹페이지에서 해당 앱을 사용할 준비가 된 것입니다 :)

## 9. 모델 만들기

생성한 앱이 데이터를 다루기 위해서는 앱 폴더에 있는 models.py를 수정해야합니다.

* **models.py의 역할 :**

사실 DB를 다루기 위해서는 SQL 언어를 알아야합니다. 하지만, 그러한 일을 피하기 위하여

웹 프로그래머가 DB 작업은 신경 쓰지 않고 로직에만 집중할 수 있도록 만들어진(ORM) 것이 models.py의 역할입니다.

models.py에서는 데이터베이스에 어떤 테이블을 만들고 그 테이블에 어떤 컬럼을 어떤 타입으로 만들지 결정하는 것이 주 내용이 되겠습니다.

* 해당 앱의 DB는 db.sqlite3로 저장되며 앱이름_모델이름으로 테이블이 생성됩니다.

models.py에 이름, 키, 체중, BMI 값을 저장하기 위한 모델을 아래와 같이 설정하겠습니다.


```python
{% raw %}
class BMI(models.Model) :
    name = models.CharField(max_length=20) # 길이 최대 20의 문자
    height = models.FloatField() # 소수 숫자
    weight = models.FloatField()
    bmi = models.FloatField(blank=True, null=True)
    # blank=True : 필수 필드가 아닌 값임을 의미
    # null=True : NULL 값도 가능함을 의미
{% endraw %}
```

자 이제 DB를 정의하였는데, 주의할 점이 있습니다.

models.py를 변경할 경우 DB에 반영하기 위해서는 아래의 두 명령어를 실행해야합니다!

> 터미널 명령 : **python manage.py makemigrations bmi**

위 명령어는 앱의 변경 사항을 추적하여, 데이터베이스에 반영할 내용을 문서로 작성합니다.

작성된 문서는 migrations에서 확인 가능합니다.

> 터미널 명령 : **python manage.py migrate bmi**

위 명령어는 변경 사항 문서를 기준으로 데이터베이스에 반영합니다.

그럼 이제 bmi 앱은 DB를 사용할 준비가 되었습니다.

**(+) 일반적으로 BMI는 몸무게와 키를 주면 자동적으로 계산할 수 있습니다. 이를 DB에 반영하고 싶다면?**

아래처럼 기존 models.py를 수정하면 됩니다!


```python
{% raw %}
class BMI(models.Model) :
    name = models.CharField(max_length=20)
    height = models.FloatField()
    weight = models.FloatField()
    bmi = models.FloatField(blank=True, null=True)
    
    # 값을 저장할 때 일어나는 일을 조종하고 싶을 때
    def save(self, force_insert=False, force_update=False, using=None,
             update_fields=None):
        
        # bmi 값 입력
        self.bmi = self.weight / ((self.height/100) ** 2) # BMI 계산식
        
        # 부모의 save를 호출하여 실제 세이브 구동
        super(BMI, self).save(force_insert, force_update, using, update_fields)
{% endraw %}
```

이 때, 위와 같이 변경했을 때는 migration을 변경하지 않아도 자동적으로 업데이트가 되는 데,

그 이유는 DB 자체의 구조가 바뀔 필요가 없기 때문입니다.

## 10. 뷰 만들기

관리자 페이지에 우리가 만든 앱을 등록하기 위해서는 앱 폴더에 있는 admin.py를 수정해야합니다.


```python
{% raw %}
# 내가 작성한 모델을 관리자 페이지에서 관리할 수 있도록 추가
# 관리자 페이지의 목록이나 기능을 커스터마이징 하는 옵션을 추가

from .models import BMI
admin.site.register(BMI) # app을 register에 추가해주어야함
{% endraw %}
```

해당 앱이 반영된 것은 admin 사이트에서 확인 가능합니다. [http://127.0.0.1:8000/admin/](http://127.0.0.1:8000/admin/)

![BMI]({{site.url}}/images/django-week2-post/bmi.png){:.center-image}

BMI 앱이 등록되었습니다!

이제 관리자 사이트에서 BMI의 Add버튼을 통해 데이터 추가하거나 변경 가능합니다.

저는 Add를 눌러 "타미"라는 이름으로 데이터를 입력하였습니다.

![BMI_info]({{site.url}}/images/django-week2-post/bmi_info.png){:.center-image}

그 후 Bmis를 클릭하면 위와 같은 이미지가 출력되는데, 이는 데이터베이스를 보여주는 것입니다.

만약 여태까지 똑같이 코딩했다면 위와 같이 "타미의 BMI 수치"가 아닌 "타미"처럼 이름만이 있을 것입니다.

이는 아래와 같이 models.py를 수정해면 똑같은 결과가 나올 것 입니다.


```python
{% raw %}
class BMI(models.Model) :
    name = models.CharField(max_length=20)
    height = models.FloatField()
    weight = models.FloatField()
    bmi = models.FloatField(blank=True, null=True)
    
    # 값을 저장할 때 일어나는 일을 조종하고 싶을 때
    # save 입력시 Tab을 활용하여 자동완성 기능 사용 추천
    def save(self, force_insert=False, force_update=False, using=None,
             update_fields=None):
        
        # bmi 값 입력
        self.bmi = self.weight / ((self.height/100) ** 2) 
        
        # 부모의 save를 호출하여 실제 세이브 구동
        super(BMI, self).save(force_insert, force_update, using, update_fields
                               
    # magic method
    # 우리가 만든 것을 출력할 때 어떻게 출력할 것인가?
    def __str__(self):
        return self.name + "의 BMI 수치" # <주의> 항상 문자 리턴
{% endraw %}
```

만약 이름뿐만 아니라, 내용물을 모두 확인하고 싶다면 admin 페이지를 수정해주어야합니다.

아래와 같이 admin.py를 수정해주면 됩니다. 


```python
{% raw %}
# 내가 작성한 모델을 관리자 페이지에서 관리할 수 있도록 추가
# 관리자 페이지의 목록이나 기능을 커스터마이징 하는 옵션 클래스를 추가

from .models import BMI

class BMIAdmin(admin.ModelAdmin) :
    list_display = ['id', 'name', 'height', 'weight', 'bmi'] # 무엇을 띄워줄 것인가?
    # 무조건 list_display로 선언해주어야함
    
admin.site.register(BMI, BMIAdmin) # BMI와 함께 class를 추가해야 보임!
{% endraw %}
```

![BMI_adv_info]({{site.url}}/images/django-week2-post/bmi_adv_info.png){:.center-image}

그럼 이제 위와 같이 보여지는 형태가 바뀌게 됩니다.

- - -

사실 여태까진 사용자가 접근할 방법이 없습니다.

관리자만 웹페이지에 BMI를 기록할 수 있는 것이죠.

이제부터는 뷰를 활용하여 사용자가 생성, 접근, 수정, 삭제, 리스트 확인(CRUDL : Create, Read, Update, Delete, List)할 수 있도록 하겠습니다.

뷰 : 뷰란 어떤 기능단위, 로직을 처리하는 함수 혹은 클래스를 의미합니다.

단순히 말하면 뷰는 하나의 주소에 연결된 하나의 페이지라 할 수 있습니다.

물론 화면에 나타나게 하거나(Templete 필요) 나타나지 않게 할 수 있습니다.

사실 저번 시간에 함수형 뷰는 이미 만든 적이 있습니다.

따라서 오늘부터는 클래스형 뷰를 사용해보려합니다.

* 함수형 뷰 : def 함수로 만든 뷰 / 자유도가 높은 대신 결정해야할 것이 많음

* 클래스형 뷰 : class 클래스로 만든 뷰 / 많이 사용되는 형태의 뷰(제네릭 뷰)를 상속받아서 사용

클래스형 뷰를 사용은 아래와 같이 bmi > views.py를 변경합니다.


```python
{% raw %}
from django.views.generic.list import ListView # ListView : List를 보여주기 위한 제네릭 뷰
from .models import BMI # BMI 모델을 불러와서

class BMIList(ListView) :
    model= BMI # BMI를 활용한 List를 만들 것임
{% endraw %}
```

## 11. 템플릿 만들기

원래는 위에서 언급한 것과 같이 화면에 표시하기 위해서는 Templete(*.html 파일*)이 필요합니다.

하지만 잠시 넘어가보도록 하겠습니다.

## 12. URL 연결하기

config 안의 urls.py에 모든 앱의 주소를 기록하려면 복잡해지게 됩니다.

따라서 일반적으로 앱의 기능에 따른 주소는 각 앱의 urls.py에서 진행하며, 프로젝트의 config > urls.py에는 앱의 메인 주소를 추가해줍니다.

이를 위해 bmi 안에 새로 urls.py를 만들어야 합니다.

만든 후에는 아래를 입력하여 초기화합니다.


```python
{% raw %}
from django.urls import path

urlpatterns = [
    
    
]
{% endraw %}
```

이 때, urlpatterns 안에 있는 것은 아래와 같은 의미를 가집니다.


```python
{% raw %}
# admin/ 이하의 주소를 admin.site.urls를 전달해라
# ex) 127.0.0.0:8888/admin/bmi/base.html이면 bmi/base.html을 admin.site.urls에 전달
path('admin/', admin.site.urls),
{% endraw %}
```

좀 더 자세히 설명하자면 아래와 같습니다.


```python
{% raw %}
# 1) url pattern
# 2) view
# 3) 1)번 url pattern의 이름
path(1, 2 ,3),
{% endraw %}
```

다음과 같이 코딩하면, List를 주소로 보여줄 수 있습니다.


```python
{% raw %}
from django.urls import path

from .views import BMIList
urlpatterns = [

    # 1) url pattern
    # 2) view
    # 3) 1)번 url pattern의 이름
    path('', BMIList.as_view(), name = 'bmi_list'),
]
{% endraw %}
```

단, 이 때 함수형 뷰와 다르게 이름만이 아니라, 클래스형 뷰의 경우 .as_view()를 입력해주어야합니다.

그 후 config > urls.py를 아래와 같이 수정하여 메인 홈페이지와 앱을 해주어야합니다.


```python
{% raw %}
from django.urls import include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('bmi.urls')), # 그냥 들어올 경우, bmi.urls를 방문하여라
]
{% endraw %}
```

자, 이제 python manage.py runserver를 실행하고 들어가보면..

![Template Error]({{site.url}}/images/django-week2-post/template error.png){:.center-image}

TemplateDoesNotExist at / 에러가 뜹니다!

## 11. 템플릿 만들기

이는 보여줄 html 파일이 없기 때문이며, 사실 아까 잠깐 스킵하고 넘어갔기 때문입니다.

이를 수정하기 위해서는 다음과 같이 해야합니다.

1. "bmi" 앱 폴더 안에 "templates" 폴더 만들기
2. "templates" 폴더 안에 앱 이름("bmi") 폴더 만들기
3. 방금 만든 "bmi" 앱 폴더 안에 "앱_제네릭뷰.html"(bmi_list.html) 폴더 만들기

최종적으로 bmi_list.html은 아래와 같이 위치하게 됩니다.

프로젝트 명 > bmi > templates > bmi > bmi_list.html

그리고 다시 python manage.py runserver를 실행하면 빈 페이지가 뜨게 될 것입니다.

이제는 자유롭게 html 수정을 통해 사용자에게 보여질 페이지를 수정하면 됩니다.


```python
{% raw %}
#{% %} : 기능 태그
#{{}} : 출력 태그
{% endraw %}
```


```html
{% raw %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>BMI List</title>
</head>
<body>

{% for object in object_list %}
    {{object.name}} {{object.height}} {{object.weight}} {{object.bmi}}<br>
{% endfor %}
</body>
</html>
{% endraw %}
```

이 때 object_list로 받아오는 이유는 받아오는 객체가 여러 개이기 때문입니다.

조금 더 친절하게 안에 있는 내용을 테이블을 활용하여 보여주기 위해서는 아래와 같이 수정해주시면 됩니다.


```html
{% raw %}
<!DOCTYPE html>
<html lang="en" xmlns="http://www.w3.org/1999/html">
<head>
    <meta charset="UTF-8">
    <title>BMI List</title>
</head>
<body>
<table border="1">
    <thead>
        <tr>
            <th>이름</th>
            <th>신장</th>
            <th>체중</th>
            <th>BMI</th>
            <th>수정</th>
            <th>삭제</th>
        </tr>
    </thead>
{% for object in object_list %}
    <tr>
        <td> {{object.name}}</td> <td> {{object.height}}</td> <td>
        {{object.weight}}</td>  <td> {{object.bmi}}</td>
        <td>수정</td><td>삭제</td>
    </tr>
{% endfor %}
</table>
</body>
</html>
{% endraw %}
```

이 때 위를 잘 살펴보면 수정 기능을 추가하고자 하여 수정과 삭제를 넣었지만, 아직 구현이 되어 있지 않은 상태입니다.

아래 예제부터는 순서대로 수정, 생성, 삭제의 기능을 추가하게 됩니다.

### - 수정 기능 추가하기

먼저 **수정 기능**을 추가해보겠습니다.

### 1) views.py에 아래를 추가


```python
{% raw %}
from django.views.generic.edit import UpdateView
class BMIUpdate(UpdateView) :
    model = BMI
    fields = ['name', 'height', 'weight'] # name, height, weight 만 입력받음을 알려줌
    template_name = 'bmi/bmi_update.html' # 이 앱은 template_name을 다음과 같이 사용하겠다.
{% endraw %}
```

### 2) urls.py 아래와 같이 수정


```python
{% raw %}
from django.urls import path

from .views import BMIList, BMIUpdate
urlpatterns = [
    path('', BMIList.as_view(), name = 'bmi_list'),
    path('update/<int:pk>/', BMIUpdate.as_view(), name = 'bmi_update'),
]
{% endraw %}
```

글쓰기/수정을 할 때에는 어느 것을 수정할 지 알아야하므로 주소에 전달을 해주어야하는 데, 쿼리로 날리게 될 시 www.naver.com/?document_id=~ 와 같이 더러워지기 때문에 'int:pk'라는 것을 활용합니다.

### 3) html 추가

"template"안에 "bmi"안에 아래 내용의 "bmi_update.html"을 추가


```html
{% raw %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>BMI Update</title>
</head>
<body>
<form action="" method = "post">
    {{form.as_p}}
    {% csrf_token %} # csrf 공격을 막기 위한 토큰을 발급
    <input type="submit" value="수정완료">
</form>
</body>
</html>
{% endraw %}
```

이러고 실행을 해보면 오류가 뜨는데, 이는 수정 후 보여줄 페이지가 없기 때문입니다.

따라서 views.py를 아래와 같이 수정합니다.


```python
{% raw %}
from django.views.generic.edit import UpdateView
class BMIUpdate(UpdateView) :
    model = BMI
    fields = ['name', 'height', 'weight'] # name, height, weight 만 입력받음을 알려줌
    template_name = 'bmi/bmi_update.html'
    success_url = '/' # 완료되면 메인 페이지로 돌아감
{% endraw %}
```

### 4) 메인 홈페이지 버튼과 연결


```html
{% raw %}
<!DOCTYPE html>
<html lang="en" xmlns="http://www.w3.org/1999/html">
<head>
    <meta charset="UTF-8">
    <title>BMI List</title>
</head>
<body>
<table border="1">
    <thead>
        <tr>
            <th>이름</th>
            <th>신장</th>
            <th>체중</th>
            <th>BMI</th>
            <th>수정</th>
            <th>삭제</th>
        </tr>
    </thead>
{% for object in object_list %}
    <tr>
        <td> {{object.name}}</td> <td> {{object.height}}</td> <td>
        {{object.weight}}</td>  <td> {{object.bmi}}</td>
        <td><a href="{% url 'bmi_update' object.id %}">수정</a></td><td>삭제</td>
    </tr>
{% endfor %}
</table>
</body>
</html>
{% endraw %}
```

위와 같이 bmi_update와 수정을 연결해주시면, 이제 수정하기가 가능해집니다!

위와 같은 과정을 반복하여 생성, 삭제를 추가할 수 있습니다.

### - 생성 기능 추가하기

### 1) bmi_list.html의 body 밑에 추가


```python
{% raw %}
<a href="{% url 'bmi_create' %}">추가</a>
{% endraw %}
```

### 2) views.py에 아래 추가


```python
{% raw %}
from django.views.generic.edit import CreateView
class BMICreate(CreateView) :
    model = BMI
    fields = ['name', 'height', 'weight'] # name, height, weight 만 입력받음을 알려줌
    template_name = 'bmi/bmi_create.html'
    success_url = '/'
{% endraw %}
```

### 3) urls.py의 urlpatterns 안에 아래 추가


```python
{% raw %}
# 물론 urlpatterns 밖에 import 필수(from .views import BMICreate) 
path('create', BMICreate.as_view(), name = 'bmi_create')
{% endraw %}
```

create는 현재 글번호가 필요 없으므로 int:pk가 필요없습니다!

### 4) templates의 bmi 폴더 안에 bmi_create.html 생성


```html
{% raw %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>BMI Create</title>
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

### - 삭제 기능 추가하기

### 1) bmi_list.html 수정


```python
{% raw %}
<td><a href="{% url 'bmi_delete' object.id %}">삭제</a></td>
{% endraw %}
```

### 2) views.py에 아래 추가


```python
{% raw %}
from django.views.generic.edit import DeleteView
class BMIDelete(DeleteView):
    model = BMI
    template_name = 'bmi/bmi_delete.html'
    success_url = '/'
{% endraw %}
```

### 3) urls.py의 urlpatterns 안에 아래 추가


```python
{% raw %}
# 물론 urlpatterns 밖에 import 필수(from .views import BMIDelete) 
path('delete/<int:pk>/', BMIDelete.as_view(), name = 'bmi_delete'),
{% endraw %}
```

### 4) templates의 bmi 폴더 안에 bmi_delete.html 생성


```html
{% raw %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>BMI Delete</title>
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

실행한 후 삭제를 눌러보면 어디서 많이 본 문구가 등장합니다.

> Do you want to delete 타미의 BMI 수치?

바로 이전에 models.py에서 설정했던 \_\_str\_\_ 함수 때문입니다!


```python
{% raw %}
def __str__(self):
    return self.name + "의 BMI 수치" # 항상 문자 리턴
{% endraw %}
```

바로 이 함수가 사실 관리자 페이지에서 뿐만이 아니라, 홈페이지 내에서 {{object}}라는 것을 출력할 때 기본적으로 호출되는 것입니다.

이를 내용과 함께 바꾸고 싶다면 {{object.name}} 등을 통해 직접 불러오면 됩니다.

### - 상세정보 기능 추가하기

사실 보통 홈페이지에서는 리스트에서 어떤 아이템을 클릭했을 때, 아이템의 디테일한 정보를 보여주는 페이지로 넘어갑니다.

따라서 여기서도 그러한 디테일(detail) 페이지를 만들도록 하겠습니다.

### 1) bmi_list.html 수정


```python
{% raw %}
<td><a href="{% url 'bookmark_update' object.id %}">{{object.site_name}}</a></td>
{% endraw %}
```

### 2) views.py에 아래 추가


```python
{% raw %}
from django.views.generic.detail import DetailView
class BMIDetail(DetailView):
    model = BMI
    template_name = 'bmi/bmi_detail.html'
{% endraw %}
```

### 3) urls.py의 urlpatterns 안에 아래 추가


```python
{% raw %}
# 물론 urlpatterns 밖에 import 필수(from .views import BMIDetail) 
path('detail/<int:pk>/', BMIDetail.as_view(), name = 'bmi_detail'),
{% endraw %}
```

### 4) templates의 bmi 폴더 안에 bmi_detail.html 생성


```html
{% raw %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>BMI Detail</title>
</head>
<body>
{{object.name}}<br>
{{object.height}}<br>
{{object.weight}}<br>
{{object.bmi}}
</body>
</html>
{% endraw %}
```

만약 이전에 만들었던 수정(Update)과 생성(Create)이 완료된 후 Detail 페이지로 이동 시키고 싶으면 어떻게 할까요?

우선 views.py의 success_url을 지워줍니다.


```python
{% raw %}
from django.views.generic.edit import UpdateView
class BMIUpdate(UpdateView) :
    model = BMI
    fields = ['name', 'height', 'weight'] # name, height, weight 만 입력받음을 알려줌
    template_name = 'bmi/bmi_update.html'

from django.views.generic.edit import CreateView
class BMICreate(CreateView) :
    model = BMI
    fields = ['name', 'height', 'weight'] # name, height, weight 만 입력받음을 알려줌
    template_name = 'bmi/bmi_create.html'
{% endraw %}
```

Django에서는 views.py에 success_url이 없을 경우 models.py를 참고하게 됩니다.

따라서 이 models.py에서 success_url이 없을 경우의 default 페이지를 설정할 수 있습니다.


```python
{% raw %}
from django.urls import reverse_lazy

class BMI(models.Model) :
    name = models.CharField(max_length=20) # 길이 최대 20의 문자
    height = models.FloatField() # 소수 숫자
    weight = models.FloatField()
    bmi = models.FloatField(blank=True, null=True) # 필수 필드가 아닌 값, NULL 갑도 가능함.

    # 저장할 때 일어나는 일을 조종하고 싶을 때
    def save(self, force_insert=False, force_update=False, using=None,
             update_fields=None):

        self.bmi = self.weight / ((self.height/100) ** 2)# bmi 초기화
        super(BMI, self).save(force_insert, force_update, using, update_fields) # 부모의 save를 호출하여 실제 세이브
        
    # magic method / 던더 메소드
    # 우리가 만든 것을 출력할 때 어떻게 출력할 것인가?
    def __str__(self):
        return self.name + "의 BMI 수치" # 항상 문자 리턴

    # success_url이 없을 시 아래 함수 실행
    def get_absolute_url(self):
        return reverse_lazy('bmi_detail', args = [self.id])
{% endraw %}
```

이제 페이지가 완료되었습니다!
