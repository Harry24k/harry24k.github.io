---
layout: post
title: 주피터 노트북 찾기
tags: [Python]
image:
  path: /images/abstract-1.jpg
  feature: abstract-1.jpg
---

이번 글에서는 우분투 환경에서 주피터 노트북 검색 방법에 대해 알아보겠습니다.

## grep 명령어

사실, 주피터 노트북 전용으로 찾는 것은 아니고, 우분투 내의 명령어인 grep을 활용하면 됩니다.

grep은 다음과 같이 사용할 수 있습니다.

`In[1]:`
```ruby
{% raw %}grep [OPTION] [PATTERN] [FILE]{% endraw %}
```

OPTION으로는 -r (하위 디렉토까지 탐색), -i (대소문자 무시) 등이 있습니다.

따라서 grep을 활용해서 주피터 노트북 파일들 중 'torch'라는 단어가 포함된 파일을 하위 디렉토리까지 검색하고 싶다면, 아래를 입력하면됩니다.

`In[2]:`
```ruby
{% raw %}grep -r torch *.ipynb{% endraw %}
```
