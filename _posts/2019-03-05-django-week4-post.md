---
layout: post
title: Django Week 4 - 웹사이트 배포하기 1
tags: [Django]
image:
  path: /images/abstract-1.jpg
  feature: abstract-1.jpg
comments: true
---

이번 문서에서는 Django를 통해 만든 북마크 홈페이지를 Python Anywhere와 Github을 활용하여 배포해겠습니다.

- - -

**"PYTHON & DJANGO를 활용한 웹 서비스 개발 CAMP"**을 수강하며 들은 내용을 정리 및 각색한 것입니다.

본 문서는 **python=3.6.2 & django=2.1.5** 기준으로 작성되었습니다.

- - -

## 1. Github으로 배포 준비하기

### 1) settings.py 수정

우선 배포하기 위해서는 config > settings.py에 DEBUG = True를 False로 바꿔주어야합니다.

또한 ALLOWED_HOSTS 역시 비워두면 안됩니다.

일단은 '\*'로 채워 넣을 것이지만, 실제로 할 때는 도메인 이름을 넣어줘야합니다.

### 2) requirements 생성

온라인에서의 배포를 위해서는, 서버에 필요한 패키지를 설치하여야하기 때문에 아래와 같이 requirements를 만들어줘야합니다.

> 터미널 명령 : pip freeze > requirements.txt

### 3) .gitignore 폴더 생성

python anywhere에 바로 올릴 수도 있지만,

이번에는 github을 활용하여 올려보도록 하겠습니다.

이를 위해 일단 최상단 프로젝트 폴더에 .gitignore를 만들어줍니다.

여기서는 git에 올리지 않을 파일을 설정을 합니다.


```python
{% raw %}
*.pyc
*~
__pycache__
/venv
db.sqlite3
{% endraw %}
```

git 초기화

> 터미널 명령 : git init 

현재 git의 상태

> 터미널 명령 : git status

모두 git에 올리겠다!

> 터미널 명령 : git add --all .

이번에 내가 올린 부분은 "First Commit"이다.

> 터미널 명령 : git commit -m "First Commit"

혹시 여기서 에러가 난다면, EMAIL 및 ID를 등록하지 않은 것 때문이니 에러 창에 있는 명령어를 입력하면 됩니다.

> 터미널 명령 : git config --global user.name username

> 터미널 명령 : git config --global user.email you@example.com

이제 깃헙에 프로젝트(bookmark)를 만들고 아래를 실행합니다.

> 터미널 명령 : git remote add origin https://github.com/"깃헙아이디"/bookmark.git

> 터미널 명령 : git push -u origin master

## 2. Python Anywhere로 배포하기

### 1) 서버 세팅하기

파이썬 애니웨어로 이동하여 bash 버튼 클릭하면 리눅스 서버를 제어 가능합니다.

이제 git의 프로젝트 url(https://github.com/"깃헙아이디"/bookmark.git)을 복사하여 위 bash 창에 아래와 같이 붙여넣습니다.

> 터미널 명령 : git clone https://github.com/"깃헙아이디"/bookmark.git

이제 bookmark 폴더 안으로 들어가서 가상환경 설정을 해줘야합니다.

> 터미널 명령 : cd bookmark

> 터미널 명령 : virtualenv --python=python3.6 venv

가상환경 설정이 끝나면 활성화도 해주어야합니다.

> 터미널 명령 : source venv/bin/activate

이제 아까 만들었던 requirement를 활용하여, 가상환경 내에 패키지 설치합니다.

> 터미널 명령 : pip install -r requirements.txt

가상환경에서도 DB를 만들어주어야겠죠?

> 터미널 명령 : python manage.py migrate

> 터미널 명령 : python manage.py createsuperuser

### 2) 웹 어플리케이션 서버 설정하기

* **여기서 잠깐!**
기존 runserver는 가상의 서버였기 때문에, uwsgi, gunicorm 같은 wsgi 관련 모듈을 쓰게되는데 이들이 웹 어플리케이션 서버라고 불리는 것들입니다. 실제 서버에서 구성이 되려면 apache, nginx를 모두 연결해주어야합니다. **하지만 python anywhere는 해당 과정을 인터페이스로 모두 구현해놓아서 편리**하게 설정할 수 있습니다.

python anywhere에서 상단바에 있는 Web으로 들어갑니다.

"add a new web app"을 누르면 domain name을 자동으로 배정받게 됩니다.

이제 설정을 위해 manual configuration을 클릭합니다.

python 버전 선택 후 next를 누르다보면 서버 세팅이 끝나게 되어있습니다.

이제 **Configuration for ~.pythonanywhere.com**으로 이동이 되었을 겁니다.

차례차례 아래에 있는 설정들을 바꿔주도록 하겠습니다.

* Code :

  * Source Code : /home/"깃헙아이디"/bookmark

  * Working directory : /home/"깃헙아이디"
  
* Virtualenv : /home/"깃헙아이디"/bookmark/venv

이제 /var/www/harrykim_pythonanywhere_com_wsgi.py를 수정해야합니다.

아래를 복사하여 붙여넣기하면 됩니다.

물론 path는 알맞게 바꿔줘야합니다.


```python
{% raw %}
"""
WSGI config for config project.

It exposes the WSGI callable as a module-level variable named ``application``.

For more information on this file, see
https://docs.djangoproject.com/en/2.1/howto/deployment/wsgi/
"""

import os
import sys
path = "/home/'깃헙아이디'/bookmark/"
if path not in sys.path :
    sys.path.append(path)
    
from django.contrib.staticfiles.handlers import StaticFilesHandler

from django.core.wsgi import get_wsgi_application

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'config.settings')

application = StaticFilesHandler(get_wsgi_application()) # StaticFilesHandler : middleware
{% endraw %}
```

### 3) 관리자 사이트 사용하기

근데 지금 관리자 사이트(/admin)으로 접속하면 관리자 패이지가 깨져보일 것입니다.

python anywhere에서 상단바에 있는 Files로 들어갑니다.

bookmark > config > settings.py 맨 밑에 STATIC 관련 정보를 추가


```python
{% raw %}
STATIC_ROOT = os.path.join(BASE_DIR, 'static_files')
{% endraw %}
```

이제 다시 Console로 돌아가, 가상환경 실행 시킨 상태(source venv/bin/activate)에서

python manage.py collectstatic을 콘솔에 입력합니다.

* Static files :

  * URL : /static/

  * Directory : home/HarryKim/bookmark/static_files

이때 '/static/' 이라는 가상폴더(실제로는 존재하지 않는 폴더)명이 바로 어떤 프로그램으로 사이트가 구성되어 있는지 모르도록 하기 위한 것입니다.

이제 해당 사이트로 접속하면 내 컴퓨터에서만 됐었던 홈페이지가, 다른 컴퓨터에서도 보이게 됩니다!
