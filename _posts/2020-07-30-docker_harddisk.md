---
layout: post
title: 도커 하드디스크 연결하기
tags: [Docker]
image:
  path: /images/abstract-1.jpg
  feature: abstract-1.jpg
---

이번 글에서는 도커 환경에 하드디스크를 연결해보겠습니다.

도커 설치 [포스트](https://harry24k.github.io/deep-learning-server-docker-post/)를 기억해보면.

다음과 같은 -v 옵션으로 폴더를 연결해주었었습니다.

`In[1]:`
```ruby
{% raw %}nvidia-docker run -it \
-p 8888-8890:8888-8890 \
--shm-size 2G \
-h hk \
--name hk \
-v /docker/data:/data \
ufoym/deepo:all-py36-jupyter{% endraw %}
```

즉, 하드디스크도 마운트를 하고 잘 연결만한다면 도커에서도 사용할 수 있는 것입니다!

우선 하드디스크부터 설치해야합니다.

편의상 2TB를 초과하는 하드디스크라고 가정해보겠습니다.

만약 2TB 이하라면 [이 사이트](https://zero-gravity.tistory.com/297)를 참조해주세요!

## 하드디스크 확인

먼저 하드디스크 먼저 확인을 합니다.

`In[2]:`
```ruby
{% raw %}sudo fdisk -l{% endraw %}
```
`Out[2]:`

    Disk /dev/sdb: 3.7 TiB, 4000787030016 bytes, 7814037168 sectors
    Units: sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 4096 bytes
    I/O size (minimum/optimal): 4096 bytes / 4096 bytes
    Disklabel type: gpt
    Disk identifier: 22DAD03C-ECFC-4071-AC46-89949847C008
    
    Device     Start        End    Sectors  Size Type
    /dev/sdb1   2048 7814035455 7814033408  3.7T Linux filesystem
    
    
    Disk /dev/sdc: 3.7 TiB, 4000787030016 bytes, 7814037168 sectors
    Units: sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 4096 bytes
    I/O size (minimum/optimal): 4096 bytes / 4096 bytes
    Disklabel type: gpt
    Disk identifier: B61D21E9-C835-4F9D-89D6-08AD884988A9
    
    Device     Start        End    Sectors  Size Type
    /dev/sdc1   2048 7814035455 7814033408  3.7T Linux filesystem
    
    
    Disk /dev/sdd: 3.7 TiB, 4000787030016 bytes, 7814037168 sectors
    Units: sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 4096 bytes
    I/O size (minimum/optimal): 4096 bytes / 4096 bytes
    Disklabel type: gpt
    Disk identifier: 7D294F2C-5746-455E-A955-838BBC1E9203
    
    Device     Start        End    Sectors  Size Type
    /dev/sdd1   2048 7814035455 7814033408  3.7T Linux filesystem
    

결과로 어떤 디스크가 있는지 출력되고, 각 디스크의 정보가 담기게 됩니다.

이 때 각 Disk 오른쪽에 있는 이름 "/dev/sdb"와 같은 것이 바로 해당 디스크의 이름이 됩니다.

이제 파티션 할당을 할 디스크의 이름을 기억한 뒤 아래와 같이 입력하면 됩니다.

 "/dev/sdb"로 선택했다고 가정하겠습니다.

`In[3]:`
```ruby
{% raw %}parted /dev/sdb
mklabel gpt
unit TB
mkpart primary 0.00TB 4.00TB # 알맞은 용량 기입
print
quit{% endraw %}
```

그리고 파일 시스템을 만들어 줍니다.

`In[4]:`
```ruby
{% raw %}mkfs -t ext4 /dev/sdb{% endraw %}
```

## 마운트 및 도커와 연결

마운트를 하면 완료!

단, 이 때 마운트할 폴더는 사전에 생성되어있어야 합니다.

`In[5]:`
```ruby
{% raw %}mount -t ext4 /dev/sdb /workspace/disk{% endraw %}
```

여기서 꼼수를 부리면 도커를 재설치하지 않고도 여러 개의 하드디스크를 도커에서 접근하게 할 수 있는데,

그 방법은 아래와 같습니다.

0. 이미 도커가 "~/workspace/"라는 폴더를 공유하고 있다고 가정해보겠습니다.

1. 도커와 공유된 폴더 안에 마운트 폴더를 만든다.
    * 위와 가정 하에서는 "~/workspace/disk"와 같이 마운트 폴더를 만들어줍니다.
    
2. 마운트 폴더에 디스크를 연결시킨다.
    * mount -t ext4 /dev/sdb ~/workspace/disk
    
3. 도커를 껐다가 켠다.
    * docker stop hk
    * docker start hk
    * 이 과정이 없으면 정상적으로 인식이 안됩니다!
    
이러한 방식을 거쳐 여러 개의 디스크를 도커에서 접근할 수 있게 할 수 있습니다!

## 마운트 되돌리기

만약 중간에 폴더를 잘못 설정했다면 아래와 같은 명령어로 되돌릴 수 있습니다.

`In[6]:`
```ruby
{% raw %}umount /workspace/disk{% endraw %}
```

## 권한 부여하기

새로 마운트된 디스크는 권한 문제가 있을 수 있는데 이는 아래와 같이 해결 가능합니다!

`In[7]:`
```ruby
{% raw %}chown -R [계정명] /workspace/disk
chmod +w /workspace/disk{% endraw %}
```

확인은 아래를 통해 할 수 있습니다.

`In[8]:`
```ruby
{% raw %}ls -all /workspace{% endraw %}
```
