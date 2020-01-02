---
layout: post
title: Django Week 6 - 인스타그램 모방하기
tags: [Django]
image:
  path: /images/abstract-1.jpg
  feature: abstract-1.jpg
comments: true
---

이번 문서에서는 Django를 활용하여 현재 가장 인기있는 SNS인 인스타그램을 모방해보도록 하겠습니다.

- - -

**"PYTHON & DJANGO를 활용한 웹 서비스 개발 CAMP"**을 수강하며 들은 내용을 정리 및 각색한 것입니다.

본 문서는 **python=3.6.2 & django=2.1.5** 기준으로 작성되었습니다.

- - -

## 1. Django 세팅

여태까지 해왔던 것처럼 파이참에서 프로젝트를 생성한 뒤 아래 명령을 실행합니다.

> 터미널 명령 : pip install django

> 터미널 명령 : django-admin startproject config .

> 터미널 명령 : python manage.py migrate

> 터미널 명령 : python manage.py createsuperuser

> 터미널 명령 : python manage.py startapp photo

## 2. 인스타그램 기본 틀 구성하기

### 1) 데이터베이스 구축하기

#### config/models.py 수정


```python
{% raw %}
from django.db import models

# 1. 함수를 이용해서 찾는 방법 :
from django.contrib.auth import get_user_model # 가장 보편적인 방법 (수정 쉬움)
# 2. User 모델을 불러와서 사용하는 방법 :
from django.contrib.auth.models import User
# 3. 셋팅 파일에 있는 설정을 불러와서 사용하는 방법 : 'auth.user'

class Photo(models.Model) :
    # Foreign Key란 다른 데이터베이스의 Key를 상속받는 것을 의미함.
    # USER MODEL에서 알아서 Foreign Key를 가져오고, 회원이 탈퇴하면 전부 삭제 (단, 선행관계가 다른 PROTECTED 참고 바람)
    writer = models.ForeignKey(get_user_model(), on_delete=models.CASCADE)
    text = models.TextField(blank=True)
    # 이미지를 저장할 때 images안에 년 월 일로 저장 (폴더 별로 파일의 개수가 제한적이기 때문)
    image = models.ImageField(upload_to='images/%Y/%m/%d')
    created = models.DateTimeField(auto_now_add=True) # 데이터 등록될 때 자동으로 기록
    updated = models.DateTimeField(auto_now=True) # 데이터가 갱신(등록 포함)때 자동으로 기록

    class Meta:
        ordering = ['-updated'] #update 기준으로 내림차순 정렬
{% endraw %}
```

#### photo/admin.py 수정


```python
{% raw %}
from .models import Photo
admin.site.register(Photo)
{% endraw %}
```

python manage.py runserver를 통해 제대로 DB가 구축되었는지 admin 페이지를 통해 확인해봅시다!

### 2) 이미지 업로드 구현하기

아직까지는 이미지에 접근이 불가능한데, 이는 미디어 폴더가 없기 때문입니다.

#### config/settings.py 수정


```python
{% raw %}
STATIC_URL = '/static/'
# 가상 URL : (인터넷 주소 창에 뜰 가짜 URL)
MEDIA_URL = '/media/'
# 실제 URL : (실제 이미지가 저장되는 주소)
MEDIA_ROOT = os.path.join(BASE_DIR, 'files')
{% endraw %}
```

이제 다시 서버를 켜서 이미지를 업로드하면 files라는 새로 생긴 폴더에 미디어가 저장되는 것을 확인할 수 있습니다.

이제 관리자 페이지를 조금 더 디테일하게 수정해봅시다.


```python
{% raw %}
from .models import Photo
class PhotoAdmin(admin.ModelAdmin):
    list_display = ['id', 'writer', 'image', 'created', 'updated'] # admin 페이지 리스트 뷰 수정
    list_filter = ['writer','created','updated'] # admin 페이지에 filter 추가
    search_fields = ['text','image'] # foreign key는 icontain으로 검색 불가하므로 writer를 제외한 나머지 가능
    raw_id_fields = ['writer'] # ADD PHOTO 시, 검색 버튼을 통해 writer 지정 (보통 foreign key에 작성)
    ordering = ['-updated', '-created'] # 관리자 페이지에서만 정렬하여 출력 (- : 내림차순)

admin.site.register(Photo, PhotoAdmin)
{% endraw %}
```

### 3) 사용자를 위한 CRUDL 추가

#### templates/base.html 생성

저번 시간에 다루었던 Bootstrap을 사용해봅시다.


```html
{% raw %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.2.1/css/bootstrap.min.css" integrity="sha384-GJzZqFGwb1QTTN6wy59ffF1BuGJpLSa9DkKMp0DgiMDm4iYMj70gZWKYbI706tWS" crossorigin="anonymous">
    <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.6/umd/popper.min.js" integrity="sha384-wHAiFfRlMFy6i5SRaxvfOCifBUQy1xHdJ/yoi7FRNXMRBu5WHdZYu1hA6ZOblgut" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.2.1/js/bootstrap.min.js" integrity="sha384-B0UglyR+jN6CkvvICOB2joaf5I4l3gm9GU6Hc1og6Ls7i6U/mkkaduKaBhlAXv9k" crossorigin="anonymous"></script>
</head>
<body>

</body>
</html>
{% endraw %}
```

이제 body에 container div를 만들고 안에 원하는 것을 집어넣도록 합니다.

아래는 Bootstrap의 여러 요소들 중 navbar을 추가한 것입니다.


```python
{% raw %}
<div class="container">
    <nav class="navbar navbar-expand-lg navbar-light bg-light">
        <a class="navbar-brand" href="#">Navbar w/ text</a>
        <div class="collapse navbar-collapse" id="navbarText">
            <ul class="navbar-nav mr-auto">

                {% if user.is_authenticated %}
                <li class="nav-item">
                    <a class="nav-link" href="#">Upload</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="#">Logout</a>
                </li>

                {% else %}
                <li class="nav-item">
                    <a class="nav-link" href="#">Register</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="#">Login</a>
                </li>
                {% endif %}

            </ul>

            <span class="navbar-text">
          Navbar text with an inline element
        </span>
        </div>
    </nav>
</div>
{% endraw %}
```

위의 user.is_authenticated의 경우 유저가 로그인했을 때와 안했을 때에 다르게 보여주기 위함입니다.

#### config/settings.py 수정


```python
{% raw %}
'DIRS': [os.path.join(BASE_DIR, 'templates')],
{% endraw %}
```

#### photo/views.py 생성


```python
{% raw %}
from django.views.generic.list import ListView
from django.views.generic.edit import CreateView, UpdateView, DeleteView
from django.views.generic.detail import DetailView
from .mmodels import Photo

class PhotoList(ListView) :
    model = Photo
    template_name = 'photo/list.html'

class PhotoUpdate(UpdateView):
    model = Photo
    fields = ['text', 'image']
    template_name = 'photo/update.html'

class PhotoDelete(DeleteView):
    model = Photo
    template_name = 'photo/delete.html'
    success_url = '/'

class PhotoCreate(CreateView):
    model = Photo
    fields = ['text', 'image']
    template_name = 'photo/create.html'

class PhotoDetail(DetailView):
    model = Photo
    template_name = 'photo/detail.html'
{% endraw %}
```

그리고 각각에 해당하는 html을 photo/templates/photo 안에 만들어줍니다.

단 추후에 수정을 위해 일괄적으로 아래와 같이 만들어주도록 합니다.


```python
{% raw %}
{% extends 'base.html' %}

{% block title%}
{% endblock%}

{% block content%}
{% endblock%}
{% endraw %}
```

#### photo/urls.py 생성


```python
{% raw %}
from django.urls import path

from .views import *

app_name  = 'photo'
urlpatterns = [
    path('',PhotoList.as_view(), name='index'),
    path('create/', PhotoCreate.as_view(), name='photo_upload'),
    path('update/<int:pk>/', PhotoUpdate.as_view(), name='photo_update'),
    path('delete/<int:pk>/', PhotoDelete.as_view(), name='photo_delete'),
    path('detail/<int:pk>/', PhotoDetail.as_view(), name='photo_detail'),
]
{% endraw %}
```

#### config/urls.py 수정


```python
{% raw %}
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('photo.urls', namespace='photo'))
]

{% endraw %}
```

위의 app_name의 경우 namespace을 활용해보았습니다.

이로 인한 편리함은 아래에서 확인 가능합니다.

#### photo/models.py 수정


```python
{% raw %}
from django.urls import reverse_lazy
class Photo(models.Model) :
    # USER MODEL에서 알아서 Foreign Key를 가져오고, 회원이 탈퇴하면 전부 삭제 (단, 선행관계가 다른 PROTECTED 참고 바람)
    writer = models.ForeignKey(get_user_model(), on_delete=models.CASCADE)
    text = models.TextField(blank=True)
    # 이미지를 저장할 때 images안에 년 월 일로 저장 (폴더 별로 파일의 개수가 제한적이기 때문)
    image = models.ImageField(upload_to='images/%Y/%m/%d')
    created = models.DateTimeField(auto_now_add=True) # 데이터 등록될 때 자동으로 기록
    updated = models.DateTimeField(auto_now=True) # 데이터가 갱신(등록 포함)때 자동으로 기록

    class Meta:
        ordering = ['-updated'] #update 기준으로 내림차순 정렬

    def get_absolute_url(self):
        return reverse_lazy('photo:photo_detail', args=[self.id])
{% endraw %}
```

models.py의 get_absolute_url를 보시면 여태까지와 다르게

'photo:photo_detail'과 같이 html이 아니라 photo:으로 표현되어있는 것을 확인 할 수 있습니다.

#### templates/base.html 수정

이제 더 디테일한 수정을 위해 base.html을 다시 수정하여 봅시다.


```html
{% raw %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Dstagram {% block title %}{% endblock %}</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.2.1/css/bootstrap.min.css" integrity="sha384-GJzZqFGwb1QTTN6wy59ffF1BuGJpLSa9DkKMp0DgiMDm4iYMj70gZWKYbI706tWS" crossorigin="anonymous">
    <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.6/umd/popper.min.js" integrity="sha384-wHAiFfRlMFy6i5SRaxvfOCifBUQy1xHdJ/yoi7FRNXMRBu5WHdZYu1hA6ZOblgut" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.2.1/js/bootstrap.min.js" integrity="sha384-B0UglyR+jN6CkvvICOB2joaf5I4l3gm9GU6Hc1og6Ls7i6U/mkkaduKaBhlAXv9k" crossorigin="anonymous"></script>
</head>
<body>

<div class="container">
    <nav class="navbar navbar-expand-lg navbar-light bg-light">
        <a class="navbar-brand" href="#">Distargram</a>
        <div class="collapse navbar-collapse" id="navbarText">
            <ul class="navbar-nav mr-auto">

                {% if user.is_authenticated %}
                <li class="nav-item">
                    <a class="nav-link" href="{% url 'photo:photo_upload' %}">Upload</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="#">Logout</a>
                </li>

                {% else %}
                <li class="nav-item">
                    <a class="nav-link" href="#">Register</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="#">Login</a>
                </li>
                {% endif %}

            </ul>

            <span class="navbar-text">
          Navbar text with an inline element
        </span>
        </div>
    </nav>
</div>

{% block content %}
{% endblock %}

</body>
</html>
{% endraw %}
```

#### photo/templates/photo/list.html 수정


```python
{% raw %}
{% extends 'base.html' %}

{% block title%}
- Photo List
{% endblock%}

{% block content%}
<div class="container">
    <div class="row">
        <div class="col-3">

        </div>
        <div class="col-6">
            {% for object in object_list %}

            <br>
            <div class="card" >
                <img src="{{object.image.url}}" class="card-img-top" alt="...">
                <div class="card-body">
                    <h5 class="card-title">{{object.writer.username}}</h5>
                    <p class="card-text">{{object.text}}<br>
                    {{object.updated}}</p>
                    <a href="#" class="btn btn-primary">댓글 달기</a>
                </div>
            </div>
            {% endfor %}

        </div>
        <div class="col-3">

        </div>
    </div>
</div>
{% endblock%}

{% endraw %}
```

하지만 지금 runserver를 통해 이미지를 업로드하고 보면,

기대했던 바와 달리 이미지가 안뜨게 됩니다...

왜냐하면! 지금은 STATIC과 실제 폴더가 연결되어 있지 않기 때문입니다.

#### config/urls.py 수정


```python
{% raw %}
from django.conf.urls.static import static
from django.conf import settings

urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
{% endraw %}
```

그럼 이제 이미지가 뜨게됩니다!

이제 나머지 html도 차근차근 수정해봅시다.

#### photo/templates/photo/create.html


```python
{% raw %}
{% extends 'base.html' %}

{% block title%}
{% endblock%}

{% block content%}
<div class="container">
    <div class="row">
        <div class="col-3">

        </div>
        <div class="col-6">
           <form action="" method="post" enctype="multipart/form-data">
               {{form.as_p}}
               {% csrf_token %}
               <input type="submit" value="Upload" class="btn btn-success">
           </form>
        </div>
        <div class="col-3">

        </div>
    </div>
</div>
{% endblock%}

{% endraw %}
```

파일을 업로드할 때는  enctype="multipart/form-data"이 필수이므로 잘 추가해줍니다!

그런데.. 지금 runserver를 해보면 유저 아이디가 없어서 에러가 발생합니다.

이는 views.py를 수정하여 해결할 수 있습니다.

#### photo/views.py 수정


```python
{% raw %}
from django.shortcuts import redirect
from django.urls import reverse

class PhotoCreate(CreateView):
    model = Photo
    fields = ['text', 'image']
    template_name = 'photo/create.html'

    def form_valid(self, form):
        form.instance.writer_id = self.request.user.id
        if form.is_valid() : # require 한 필드가 모두 알맞게 채워져있으면
            form.instance.save() # 인스턴스를 저장한 뒤에
            return redirect(reverse('photo:photo_detail', args = [form.instance.id])) # 인스턴스를 전달한다
        else :
            return self.render_to_response({'form':form}) # 빠진 곳만 입력하면 되도록
{% endraw %}
```

이제는 아마 업로드가 잘 될 것입니다.

허나, 로그인을 한 사람들에게만 실행되어야하므로, 아래와 같이 변경해줍니다.

#### photo/views.py 수정


```python
{% raw %}
from django.contrib.auth.mixins import LoginRequiredMixin

class PhotoCreate(LoginRequiredMixin, CreateView): ... # 위는 무조건 앞에 상속하여 미리 체크하도록 해야한다.
{% endraw %}
```

최종적인 views.py는 아래와 같습니다.


```python
{% raw %}
from django.shortcuts import render

# Create your views here.

from django.views.generic.list import ListView
from django.views.generic.edit import CreateView, UpdateView, DeleteView
from django.views.generic.detail import DetailView
from .models import Photo
from django.shortcuts import redirect
from django.urls import reverse

from django.contrib.auth.mixins import LoginRequiredMixin

class PhotoList(ListView) :
    model = Photo
    template_name = 'photo/list.html'

class PhotoUpdate(LoginRequiredMixin, UpdateView):
    model = Photo
    fields = ['text', 'image']
    template_name = 'photo/update.html'

class PhotoDelete(LoginRequiredMixin, DeleteView):
    model = Photo
    template_name = 'photo/delete.html'
    success_url = '/'

class PhotoCreate(LoginRequiredMixin, CreateView):
    model = Photo
    fields = ['text', 'image']
    template_name = 'photo/create.html'

    def form_valid(self, form):
        form.instance.writer_id = self.request.user.id
        if form.is_valid() : # require 한 필드가 모두 알맞게 채워져있으면
            form.instance.save() # 인스턴스를 저장한 뒤에
            return redirect(reverse('photo:photo_detail', args = [form.instance.id])) # 인스턴스를 전달한다
        else :
            return self.render_to_response({'form':form}) # 빠진 곳만 입력하면 되도록


class PhotoDetail(DetailView):
    model = Photo
    template_name = 'photo/detail.html'
{% endraw %}
```

나머지 html도 마무리해줍시다.

#### photo/templates/photo/list.html


```python
{% raw %}
{% extends 'base.html' %}

{% block title%}
- Photo List
{% endblock%}

{% block content%}
<div class="container">
    <div class="row">
        <div class="col-3">

        </div>
        <div class="col-6">
            {% for object in object_list %}

            <br>
            <div class="card" >
                <img src="{{object.image.url}}" class="card-img-top" alt="...">
                <div class="card-body">
                    <h5 class="card-title">{{object.writer.username}}</h5>
                    <p class="card-text">{{object.text}}<br>
                    {{object.updated}}</p>
                    <a href="{% url 'photo:photo_detail' object.id %}" class="btn btn-primary btn-sm">댓글 달기</a>
                </div>
            </div>
            {% endfor %}

        </div>
        <div class="col-3">

        </div>
    </div>
</div>
{% endblock%}

{% endraw %}
```

#### photo/templates/photo/create.html


```python
{% raw %}
{% extends 'base.html' %}

{% block title%}
{% endblock%}

{% block content%}
<div class="container">
    <div class="row">
        <div class="col-3">

        </div>
        <div class="col-6">
           <form action="" method="post" enctype="multipart/form-data">
               {{form.as_p}}
               {% csrf_token %}
               <input type="submit" value="Upload" class="btn btn-success">
           </form>
        </div>
        <div class="col-3">

        </div>
    </div>
</div>
{% endblock%}

{% endraw %}
```

#### photo/templates/photo/detail.html


```python
{% raw %}
{% extends 'base.html' %}

{% block title%}
- Photo List
{% endblock%}

{% block content%}
<div class="container">
    <div class="row">
        <div class="col-3">
        </div>
        <div class="col-6">
            <br>
            <div class="card" >
                <img src="{{object.image.url}}" class="card-img-top" alt="...">
                <div class="card-body">
                    <h5 class="card-title">{{object.writer.username}}</h5>
                    <p class="card-text">{{object.text}}<br>
                    {{object.updated}}</p>
                    <a href="{% url 'photo:photo_delete' object.id %}" class="btn btn-sm btn-danger">Delete</a>
                    <a href="{% url 'photo:photo_update' object.id %}" class="btn btn-sm btn-warning">Update</a>
                </div>
            </div>
        </div>
        <div class="col-3">
        </div>
    </div>
</div>
{% endblock%}

{% endraw %}
```

#### photo/templates/photo/update.html


```python
{% raw %}
{% extends 'base.html' %}

{% block title%}
{% endblock%}

{% block content%}
<div class="container">
    <div class="row">
        <div class="col-3">

        </div>
        <div class="col-6">
           <form action="" method="post" enctype="multipart/form-data">
               {{form.as_p}}
               {% csrf_token %}
               <input type="submit" value="Update" class="btn btn-success">
           </form>
        </div>
        <div class="col-3">

        </div>
    </div>
</div>
{% endblock%}

{% endraw %}
```

#### photo/templates/photo/delete.html


```python
{% raw %}
{% extends 'base.html' %}

{% block title%}
{% endblock%}

{% block content%}
<form action="" method = "post">
    <div class = "alert alert-danger">Do you want to delete {{object}}?</div>
    {% csrf_token %}
    <input type="submit" value="Delete OK" class="btn btn-danger">
</form>
{% endblock%}

{% endraw %}
```

던더 메소드를 활용하여 delete 페이지에 나오는 이름을 수정해보도록 합시다.

#### photo/models.py 수정


```python
{% raw %}
from django.db import models

# 1. 함수를 이용해서 찾는 방법 :
from django.contrib.auth import get_user_model # 가장 보편적인 방법 (수정 쉬움)
# 2. User 모델을 불러와서 사용하는 방법 :
from django.contrib.auth.models import User
# 3. 셋팅 파일에 있는 설정을 불러와서 사용하는 방법 : 'auth.user'

from django.urls import reverse_lazy
class Photo(models.Model) :
    # USER MODEL에서 알아서 Foreign Key를 가져오고, 회원이 탈퇴하면 전부 삭제 (단, 선행관계가 다른 PROTECTED 참고 바람)
    writer = models.ForeignKey(get_user_model(), on_delete=models.CASCADE)
    text = models.TextField(blank=True)
    # 이미지를 저장할 때 images안에 년 월 일로 저장 (폴더 별로 파일의 개수가 제한적이기 때문)
    image = models.ImageField(upload_to='images/%Y/%m/%d')
    created = models.DateTimeField(auto_now_add=True) # 데이터 등록될 때 자동으로 기록
    updated = models.DateTimeField(auto_now=True) # 데이터가 갱신(등록 포함)때 자동으로 기록

    class Meta:
        ordering = ['-updated'] #update 기준으로 내림차순 정렬

    def get_absolute_url(self):
        return reverse_lazy('photo:photo_detail', args=[self.id])

    def __str__(self):
        return self.writer.username +' '+self.created.strftime("%Y-%m-%d %H:%M:%S")
{% endraw %}
```

## 3. 로그인 로그아웃 구현

이제 로그인과 로그아웃을 구현하기 위해 새로운 앱을 만들고 기능을 추가하겠습니다.

>python manage.py startapp accounts

#### accounts/urls.py 추가


```python
{% raw %}
from django.urls import path
from django.contrib.auth.views import LoginView, LogoutView

urlpatterns = [
    path('login/', LoginView.as_view(template_name='accounts/login.html'), name='login'),
    path('logout/', LogoutView.as_view(template_name='accounts/logout.html'), name='logout'),
]
{% endraw %}
```

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
    'photo',
    'accounts',
]
{% endraw %}
```

#### config/urls.py 수정


```python
{% raw %}
urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('photo.urls', namespace='photo')),
    path('accounts/', include('accounts.urls')),
]
{% endraw %}
```

#### accounts/templates/accounts/login.html 생성


```python
{% raw %}
{% extends 'base.html' %}

{% block title%}
{% endblock%}

{% block content%}

<div class="container">
    <div class="row">
        <div class="col-3">
        </div>
        <div class="col-6">
            <form action="" method = "post">
                <div class = "alert alert-info">Insert your login information</div>
                {{form.as_p}}
                {% csrf_token %}
                <input type="submit" value="Login" class="btn btn-success">
            </form>
        </div>
        <div class="col-3">
        </div>
    </div>
</div>

{% endblock%}
{% endraw %}
```

#### accounts/templates/accounts/logout.html 생성


```python
{% raw %}
{% extends 'base.html' %}

{% block title%}
{% endblock%}

{% block content%}

<div class="container">
    <div class="row">
        <div class="col-3">
        </div>
        <div class="col-6">
            <form action="" method = "post">
                <div class = "alert alert-info">Successfully logged out.</div>

                <a href="{% url 'login' %}" class = "btn btn-primary"> Move to Login </a>
            </form>
        </div>
        <div class="col-3">
        </div>
    </div>
</div>

{% endblock%}
{% endraw %}
```

#### templates/base.html 수정


```html
{% raw %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Dstagram {% block title %}{% endblock %}</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.2.1/css/bootstrap.min.css" integrity="sha384-GJzZqFGwb1QTTN6wy59ffF1BuGJpLSa9DkKMp0DgiMDm4iYMj70gZWKYbI706tWS" crossorigin="anonymous">
    <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.6/umd/popper.min.js" integrity="sha384-wHAiFfRlMFy6i5SRaxvfOCifBUQy1xHdJ/yoi7FRNXMRBu5WHdZYu1hA6ZOblgut" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.2.1/js/bootstrap.min.js" integrity="sha384-B0UglyR+jN6CkvvICOB2joaf5I4l3gm9GU6Hc1og6Ls7i6U/mkkaduKaBhlAXv9k" crossorigin="anonymous"></script>
</head>
<body>

<div class="container">
    <nav class="navbar navbar-expand-lg navbar-light bg-light">
        <a class="navbar-brand" href="/">Distargram</a>
        <div class="collapse navbar-collapse" id="navbarText">
            <ul class="navbar-nav mr-auto">

                {% if user.is_authenticated %}
                <li class="nav-item">
                    <a class="nav-link" href="{% url 'photo:photo_upload' %}">Upload</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="{% url 'logout' %}">Logout</a>
                </li>

                {% else %}
                <li class="nav-item">
                    <a class="nav-link" href="#">Register</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="{% url 'login' %}">Login</a>
                </li>
                {% endif %}

            </ul>

            <span class="navbar-text">
                {% if user.is_authenticated %}
                  Welcome, {{user.username}}
                {% else %}
                  Login?
                {% endif %}
        </span>
        </div>
    </nav>
</div>

{% block content %}
{% endblock %}

</body>
</html>
{% endraw %}
```

그러면 profile 주소에 해당하는 게 없다면서 에러가 뜨는데, 

이는 아래와 같이 profile 페이지를 따로 생성하지 않고 해결할 수 있습니다.

#### config/settings.py 수정


```python
{% raw %}
LOGIN_REDIRECT_URL = '/'
{% endraw %}
```

## 4. 회원가입 구현

이제 회원가입을 구현할 차례입니다.

이를 위해서 form을 활용해봅시다.

#### accounts/forms.py 생성


```python
{% raw %}
from django.contrib.auth import get_user_model
from django import forms
class RegisterForm(forms.ModelForm):
    
    # Meta로 설정 불가능한 요소 설정
    password = forms.CharField(label='Password', widget=forms.PasswordInput)
    password2 = forms.CharField(label='Repeat Password', widget=forms.PasswordInput)

    class Meta :
        model = get_user_model()
        fields = ('username', 'first_name', 'last_name', 'email')

    def clean_password2(self):
        cd = self.cleaned_data # 검증을 마친 데이터
        if cd['password'] != cd['password2'] :
            raise forms.ValidationError("Password doesn't match.")
        return cd['password2']
{% endraw %}
```

#### accounts/views.py 수정


```python
{% raw %}
from django.shortcuts import render
from .forms import *

def register(request) :
    if request.method == 'POST' : #(입력 완료 눌렀음)
        user_form = RegisterForm(request.POST) # 정보를 가져와서 RegisterForm에 입력
        if user_form.is_valid(): # 각 필드에 대한 검증 + clean_data
            # 회원 가입 폼 - 모델 폼을 상송받아서 만듦
            # 회원 가입 폼 - 설정된 모델 - 유저 모델
            # 회원 가입 폼.save - 설정된 모델인 유저 모델의 인스턴스가 만들어짐
            # save를 하는 순간, 인스턴스가 생성되고, 데이터베이스에 저장
            # commit=False는 데이터베이스에 저장되지 않도록함
            new_user = user_form.save(commit=False)
            new_user.set_password(user_form.cleaned_data['password']) # hash된 데이터가 저장됨
            new_user.save()
            return render(request, 'registration/register_ok.html', {'new_user':new_user})

    else : # 일반적으로 POST가 아닌 경우면 GET (입력하려고 들어가는 중)
        user_form = RegisterForm()
    return render(request, 'registration/register.html', {'form':user_form})
{% endraw %}
```

#### accounts/urls.py 수정


```python
{% raw %}
from django.urls import path
from django.contrib.auth.views import LoginView, LogoutView
from .views import register

urlpatterns = [
    path('login/', LoginView.as_view(template_name='accounts/login.html'), name='login'),
    path('logout/', LogoutView.as_view(template_name='accounts/logout.html'), name='logout'),
    path('register/', register, name='register'),
]
{% endraw %}
```

#### templates/base.html 수정


```python
{% raw %}
<li class="nav-item">
    <a class="nav-link" href="{% url 'register' %}">Register</a>
</li>
{% endraw %}
```

#### accounts/templates/registration/register.html 생성


```python
{% raw %}
{% extends 'base.html' %}

{% block title%}
{% endblock%}

{% block content%}

<div class="container">
    <div class="row">
        <div class="col-3">
        </div>
        <div class="col-6">
            <form action="" method = "post">
                <div class = "alert alert-info">Insert your account information</div>
                {{form.as_p}}
                {% csrf_token %}
                <input type="submit" value="Register" class="btn btn-success">
            </form>
        </div>
        <div class="col-3">
        </div>
    </div>
</div>

{% endblock%}
{% endraw %}
```

#### accounts/templates/registration/register_ok.html 생성


```python
{% raw %}
{% extends 'base.html' %}

{% block title%}
{% endblock%}

{% block content%}

<div class="container">
    <div class="row">
        <div class="col-3">
        </div>
        <div class="col-6">
            <p>OK!</p>
        </div>
        <div class="col-3">
        </div>
    </div>
</div>

{% endblock%}
{% endraw %}
```

## 5. 댓글달기 구현

원래 댓글달기 할 때는 페이지가 새로고침 되지 않습니다.

이것은 아쉽게도 Django뿐만으로는 버겁고,

Model, Form, Template을 바탕으로 Ajax와 JS를 구동하여 가능합니다.

따라서 이를 쉽게 구현해주는 [DISQUS](https://disqus.com)를 이용해봅시다.

[https://disqus.com/profile/signup/intent/](https://disqus.com/profile/signup/intent/)에 접속합니다.

이제 이름(이 이름은 기억!) 설정하고, 맨끝을 누루고 configure, complete setup을 누릅니다.

본 문서에서 이름은 "sample-dstargram"으로 지정했습니다.

이제 터미널에 아래 명령어를 입력합시다.

> pip install django-disqus

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
    'photo',
    'accounts',
    'disqus',
    'django.contrib.sites',
]

{% endraw %}
```


```python
{% raw %}
DISQUS_WEBSITE_SHORTNAME = 'sample-dstargram' # 아까 disqus 사이트에서 설정한 이름
SITE_ID = 1
{% endraw %}
```

> python manage.py migrate

#### photo/templates/photo/detail.html 수정


```python
{% raw %}
{% extends 'base.html' %}

{% block title%}
- Photo List
{% endblock%}

{% block content%}
<div class="container">
    <div class="row">
        <div class="col-3">
        </div>
        <div class="col-6">
            <br>
            <div class="card" >
                <img src="{{object.image.url}}" class="card-img-top" alt="...">
                <div class="card-body">
                    <h5 class="card-title">{{object.writer.username}}</h5>
                    <p class="card-text">{{object.text}}<br>
                    {{object.updated}}</p>
                    <a href="{% url 'photo:photo_delete' object.id %}" class="btn btn-sm btn-danger">Delete</a>
                    <a href="{% url 'photo:photo_update' object.id %}" class="btn btn-sm btn-warning">Update</a>
                </div>
            </div>
        </div>
        <div class="col-3">
        </div>
    </div>

추가된 부분
    <div class="row">
        <div class="col-3">
        </div>
        <div class="col-6">
            {% load disqus_tags %}
            {% disqus_show_comments %}
        </div>
        <div class="col-3">
        </div>
    </div>
</div>
{% endblock%}

{% endraw %}
```

이제 댓글 달기도 제대로 작동되는 것을 확인할 수 있습니다.

## 6. Heroku를 통해 배포하기

이제는 본 홈페이지를 다른 곳에서도 접속할 수 있도록 배포를 해보겠습니다.

이를 위해 Heroku라는 서비스를 이용할 것이며 회원가입을 해야합니다.

[https://heroku.com](https://heroku.com)

회원 가입이 다 되었다면 아래 사이트로 이동하여 배포 방법을 살펴봅니다.

https://devcenter.heroku.com/articles/deploying-python

어느 정도 숙지가 되었다면 이제 같이 배포해볼까요?

터미널에 dj-database-url, gunicorn, whitenoise, psycopg2-binary를 install해줍니다.

> pip install dj-database-url gunicorn whitenoise psycopg2-binary

#### config/settings.py 수정


```python
{% raw %}
import dj_database_url

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}

DATABASES['default'].update(dj_database_url.config(conn_max_age=500))
{% endraw %}
```


```python
{% raw %}
# SECURITY WARNING: don't run with debug turned on in production!
DEBUG = False

ALLOWED_HOSTS = ['*']
{% endraw %}
```


```python
{% raw %}
STATIC_ROOT = os.path.join(BASE_DIR, 'static')
{% endraw %}
```

#### 그 후 프로젝트 안에 static 폴더를 만들어 줍니다.

그 다음 이 폴더를 사용할 수 있도록 아래와 같이 settings.py를 수정합니다.

#### config/settings.py 수정


```python
{% raw %}
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
]

{% endraw %}
```

이제 얼마 남지 않았습니다!

> pip freeze > requirements.txt

#### 프로젝트 폴더 안에 Procfile 생성 (확장자 없음)


```python
{% raw %}
web: gunicorn config.wsgi
{% endraw %}
```

#### 프로젝트 폴더 안에 runtime.txt 생성


```python
{% raw %}
python-3.6.0
{% endraw %}
```

이제 git을 통해 배포를 진행해볼까요?

> git init

> git add -A .

> git commit -m "First Heroku"

> heroku login

이제 웹 브라우저가 뜨고 로그인을 한 후 아래를 마저 입력해줍시다.

> heroku create sample-dstagram

위의 sample-dstagram을 다시 기억해줍니다.

다시 터미널로 돌아가 아래를 입력해줍시다.

> git push heroku master

> heroku run python manage.py migrate

> heroku run python manage.py createsuperuser

> heroku run python manage.py runserver

이제 다됐습니다! [https://sample-dstagram.herokuapp.com](https://sample-dstagram.herokuapp.com) 으로 접속하면 내 사이트를 웹페이지에서 볼 수 있습니다!

## Bonus1. Tagging 기능 추가하기

> pip install django-tagging

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
    'photo',
    'accounts',
    'disqus',
    'django.contrib.sites',
    'tagging',
]
{% endraw %}
```

#### photo > models.py 수정


```python
{% raw %}
from tagging.fields import TagField

class Photo(models.Model) :
    # USER MODEL에서 알아서 Foreign Key를 가져오고, 회원이 탈퇴하면 전부 삭제 (단, 선행관계가 다른 PROTECTED 참고 바람)
    writer = models.ForeignKey(get_user_model(), on_delete=models.CASCADE)
    text = models.TextField(blank=True)
    tag = TagField()
    # 이미지를 저장할 때 images안에 년 월 일로 저장 (폴더 별로 파일의 개수가 제한적이기 때문)
    image = models.ImageField(upload_to='images/%Y/%m/%d')
    created = models.DateTimeField(auto_now_add=True) # 데이터 등록될 때 자동으로 기록
    updated = models.DateTimeField(auto_now=True) # 데이터가 갱신(등록 포함)때 자동으로 기록

{% endraw %}
```

> python manage.py makemigrations photo

> python manage.py migrate

#### photo/views.py 수정


```python
{% raw %}
class PhotoCreate(LoginRequiredMixin, CreateView):
    model = Photo
    fields = ['text', 'image', 'tag']
    template_name = 'photo/create.html'

    def form_valid(self, form):
        form.instance.writer_id = self.request.user.id
        if form.is_valid() : # require 한 필드가 모두 알맞게 채워져있으면
            form.instance.save() # 인스턴스를 저장한 뒤에
            return redirect(reverse('photo:photo_detail', args = [form.instance.id])) # 인스턴스를 전달한다
        else :
            return self.render_to_response({'form':form}) # 빠진 곳만 입력하면 되도록


class PhotoUpdate(LoginRequiredMixin, UpdateView):
    model = Photo
    fields = ['text', 'image', 'tag']
    template_name = 'photo/update.html'
{% endraw %}
```

#### photo/templates/photo/detail.html 수정


```python
{% raw %}
{% extends 'base.html' %}

{% block title%}
- Photo List
{% endblock%}

{% block content%}
<div class="container">
    <div class="row">
        <div class="col-3">
        </div>
        <div class="col-6">
            <br>
            <div class="card" >
                <img src="{{object.image.url}}" class="card-img-top" alt="...">
                <div class="card-body">
                    <h5 class="card-title">{{object.writer.username}}</h5>
                    <p class="card-text">{{object.text}}<br>
                    {{object.updated}}</p>
                    <a href="{% url 'photo:photo_delete' object.id %}" class="btn btn-sm btn-danger">Delete</a>
                    <a href="{% url 'photo:photo_update' object.id %}" class="btn btn-sm btn-warning">Update</a>
                </div>
            </div>
        </div>
        <div class="col-3">
        </div>
    </div>

     <div class="row">
        <div class="col-3">
        </div>
        <div class="col-6">
            {% load tagging_tags %}
            {% tags_for_object object as tags %}
            {% for tag in tags %}
            <a href="{% url 'photo:tag_post_list' tag.name %}">{{tag.name}}</a>
            {% endfor %}
        </div>
        <div class="col-3">
        </div>
    </div>

     <div class="row">
        <div class="col-3">
        </div>
        <div class="col-6">
            {% load disqus_tags %}
            {% disqus_show_comments %}
        </div>
        <div class="col-3">
        </div>
    </div>
</div>
{% endblock%}

{% endraw %}
```

#### photo/views.py 수정


```python
{% raw %}
from django.views.generic import TemplateView
from tagging.models import Tag, TaggedItem
from tagging.views import TaggedObjectList

class TagView(TemplateView):
    template_name = 'photo/tagging_list.html'
class TagPostList(TaggedObjectList):
    model = Photo
    template_name = 'photo/tagging_post_list.html'
{% endraw %}
```

#### photo/urls.py 수정


```python
{% raw %}
path('tag/', TagView.as_view(), name='tag_list'),
path('tag/<tag>/', TagPostList.as_view(), name='tag_post_list'),
{% endraw %}
```

## Bonus2. 삭제 권한 확인하기

내가 업로드한 이미지를 아무나 지울 수 있으면 안되겠죠?

하지만 지금은 아쉽게도 그런 상태입니다...

수정해봅시다!

#### photo/views.py 수정


```python
{% raw %}
from django.http import HttpResponseRedirect
from django.contrib import messages

class PhotoDelete(LoginRequiredMixin, DeleteView):
    model = Photo
    template_name = 'photo/delete.html'
    success_url = '/'

    # 선택된 아이템을 눌렀을 때 실행되는 메소드
    def get(self, request, *args, **kwargs):
        object = self.get_object()
        if object.writer != request.user:
            messages.warning(request,"You do not have permission for deleting this photo")
            return HttpResponseRedirect(object.get_absolute_url())
        else:
            return super(PhotoDelete, self).get(request, *args, **kwargs)
{% endraw %}
```

#### photo/templates/photo/detail.html 수정


```python
{% raw %}
<div>
    {% if messages %}
        {% for message in messages %}
            {{message}}
        {% endfor %}
    {% endif %}

</div>
{% endraw %}
```

#### photo/models.py 수정


```python
{% raw %}
def get_absolute_url(self):
    return reverse_lazy('photo:photo_detail', kwargs={'pk':self.id})
{% endraw %}
```

이 외에도 장고의 가디언이라는 앱 등 수많은 앱이 있으니 참고부탁드립니다 :)
