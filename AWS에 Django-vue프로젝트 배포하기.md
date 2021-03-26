# AWS로 Django-vue 프로젝트 배포하기(도메인구입ver)
-ssafy 서울캠퍼스 박종원


## AWS EC2 / Ubuntu / Nginx 에서 Vue 배포 및 Django 배포.



## 시작하기 전에

> 일단 도메인과 Aws ubuntu 인스턴스는 준비가 되어 있어야 한다.
>
> 여기서 준비란, 도메인주소 ns에 aws것을 입력해놓고, 인스턴스는 보안그룹이 80(nginx) 및 개인의 Backend 프로젝트 포트 환경에 맞게 오픈 되어있어야 한다.
>
> 일단 https 적용을 하지 않고 배포하는 것을 목표로 한다.
> 
> putty 대신 mobaxterm을 사용하여 서버 내 파일 구조를 조금 더 편하게 보았다.
>


### 1. 키 접속하기 ###

- $ ssh -i /path/my-key-pair.pem ubungu@my-instance-public-dns-name



### 2. 접속 후에 기존 패키지 업데이트 해주기 ###

- $ sudo apt-get update


### 3. Python 관련 패키지 설치 ###

- $ sudo apt-get install python3-dev python3-venv python3-pip


### 4. www-data 그룹에 ubuntu 유저 추가 ###

- $ sudo usermod -a -G www-data ubuntu

- ec2-user랑 ubuntu랑은 다르다.


### 5. Django 배포를 위한 폴더 생성 ###

- $ sudo python3 -m venv /var/www/html/django/venv


### 7. 코드를 업로드 하기 전에, 폴더의 소유자와 사용권한 변경 ###

- $ sudo chown -R ubuntu:www-data /var/ww/html/django
- $ sudo chmod -R g+w /var/www/html/django


### 8. 서버에 django 프로젝트를 업로드 ###


- 여기에서는 설명을 위해서 django 프로젝트 이름을 backend 라고 하겠습니다.
- backend 폴더 안에는 backend, manage.py, requirements.txt ... 등의 파일들이 있습니다.
- FileZilla 와 같은 FTP 소프트웨어를 사용하여 backend 폴더를 통째로 /var/www/html/django/ 안에 넣습니다.
- 

### 9. 가상 환경에 패키지를 설치하기 위해 소스코드 폴더로 이동 ###


- $ cd /var/www/html/django


### 10. 가상 환경 활성화 ###


- $ sudo -s    // root 계정으로 전환
- source venv/bin/activate // 해당 명령어를 기입하면 계정 이름 앞에 (venv) 생성


### 11. 패키지 설치 ###


- cd backend
- pip install -r requirements.txt

- 이때, 본인의 Django 프로젝트에 requirements.txt가 없다면 pip freeze > requirements.txt 를 통해 생성해준다.



### 12. 프로젝트 구동 ###


- python manage.py runserver 8000  // 여기서 포트는 프론트에서 요청하는 포트번호를 적는다.



### 그런데 여기까지 했는데 서버상에 띄운 프론트가 백앤드에서 데이터를 못받아온다면?  ###

- 프론트코드상의 서버url이 잘못된 것 일수도 있다.
- 나같은 경우는 www.xxx.xxx 가 도메인주소였는데, 프론트상에서 www.xxx.xxx:8000 으로 서버 url을 지정해주고,
- 실행을 python manage.py runserver 8000  이렇게 8000번으로 해주었더니 해결 할 수 있었다.











