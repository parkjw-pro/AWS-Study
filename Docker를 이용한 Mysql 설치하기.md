# Docker를 이용하여 Mysql 설치하기
Docker를 이용하여 Mysql 설치하기

-ssafy 서울캠퍼스 박종원


## AWS EC2 / Ubuntu / Docker사용하여 mysql설치.



## 시작하기 전에

> 유용한 명령어들
- sudo docker images 현재 떠있는 이미지 확인

- sudo docker ps 현재 실행중인 컨테이너 확인

- sudo docker ps -a 현재 중지 중인 컨테이너 확인

- sudo docker rmi <id> 이미지 삭제

- sudo docker rm <id> 컨테이너 삭제

### 준비 ###
> Aws ubuntu 인스턴스(로컬도 가능)
> 
> Aws 인스턴스를 사용할 경우 보안그룹에서 인바운드 규칙의 3306포트가 열려있어야 한다.
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

### 2. Docker로 Mysql설치하기 ###

- 명령어 실행창에서 아래와 같은 명령어를 통해 mysql 컨테이너를 만듭니다.

``` 
$ sudo docker run --name DB이름 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=비밀번호 -d mysql

```

- 만든 mysql컨테이너가 잘 생성되었나 확인해봅니다.(위의 명령어 참조). 

``` 
$ sudo docker images -a

```

- 아래의 명령어를 통해 컨테이너 속 DB에 접속합니다. 


``` 
$ sudo docker exec -it DB이름 mysql -u root -p

```


- 이제 DB에 접속이 되었는데요, 아래와 같이 데이터베이스를 만들어 봅니다.

``` 
$ CREATE DATABASE dockerTest;

```



### Docker 네트워크 생성하여 컨테이너끼리 연동하기 ###

- 일단 현재 생성되어있는 Docker 네트워크 목록을 조회해봅니다.
- Docker 네트워크가 처음이시라면, 기본값인 bridge, host, none만 있을것입니다.

``` 
$ sudo docker network ls
```

-다음 명령어를 통해 새로운 bridge 네트워크를 생성하고 잘 생성되었는지 확인해봅니다.

``` 
$ sudo docker network create 네트워크이름
$ sudo docker network ls

```

![](/img/Docker_mysql_network.png)

- 특정 네트워크가 어떤 도커이미지를 묶고 있는지 확인하고 싶다면 아래의 명령을 통해 확인합니다.

``` 
$ docker network inspect 네트워크이름

```

- 위에서 만든 mysql 컨테이너를 방금 만든 네트워크에 추가해주기 위해 아래의 명령어를 실행합니다.

``` 
$ sudo docker network connect 네트워크이름 DB이름

```

- 다시 네트워크의 상세보기를하면 아까 만든 DB컨테이너가 해당 네트워크에 추가된 것을 확인 할 수 있습니다.

``` 
$ docker network inspect 네트워크이름

```


### DB하고 Backend프로젝트 연결하기 ###

- Docker의 장점 중, 같은 네트워크에 포함되어 있는 컨테이너들은 서로를 이름으로 찾을 수 있다는 장점이 있습니다.

- 예를들어 지금까지 backend의 환경설정에서 mysql을 연결할때 jdbc:mysql://<ip주소>:<포트번호>/<연결할데이터베이스이름>으로 했다면

- backend와 mysql이 같은 네트워크로 연동된 지금 우리는

- jdbc:mysql://<DB컨테이너이름>:<포트번호>/<연결할데이터베이스이름>로 연결할 수 있습니다.




