<img width="427" height="349" alt="image" src="https://github.com/user-attachments/assets/f9e44abc-f69a-460c-86ee-6d55d301bae2" />

Docker란 리눅스 컨테이너 기반으로하는 오픈소스 가상화 플랫폼이다.

## 가상화를 사용하는 이유?
서버 관리자 입장에서 CPU사용률이 10%대 밖에 되지 않는 활용도가 낮은 서버들의 리소스 낭비일 수 밖에 없다.
그렇다고 모든 서비스를 한 서버안에 올린다면 안정선에 문제가 생길수도 있다. 그래서 안정성을 높이며 리소스도 최대한 활용할 수 있는 방법으로 나타난게 서버 가상화이다.

## 컨테이너란?
가상화 기술 중 하나로 대표적으로 LXC(Linux Container)가 있다.
기존 OS를 가상화 시키던 것과 달리 컨테이너는 OS레벨의 가상화로 프로세스를 격리시켜 동작하는 방식으로 이루어진다.

## VM 가상화 vs Docker 가상화 
<img width="828" height="450" alt="image" src="https://github.com/user-attachments/assets/68b9a98c-6eea-4201-9e3d-7b77f1ec3063" />

VM 같은 경우 하드웨어 위에 OS가 올라가는 형태 
컨테이너 기반 가상화는 Docker엔진 위 Application 실행에 필요한 바이너리만 올라감

## Docker Image
Docker Image란 컨테이너를 실행할 수 있는 실행파일, 설정 값 들을 가지고 있는 것

<img width="548" height="413" alt="image" src="https://github.com/user-attachments/assets/379e4e7a-d36a-43bd-b0e1-63c4fa5f4ffa" />

그림과 같이 Image를 컨테이너에 담고 실행을 시킨다면 해당 프로세스가 동작

<img width="763" height="301" alt="image" src="https://github.com/user-attachments/assets/7e3aa293-e031-4362-818c-5a7bab650b9f" />

Ubuntu이미지를 만들기 위해  Layer A,B,C가 들어간다.
nginx이미지를 만들기 위해 Ubuntu image + nginx
web app 이미지를 만들기 위해 nginx + web app source 
※ 기존 image + container 

# Docker File
Docker File이란 Docker Image를 만들기 위한 설정 파일

<img width="744" height="633" alt="image" src="https://github.com/user-attachments/assets/ee2713a9-9922-4fb8-964d-9e8a68a2cdb9" />

※ 컨테이너에 담을 파일들은 Dockerfile 하위 디렉토리에 있어야하며 Dockerfile 안에서 ADD시 절대경로는 사용 불가

```
FROM
- 기반이 되는 이미지 레이어

MAINTAINER
- 메인테이너 정보

RUN
- 도커이미지가 생성되기 전 수행할 쉘 명령어

VOLUME
- VOLUME은 디렉터리의 내용을 컨테이너에 저장하지 않고 호스트에 저장하도록 설정
- 데이터 볼륨을 호스트의 특정 디렉터리와 연결하려면 docker un 명령에서 -v 옵션 사용
- ex) -v/root/data:/data

CMD
- 컨테이너가 시작되었을 때 실행할 실행 파일 또는 셸 스크립트
해당 명령어는 DockerFile내 1회만 사용 가능

WORKDIR
- CMD에서 설정한 실행 파일이 실행될 디렉터리

EXPOSE
- 호스트와 연결할 포트 번호
```

## .Dockerignore
dockerignore 파일 생성 시 Docker 이미지 생성할 때 이미지안에 들어가지 않을 파일 지정 가능 

<img width="732" height="344" alt="image" src="https://github.com/user-attachments/assets/e96bfbd8-8f7b-4eb8-8d57-90bbe11a95e1" />

## 작성 된 Docker File로 Image 만들기 
```
$docker build -t [만들고 싶은 이미지 이름]
```
※ 해당 명령어는 반드시 DockerFile 경로에서 입력 

## Docker Registry 구축
### Make Open SSL
```
$ mkdir -p /data/certs
$ openssl req \
  -newkey rsa:4096 -nodes -sha256 -keyout ~/data/certs/server.key \
  -x509 -days 36500 -out ~/data/certs/server.crt
```
### Docker Registry에 접속 할 장비 설정

**접속할 장비들에 server.crt 파일 복사**
```
$ mkdir -p /etc/docker/certs.d/docker-registry.khj93tistory.com
$ cp /data/certs/server.crt /etc/docker/certs.d/docker-registry.khj93tistory.com:5000/server.crt
```

**접속할 장비들에 hosts 등록 (localhost일 경우 127.0.0.1**
```
$ vim /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 docker-registry.kh93tistory.com
:wq!
```

### Docker Registry Image Pull / Start 
```
$ docker pull registry
$ docker run -d -p 5000:5000 --restart=always --name docker-registry   -v /data/certs:/certs   -e 
REGISTRY_HTTP_TLS_CERTIFICATE=/certs/server.crt   -e REGISTRY_HTTP_TLS_KEY=/certs/server.key   -v /data/certs/auth:/auth   -e
"REGISTRY_AUTH=htpasswd"   -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm"   -e
REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd   registry
```

### Docker Registry Image Push
```
$ docker push docker-registry.kh93tistory.com:5000/[Image Name]:[tag]
```

## Docker 설치부터 실행까지 
### Docker 설치 
```
$ curl -fsSL https://get.docker.com/ | sudo sh
$ service docker start
$ docker version
```

### Docker Image Pull 받기 
```
$ docker pull [Image Name]
```
※ Docker 이미지 내역은 Docker Hub에서 확인 할 수 있다.  https://hub.docker.com/
※ Version 명시가 없을 시 latest Pull
※ Image 앞에 Url이 없을 경우 default로 DockerHub에서 pull 사설 레지스트리에서 받을 시 앞에 Url 작성

### Docker Image list 확인
```
$ docker image
```

### Docker Image Delete
```
$ docker rmi [이미지 이름]
```

### Docker Image Push 
```
$ docker push [레지스트리url/이미지:버전]
ex) Docker Hub push - docker push mongo:3.6.3
```

### Docker Image로 컨테이너 실행 
```
$ docker run <옵션> <이미지 이름, ID> <명령> <매개 변수>
```

### Docker List 확인 [Container]
```
$ docker ps (실행중인 컨테이너만 확인)
$ docker ps - a (종료된 컨테이너까지 확인)
```

### Docker 컨테이너 log 확인 
```
$ docker logs –f [컨테이너 이름]
```

### Docker 컨테이너 내부 디렉토리 들어가기 
```
$ docker exec -i -t [컨테이너 이름] bash
```

### Docker 컨테이너 종료
```
$ docker kill [컨테이너이름]
```

### Docker 종료된 컨테이너 실행
```
$ docker start [컨테이너이름]
```

### Docker 컨테이너 삭제
```
$ docker rm [컨테이너이름]
```

