---
layout: post
title: 도커 휴지통 비우기(Docker Trashbin)
tags: [Docker]
image:
  path: /images/abstract-1.jpg
  feature: abstract-1.jpg
---

이번 글에서는 도커 환경에서 휴지통을 비워보도록하겠습니다.

우선 알아둘 것은 아래와 같습니다.

1. 우분투도 휴지통이 존재한다.

2. 도커와 공유된 폴더는 휴지통이 따로 존재한다.

3. 공유된 폴더의 휴지통은 삭제하면 우분투의 휴지통으로 이동한다.

즉, 따라서 도커와 공유된 폴더에서의 휴지통을 먼저 비워주고, 우분투의 휴지통을 비워주어야 합니다!

## 폴더 용량 확인

그 전에 우선 폴더 용량부터 확인해보겠습니다.

아래 명령어는 각 Disk의 사용용량과 남은 용량을 깔끔(-h)하게 확인할 수 있는 명령어입니다.

`In[1]:`
```ruby
{% raw %}df -h{% endraw %}
```

아래 명령어는 특정 폴더의 용량을 확인하는 것입니다.

`In[2]:`
```ruby
{% raw %}sudo du -h --max-depth=1 |sort -hr{% endraw %}
```

## 도커 휴지통 비우기

도커 휴지통은 공유된 폴더 내에 위치해있습니다.

예를 들어 공유된 폴더의 원래 위치가 "/home/user/workpsace/"라면

"/home/user/workpsace/.Trash-0"이 도커 휴지통이 되게 됩니다.

따라서 아래와 같이 입력하면 됩니다.

`In[3]:`
```ruby
{% raw %}cd /home/user/workpsace/
sudo rm -rf .Trash-0{% endraw %}
```

혹은 아래도 됩니다.

`In[4]:`
```ruby
{% raw %}sudo rm -rf ~/workspace/.Trash-0{% endraw %}
```

## 우분투 휴지통 비우기

이제 우분투 휴지통을 비우면 됩니다.

우분투 휴지통은 "~/.local/share/Trash/files/*"에 위치되어 있습니다.

따라서 아래와 같이 입력하면 됩니다.

`In[5]:`
```ruby
{% raw %}sudo rm -rf ~/.local/share/Trash/files/*{% endraw %}
```

## 보너스 휴지통에서 파일 복원하기

만약 휴지통에서 파일을 옮기고 싶다면,

그냥 파일을 옮기듯이 .Trash-0 폴더로 접속 후 옮겨주시면 됩니다.

`In[6]:`
```ruby
{% raw %}cd ~/workspace/.Trash-0/files
mv train.py ~/workpace/{% endraw %}
```
