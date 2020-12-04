---
layout: post
title: 딥러닝을 위한 Ubuntu & Docker 서버 세팅하기
tags: [Deep Learning]
image:
  path: /images/abstract-1.jpg
  feature: abstract-1.jpg
comments: true
---

이번 글에서는 딥러닝을 사용하기 위한 Ubuntu 서버 및 Docker 세팅을 다루어 보겠습니다.

## 1. 우분투 서버 설치(Install Ubuntu Server)

### 1) Ubuntu Server 16.04 설치

USB를 통해 Ubuntu Server를 설치하여야합니다.

본 내용은 Ubuntu 16.04 LTS 기반으로 작성되었으며, 이미지는 [http://www.ubuntu.com/download/server](http://www.ubuntu.com/download/server)에서 다운받을 수 있습니다.

다운받은 후에는 보통 윈도우를 설치하는 방법처럼 설치하면 됩니다. 다만 설치시 몇 가지 유의사항이 있습니다.

* 설치 언어는 English로 해주셔야합니다.
* 위치는 Seoul로 해주셔야합니다.

USB 부팅 디스크를 만드는 법 및 이 외의 설치는 많은 곳([https://jeongchul.tistory.com/492](https://jeongchul.tistory.com/492))에 나와있으니 따로 서술하지는 않겠습니다.

### 2) Port 열기 

이제 다른 컴퓨터에서 서버에 접속이 가능할 수 있도록 포트를 열어주어야합니다.

이는 우선 터미널에 접속하여 실행해야합니다.

* 터미널로 전환 : Crtl+Alt+F1~6
* GUI로 전환 : Crtl+Alt+F7~12

터미널에서 우선 openssh-server를 설치해주어야합니다.

> sudo apt-get install openssh-server

그 후 Port 추가를 위해 vi 편집기로 설정을 변경해줍니다.

> sudo vi /etc/ssh/sshd_config

vi 편집기의 경우 아래와 같이 사용할 수 있습니다.

* i - 편집모드로 전환
* esc - 편집모드 종료
* :w - 저장하기
* :q - 나가기
* :wq - 저장하고 나가기

위의 명령어를 활용하여 해당 파일에 아래 명령어를 추가해줍니다.

* Port 22
* Port 2121

주의하실 점은 앞의 #은 주석처리를 의미하기 때문에 제거해야합니다.

그 후 service를 재부팅하기 위해 아래 명령어를 입력합니다.

> sudo service ssh restart

만약 이렇게 했는데 접속이 안된다면 아래 명령어를 차례대로 입력해줍니다.

> sudo apt-get remove openssh-client openssh-server    
> sudo apt-get update    
> sudo apt-get install openssh-server   

혹시 학내 서버를 사용할 경우, 각 학교 사이트의 서비스 포트를 신청하여 학외 접속이 가능하도록 하여야합니다.

## 2. 그래픽 드라이버 설치(Install Graphic Driver)¶

### 1) NVIDIA 드라이버 확인

일반적으로 포맷한 서버에는 드라이버가 설치 되어 있지않기 때문에 그래픽 드라이버를 따로 설치해주어야합니다.

아래 명령어를 우선 입력합니다.

> sudo cat /proc/driver/nvidia/version

만약 결과가 cat: /proc/driver/nvidia/version: No such file or directory라면 NVIDIA 그래픽 카드가 설치되어있지 않은 것입니다.

따라서 이제부터 그래픽카드의 드라이버를 설치해야합니다.

혹시 설치된 그래픽카드의 종류를 모르는 경우에는 아래 명령어로 확인하면 됩니다.

> sudo lspci -k

이제 [http://www.nvidia.com/Download/Find.aspx?lang=en-us](http://www.nvidia.com/Download/Find.aspx?lang=en-us) 사이트에 가서 설치된 그래픽카드의 드라이버를 확인해봅니다.

GTX TITAN V의 경우에는 387.34라고 되어있습니다. 추가적으로 드라이버의 상세정보에 가면 *Recommended CUDA Version*도 확인 가능합니다.

이제 홈페이지를 통해 그래픽카드의 드라이버를 확인했습니다. 만약 여러 개의 드라이버가 가능할 경우 가장 최신 버전으로 설치하면 됩니다.

이제 우분투에서 확인하면 됩니다.

> apt-cache search nvidia

위 명령어를 입력하면 nvidia-xxx로 표현되는 설치 가능한 드라이버 버전이 출력되게 됩니다.

예를 들면 아래와 같은데,

nvidia-361 - Transitional package for nvidia-367    
nvidia-361-dev - Transitional package for nvidia-367-dev   
nvidia-367 - Transitional package for nvidia-375   
nvidia-367-dev - Transitional package for nvidia-375-dev   
nvidia-375 - Transitional package for nvidia-384   
nvidia-375-dev - Transitional package for nvidia-384-dev   
nvidia-384 - NVIDIA binary driver - version 384.111   
...   
nvidia-387 - ...   
...

이 경우 GTX TITAN V는 387.34이므로 nvidia-387을 설치하면 됩니다.

### 2) 드라이버 설치

드라이버 설치 전에 디스플레이 매니저를 사용 중지합니다.

> sudo service lightdm stop

혹시 이전에 설치했는데 되지 않았던 경우라면 아래 명령어를 통해 NVIDIA의 모든 패키지를 삭제하고 진행합니다.

> sudo apt-get remove --purge nvidia*

이제 확인한 드라이버 버전을 바탕으로 설치하면 됩니다.

> sudo apt-get install nvidia-387 nvidia-modprobe

혹시 폴더 생성의 여기서 에러가 날 경우 아래 명령어로 입력해주시면 됩니다.

> sudo mkdir /usr/lib/nvidia    
> sudo apt-get install nvidia-387 nvidia-modprobe

그 후 재부팅을 통해 드라이버를 정상적으로 설치 완료합니다.

> sudo reboot

### 3) 드라이버 설치 확인

이제 드라이버가 정상적으로 설치 되었는지 확인해야합니다.

> sudo cat /proc/driver/nvidia/version

그럼 이제 드라이버 버전을 확인할 수 있으며,

> sudo lspci -k

위의 명령어로 그래픽 드라이버가 해당 그래픽카드에서 사용되는지 Kernel driver in use : nvidia 등의 문구를 통해 확인 가능합니다.

추가적으로, 아래 명령어를 활용하면 현재 그래픽카드의 상태 및 사용량을 간단하게 확인할 수 있습니다.

> nvidia-smi

만약 위 명령어를 실행했을 때 설치 전과 똑같이 표시가 안되거나 에러가 나게 되면 아래 명령어를 실행시켜 줍니다.

> sudo ubuntu-drivers list   
> sudo ubuntu-drivers autoinstall   
> sudo service lightdm restart   

## 3. 도커(Docker) 세팅

우분투 서버를 세팅할 때 AWS와 같은 온라인 서비스의 경우 집에서도 초기화를 할 수 있지만,

개인 서버를 구축한 경우 물리적 거리가 있는 경우가 많습니다.

이 경우에는 서버에서 작업을 하다가 문제가 생긴다면 해당 서버로 이동하여 살펴보아야하기 때문에 상당히 골치아파집니다.

따라서 온라인 서비스를 활용하지 않는 경우에는 비교적 포맷 및 복구가 쉬운 도커를 추천드립니다.

(물론 AWS에서도 도커 사용은 가능합니다 ^^)

도커가 무엇인지 더 자세히 알고 싶으신 분들은 [https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html](https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html)를 참고해주시면 됩니다.


### 1) 도커 설치

** 중요 : 도커를 한 번 설치한 후에는 그래픽 드라이버를 바꾸게 되면 매우 골치 아파집니다.. 따라서 꼭 그래픽 드라이버를 최신으로 유지하고 설치하도록 합시다! **

```ruby
# Delete all nvidia and cuda packages
sudo apt-get remove --purge '^nvidia-.*'
sudo apt-get remove --purge '^cuda.*'

# Reinstall nvidia drivers using the drivers ppa:
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt update
sudo apt upgrade
sudo reboot
```**


도커 설치는 매우 간단합니다. 아래 명령어만 잘 입력해주시면 됩니다.

각 명령어의 뜻이 궁금하시거나 더 자세한 설치가 필요하시면 아래 주소를 참고 부탁드립니다.

[https://eungbean.github.io/2018/11/09/Ubuntu-Installation2-3/](https://eungbean.github.io/2018/11/09/Ubuntu-Installation2-3/)


```ruby
#Update the apt package index:
sudo apt-get update

#Install packages to allow apt to use a repository over HTTPS:
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common

#Add Docker’s official GPG key:
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

#Verify that you now have the key with the fingerprint:
#9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88
sudo apt-key fingerprint 0EBFCD88

#set up the stable repository
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

#Update the apt package index:
sudo apt-get update

#Install the latest version of Docker CE
sudo apt-get install docker-ce

#Verify that Docker CE is installed correctly by running the hello-world image.
sudo docker run hello-world

sudo usermod -aG docker $USER # 현재 접속중인 사용자에게 권한주기
sudo usermod -aG docker your-user # your-user 사용자에게 권한주기
```

도커 설치 확인을 위해서는 아래 명령어를 입력했을 때 정상적으로 출력되었다면 설치가 된 것입니다.

> sudo docker version


### 2) Docker로 Ubuntu 16.04 Container 생성하기

이제 Docker의 핵심 부분인 컨테이너(Container)를 만들어보는 과정을 거치겠습니다.

> docker run ubuntu:16.04

docker에서 처음 run을 하게되면 이미지를 다운 받은 후 컨테이너를 생성합니다.

따라서 위 명령어를 입력하면 ubuntu:16.04의 이미지가 다운받아지고,

이 이미지를 바탕으로 컨테이너를 생성해줍니다.

이제 그럼 정상적으로 Ubuntu가 생성된 것을 보실 수가 있으실 겁니다.

하지만 명령어를 전달하지 않았으므로 컨테이너가 종료되게 됩니다.

만약 bash 명령어를 사용하고 싶은 경우 아래와 같이 입력하면 됩니다.

> docker run -it ubuntu:16.04 /bin/bash

여기서 -it의 의미는 i가 interative, t가 tty모드를 의미하여 터미널 명령을 즉각적으로 입력하고 받을 수 있게 한다는 얘기입니다.

이제는 이 가상머신에서 터미널 명령어도 사용할 수 있게 됩니다.

이처럼 docker에서는 한 번 다운 이미지를 바탕으로 여러 개의 가상환경인 컨테이너를 간단하게 만들 수 있습니다.

도움이 될만한 명령어를 아래에 적어두도록 하겠습니다.

* docker pull ubuntu : ubuntu 이미지 가져오기
* docker images : 다운받은 이미지 확인


* docker run [image name] : 해당 이름을 가진 이미지 기반의 컨테이너 생성
    * -i : interative 모드
    * -t : tty 모드
    * -p [host port]:[container port] : 호스트와 컨테이너의 포트 연결
    * --name [container name] : 컨테이너 이름 설정
    * -rm : 프로세스 종료시 컨테이너 자동 제거
    * -v [host dir]:[container dir] : 호스트와 컨테이너의 디렉토리 연결
    * -h [host name] : 컨테이너의 호스트 네임 설정
* docker ps -a : 모든 컨테이너 목록 및 상태 확인


* docker stop [container id] : 해당 ID에 해당하는 컨테이너 끄기
* docker restart [container id] : 해당 ID에 해당하는 컨테이너 재시작
* docker rm [container id] : 해당 ID에 해당하는 컨테이너 삭제


* docker attach [container id or name] : 해당 ID 혹은 해당 이름에 해당하는 컨테이너에 접속
* docker exec -it [container id or name] [command] : 해당 ID 혹은 이름에 해당하는 컨테이너에 접속하여 command 실행
    * docker exec -it [container id or name] bash : 해당 ID 혹은 이름에 해당하는 컨테이너에 접속하여 bash 실행
* (Ctrl+P) + (Ctrl+Q) : 터미널 유지한채 컨테이너 빠져나오기
* exit : 터미널 종료하며 컨테이너 빠져나오기


* docker diff [container id] : 기존 이미지와 해당 ID의 컨테이너의 차이점 확인하기
* docker commit [container id] [image name] : 해당 ID의 컨테이너를 뒤의 이름으로 이미지로 저장하기
* docker rmi [image name] : 해당 이름을 가진 이미지 삭제하기
* docker save -o [dir/file] [image name] : 해당 이름을 가진 이미지를 파일(.tar)로 저장하기
* docker load -i [dir/file] [image name] : 해당 이름을 가진 이미지를 파일(.tar)을 불러오기

더 많은 옵션은 [https://docs.docker.com/engine/reference/run/](https://docs.docker.com/engine/reference/run/)을 참조 바랍니다.

### 3) Nvidia-Docker 설치

도커에서 그래픽카드를 사용하기 위해서는 nvidia-docker라는 추가적인 프로그램 설치가 필요합니다.

[https://github.com/NVIDIA/nvidia-docker](https://github.com/NVIDIA/nvidia-docker)에서 업데이트를 확인할 수 있으며, 현재 설치 명령어는 아래와 같습니다.


```ruby
# If you have nvidia-docker 1.0 installed: we need to remove it and all existing GPU containers
docker volume ls -q -f driver=nvidia-docker | xargs -r -I{} -n1 docker ps -q -a -f volume={} | xargs -r docker rm -f
sudo apt-get purge -y nvidia-docker

# Add the package repositories
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | \
  sudo apt-key add -
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | \
  sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update

# Install nvidia-docker2 and reload the Docker daemon configuration
sudo apt-get install -y nvidia-docker2
sudo pkill -SIGHUP dockerd

# Test nvidia-smi with the latest official CUDA image
docker run --runtime=nvidia --rm nvidia/cuda:9.0-base nvidia-smi

# Restart Docker
sudo systemctl daemon-reload
sudo systemctl restart docker
```

이제 그래픽카드를 사용하는 컨테이너를 활용하고 싶으면 nvidia-docker를 활용하면 됩니다.

만약 컨테이너가 사용할 수 있는 그래픽카드를 제한하고 싶다면, run을 할 때 아래 옵션을 활용하면 됩니다.

* (nvidia-docker2) docker run -it --runtime=nvidia -e NVIDIA_VISIBLE_DEVICES=1,2 nvidia/cuda nvidia-smi
* (nvidia-docker) NV_GPU=1,2 nvidia-docker run -it nvidia/cuda nvidia-smi

혹시 nvidia-docker를 이미 설치했지만, 설치하고보니 그래픽 드라이버가 오래되서 cuda를 활용하지 못할 경우 안타깝게도 모두 재설치하는 것이 가장 빠른 길입니다. 아래 혹은 [링크](https://devtalk.nvidia.com/default/topic/1065310/linux/docker-tensorflow-gpu-can-t-find-device-as-well-as-nvidia-smi-quot-no-device-found-quot-/)를 따라해주시면 됩니다.

```ruby
# Delete all nvidia and cuda packages
sudo apt-get remove --purge '^nvidia-.*'
sudo apt-get remove --purge '^cuda.*'

# Reinstall nvidia drivers using the drivers ppa:
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt update
sudo apt upgrade
sudo reboot
```

그 후 드라이버 설치부터 다시 시작해주시면 됩니다.


#### 참고: 만약 위로 설치가 안된다면?

만약 위의 과정에서 에러가 발생하거나 설치가 잘되지 않는다면 아래 명령어를 실행해봅시다!

```ruby
sudo apt-get install docker-ce=18.03.1~ce-0~ubuntu
sudo apt-get install nvidia-docker2=2.0.3+docker18.03.1-1 nvidia-container-runtime=2.0.0+docker18.03.1-1
sudo systemctl daemon-reload
sudo systemctl restart docker
sudo docker run --runtime=nvidia --rm nvidia/cuda:9.0-base nvidia-smi
```


### 4) Deepo 이미지 사용하기

이제 대강의 이미지 및 컨테이너 사용법 및 nvidia-docker를 설치했으니, 딥러닝용 이미지를 다운 받을 시간입니다.

여러 딥러닝용 이미지(modern-deep-learning, jupyter-deeplearning) 등이 있지만 여기서는 Deepo를 사용할 것입니다.

보다 자세한 Deepo에 대한 내용은 [https://github.com/ufoym/deepo](https://github.com/ufoym/deepo)에서 확인할 수 있습니다.

아래 명령어를 활용하여 봅시다.


```ruby
nvidia-docker run -it \
-p 8888-8890:8888-8890 \
--shm-size 2G \
-h hk \
--name hk \
-v /docker/data:/data \
ufoym/deepo:all-py36-jupyter
```

* nvidia-docker로 그래픽카드를 사용 가능한 컨테이너를 만듭니다.   
* -it로 interative한 tty 모드의 컨테이너로 만들도록 합니다.   
* -p로 호스트의 포트(8888-8890)와 컨테이너의 포트(8888-8890)를 연결시킵니다.   
    * Jupyter notebook의 포트가 포함되어 있어야하며, 8888~9000 포트를 추천드립니다. (다른 포트의 경우 이것만으로는 안되는 경우 존재)
* --shm-size로 docker가 사용할 수 있는 shared memory의 크기를 관리합니다. 너무 작으면 pytorch의 dataloader에서 에러가 나는 등의 문제가 발생합니다
    * 적당한 크기를 알아보기 위해서는 터미널에 "df -h" 명령어를 확인하여 /dev/shm의 사이즈를 참고하면 됩니다.
* -h로 호스트의 이름을 hk로 정합니다.   
* --name으로 컨테이너의 이름도 hk로 정합니다.   
* -v로 호스트의 /docker/data 폴더를 컨테이너의 /data 폴더로 연결시킵니다.
    * 이 경우 이전에 mkdir /docker/data로 폴더를 만들어주어야합니다.
    * 또한 이렇게 연결한 후 나중에 docker image로 save를 할 경우, 연결된 폴더는 따로 저장되지 않습니다. 따라서, save된 tar 파일에는 해당 환경만 저장되어있으므로 연결된 폴더는 따로 백업해야합니다.
* ufoym/deepo:all-py36-jupyter의 이미지를 바탕으로 만들도록 선언합니다.

이제 그럼 hk 이름의 컨테이너가 만들어지고, 이 안에는 왠만환 딥러닝 프레임워크 및 Jupyter가 설치되어있습니다!

이로써 우리는 매우 간단하게 딥러닝 환경을 설정할 수 있습니다.

### 5) Jupyter Notebook 설정

추가적으로 Jupyter Notebook의 비밀번호 및 포트를 설정해주려면 컨테이너 bash에서 아래와 같이 하면 됩니다.

```ruby
jupyter notebook --generate-config
vi ~/.jupyter/jupyter_notebook_config.py
```

이제 아래의 부분을 수정해주시면 됩니다.

* c.NotebookApp.ip = '0.0.0.0' # ip 설정
* c.NotebookApp.port = 8888 # port 설정
* c.NotebookApp.allow_root = True # root의 jupyter 사용 가능
* c.NotebookApp.open_browser = False # jupyter 실행 시 브라우저 사용 끄기
* c.NotebookApp.password = 'sha1:~' # 비밀번호 설정 아래 참고
* c.notebookapp.notebook_dir = '/data' # 기본 폴더 설정


```python
# 비밀번호 설정을 위한 python code
from notebook.auth import passwd
passwd()
```

이제 Docker 및 Jupyter Notebook으로 신나는 딥러닝을 즐기시면 됩니다!
