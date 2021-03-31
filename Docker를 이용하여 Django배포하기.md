# Docker를 이용하여 Django배포하기
Docker를 이용하여 Django배포하기

-ssafy 서울캠퍼스 박종원


## AWS EC2 / Ubuntu / Docker사용하여 Django 배포.



## 시작하기 전에

> Django는 python 기반의 웹 서버 개발 프레임워크입니다.
> 
> Django 웹 서버 환경을 container 이미지로 만들어 배포하는 걸 오늘 목표로 하겠습니다.
> 
> 유용한 명령어들
- sudo docker images 현재 떠있는 이미지 확인

- sudo docker ps 현재 실행중인 컨테이너 확인

- sudo docker ps -a 현재 중지 중인 컨테이너 확인

- sudo docker rmi <id> 이미지 삭제

- sudo docker rm <id> 컨테이너 삭제

### 준비 ###
> Aws ubuntu 인스턴스(로컬도 가능)
> 
> 인스턴스를 사용할 경우 보안그룹이 80(nginx) 및 개인의 Backend 프로젝트 포트 환경에 맞게 오픈 되어있어야 한다.
> 
> 일단 https 적용을 하지 않고 배포하는 것을 목표로 한다.
> 
> putty 대신 mobaxterm을 사용하여 서버 내 파일 구조를 조금 더 편하게 보았다.


### EC2에 도커 설치하기 ###

- 아래의 명령어를 실행한다.
``` c
$ sudo apt update
$ sudo apt upgrade
```

- 패키지를 설치해 준다.
``` 
$ sudo apt install apt-transport-https ca-certificates curl gnupg-agent
software-properties-common
```

- Docker의 공식 GPG 키를 추가한다.
``` 
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add
```

- stable repository 를 세팅하기 위한 명령어를 실행한다.
``` 
$ sudo add-apt-repository "deb [arch=amd64]
https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

- 가장 최신 버전의 Docker 엔진을 설치한다
``` 
$ sudo apt update
$ sudo apt install docker-ce docker-ce-cli containerd.io
```

- 도커가 잘 설치되었는지 확인을 위해 버전확인을 해보자
``` 
$ docker -v

```

### 2. 도커 컨테이너 위에 Django 프로젝트 실행하기 ###

- 도커의 시작은 Dockerfile을 만드는 것입니다.(매우 중요)
- 아래와 같은 폴더구조에서 도커 파일을 생성 할 텐데요,

![](/img/Dockerfile_dic.png)


- cd /home/ubuntu/backend/로 폴더구조에 들어간다음 vi Dockerfile 로 파일을 생성하고 아래와 같이 본인의 환경에 맞게 적습니다.
``` 
FROM python:3.6.8

ENV PYTHONUNBUFFERED 1

RUN apt-get -y update 

RUN apt-get -y install vim # docker 안에서 vi 설치 안해도됨

WORKDIR /usr/src/app

COPY . .

WORKDIR ./backend

RUN pip install --upgrade pip # pip 업글 

RUN pip install -r requirements.txt # 필수 패키지 설치 

EXPOSE 8000 
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]

```

- DockerFile이란 Docker Image를 만들기 위한 설정 파일입니다. 
- 여러가지 명령어를 토대로 Docker File을 작성하면 설정된 내용대로 Docker Image를 만들 수 있습니다.


### Docker image 생성하기 ###

- 우선 현재 자신의 작업 경로가 dockerfile이 있는 경로인지 다시 한번 확인한다음 도커 이미지를 생성해줍니다.

``` 
$ docker build -t 내가만들이미지이름 .
```

-t 는 만드는 도커 이미지에 이름을 붙여주는 것이고, 그 뒤에 나오는 . 은  .은 docker 이미지로 만들 폴더 경로를 말합니다.
우리는 현재폴더를 가르키는 . 를 찍어 현재 폴더를 지정합니다.
![](/img/Docker_build.png)

명령어를 실행하면 터미널 창에서 뭐가 많이 뜨면서 이미지가 생성되는 걸 볼 수 있는데요, 
Dockerfile에 적은 순서 대로 이미지가 만들어 지게 됩니다.

### 만든 이미지 확인하기 ###

``` 
$ docker images
```

![](/img/Docker_image.png)

만들어진 이미지가 보이시나요?

이미지가 보인다면 Django를 container로 패키징 하는데까지 하신겁니다

### Container 실행하기 ###

- 패키징한 Django 이미지가 정상적으로 동작하는지 확인하기 위해,\
- 생성한 이미지를 사용해 컨테이너를 실행해 보겠습니다.

``` 
docker run -p 8000:8000 backend-django
```
![](/img/Docker_run.png)

### 잘 배포된건가 확인해보기 ###

- 내ip주소:8000 으로 접속 하여 잘 배포가 되었나 확인해보시면 됩니다!






