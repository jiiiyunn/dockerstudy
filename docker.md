# Docker

## 도커 객체(docker objects)
### Docker image
서비스 운영에 필요한 서버 프로그램, 소스코드, 컴파일된 실행 파일을 묶은 것  

- 컨테이너를 생성할 때 필요한 요소
(가상머신을 생성할 때 사용하는 가상머신 이미지, ISO파일과 비슷한 개념)
- 여러 개의 계층(레이어)으로 된 바이너리 파일로 존재
- 컨테이너를 생성하고 실행할 때 읽기 전용으로 사용됨
- 만들어지고 난 후 변경되지 않는다
- 저장소(registry)/리포지토리(repository) : 태그(tag=버전(임의부여))

### Docker container
- 도커 이미지로 생성한 해당 이미지의 목적에 맞는 파일이 들어 있는 파일 시스템과 격리된 시스템 자원 및 네트워크를 사용할 수 있는 독립된 공간 = 자원이 있는 공간
- 컨테이너는 이미지를 읽기 전용으로 사용하되, 이미지에서 변경한 사항만 컨테이너 계층에 저장하므로 컨테이너에서 무슨짓을 하던지 원래 이미지는 영향을 받지 않음
- 생성된 컨테이너는 각기 독립된 파일 시스템을 제공받으며 호스트와 분리돼 있으므로 특정 컨테이너에서 어떤 애플리케이션을 설치하거나 삭제해도 다른 컨테이너와 호스트는 변화가 없음




## Formatting
```C:\docker> docker ps
CONTAINER ID   IMAGE     COMMAND                   CREATED          STATUS          PORTS                   NAMES
4acff13f90ec   nginx     "/docker-entrypoint.…"   8 minutes ago    Up 8 minutes    0.0.0.0:51392->80/tcp   mynginx
337b740e7bc1   ubuntu    "/bin/bash"               11 minutes ago   Up 11 minutes                          myubuntu
```
컨테이너 정보에서 원하는 부분만 뽑아서 보고싶을 때 사용

```
C:\docker> docker container ls --format "{{.ID}}:{{.Command}}"
4acff13f90ec:"/docker-entrypoint.…"
337b740e7bc1:"/bin/bash"
```
--format을 사용 .원하는요소 로 특정 데이터만 출력가능하다

## registry와 repository
**registry**  
- 도커 이미지를 관리하는 공간
- docker hub(기본 레지스트리)   

**repository**    
- 레지스트리 내에 도커 이미지가 저장되는 공간
- namespace/imgname:tag 소유, 권한과 관련된 이름 구분
  ex) aa(docker hub id)/image1:latest   bb/image1:latest ←얘네를 구분하게 하는 것

## volume
- Docker 컨테이너에서 생성되고 사용되는 데이터를 유지하기 위한 메커니즘
- Docker에서 완전히 관리
- 호스트의 파일 시스템 내에 특정 영역(리눅스의 경우, /var/lib/docker/volumes/)을 도커가 사용, 관리
- 도커가 아닌 다른 프로세스에서는 해당 영역 접근이 불가능
- 장점
    - 볼륨은 바인드 마운트보다 백업 또는 마이그레이션이 더 용이
    - Docker CLI 명령 또는 Docker API를 사용하여 볼륨 관리 가능
    - 볼륨은 Linux 및 Windows 컨테이너 모두에서 작동
    - 여러 컨테이너 간에 볼륨을 더욱 안전하게 공유
    - 볼륨 드라이버를 사용하면 원격 호스트 또는 클라우드 공급자에 볼륨을 저장하고, 볼륨 내용을 암호화하거나 다른 기능을 추가하는 것이 가능
    - 새 볼륨에는 컨테이너에 의해 콘텐츠가 미리 채워질 수 있음
    - Docker Desktop의 볼륨은 Mac 및 Windows 호스트의 바인드 마운트보다 성능이 훨씬 높음  
  
3일차 정리
# Dockerfile 작성 시 유의사항
## 사용자 입력이 발생하지 않도록, 포그라운드 프로세스로 실행되도록 해야함
- 도커이미지 빌드 후 컨테이너 설치구성을 할 때, 입력을 요구하는 요소가 들어가면 입력해줄 방법이 없다
- 일반 서버의 경우 백그라운드에서 돌아간다(demon) 하지만 컨테이너 안에서 돌고잇는 프로세스는 호스트가 제어권을 가지고 있어야하기 때문에 포그라운드로 돌아가야 한다

## 도커 이미지 크기가 커지지 않도록 유의
- 도커 컨테이너 이미지를 빌드하고 배포하는데 시간을 단축하기 위해서는 도커 이미지 크기를 최소로 유지하는 것이 좋다!
- Dockerfile의 모든 명령어는 레이어를 생성하고, 생성된 레이어는 저장
- 실제 컨테이너에서 사용하지 않는(못 하는) 레이어도 저장공간을 차지하게됨   
&ensp; → 레이어 최소화 불필요한 레이어가 생성되지 않도록 하는 것이 중요!
- **Dockerfile을 작성할 떄 명령어를 묶어서 실행하는 것이 유익**   
&ensp;**→ &&명령어를 사용하여 RUN 명령어를 하나로 묶어서 실행 라인 생성을 최소화**
- **빌더 패턴, 다단계 도커 빌드 등의 기법을 이용해서 이미지를 작게 만들기**
- 불필요한 도구 설치 금지


### 빌더 패턴
최적 크기 도커 이미지를 생성하기 위해 사용하는 방법     
두 개의 도커 이미지를 사용   
- 첫 번째 도커 이미지 → Builder → 소스코드를 실행파일로 만들기 위한 빌드 환경
- 두 번째 도커 이미지 →> Runtime → 첫 번째 도커 컨테이너가 생성한 실행파일을 실행하기 위한 런타임 환경 → 실행 파일, 종속성 및 런타임 도구만 포함
- 첫 번째 도커 컨테이너가 생성한 실행 파일을 두 번째 도커 컨테이너로 전달하는 스크립트가 필요

### 다단계 도커 빌드
Docker 17.05 버전에 새롭게 추가된 기능  
하나의 Dockerfile에 여러개의 FROM문을 사용해 빌드 단계를 정의하고, —from 플래그를 사용해 각 단계에서 생성 된 아티팩트 참조가 가능하도록 하는 것  
도커파일 안에 여러개의 도커 형식이 들어온다 참조를 위해 —from 플래그 사용   
각 단계는 0부터 순서대로 부여된 번호 또는 AS절을 통해 부여한 별칭을 이용가능

## Dockerfile 모범 사례
도커 이미지 빌드 시간 단축, 이미지 크기 감소, 보안 강화 및 유지 관리 가능성을 보장
- 도커 허브 공식 이미지 사용
```
Inefficient Dockerfile
----------------------
FROM ubuntu
RUN  apt-get update && apt-get install -y openjdk-8-jdk

Efficient Dockerfile
----------------------
FROM openjdk 
```
- 특정 버전의 태그를 사용    
latest 태그를 사용시 호환성 문제가 생길 수도 있다
```
Inefficient Dockerfile
----------------------
FROM openjdk:latest

Efficient Dockerfile
----------------------
FROM openjdk:8
```
- 최소 크기의 이미지를 사용    
최소 크기 버전의 부모 이미지(베이스 이미지)를 사용
```
Inefficient Dockerfile
----------------------
FROM openjdk:8                 ⇒ 488MB

Efficient Dockerfile
----------------------
FROM openjdk:8-jre-alpine      ⇒ 84.9MB

```
-  —user(또는 -u) 옵션을 사용
```
C:\docker\multi-state-build> docker container run -it --rm --user=9999 ubuntu
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
445a6a12be2b: Already exists
Digest: sha256:aabed3296a3d45cede1dc866a24476c4d7e093aa806263c27ddaadbdce3c1054
Status: Downloaded newer image for ubuntu:latest
I have no name!@b09ce133830c:/$

I have no name!@b09ce133830c:/$ id
uid=9999 gid=0(root) groups=0(root)
```

- USER 지시문 사용
```
FROM ubuntu
RUN  apt-get update
RUN  useradd demo-user
USER demo-user
CMD  whoami

C:\docker\multi-state-build> docker image build -t user-test .
C:\docker\multi-state-build> docker image ls
REPOSITORY         TAG       IMAGE ID       CREATED          SIZE
user-test          latest    1450e10bb5c1   19 seconds ago   122MB

C:\docker\multi-state-build>docker container run --rm user-test
demo-user

C:\docker\multi-state-build>docker container run -it --rm user-test /bin/bash
demo-user@65cb35edc53e:/$ id
uid=1000(demo-user) gid=1000(demo-user) groups=1000(demo-user)

demo-user@65cb35edc53e:/$ exit
exit
```

4일차 정리
## 빌드 컨텍스트(build context)
- 도커 이미지를 생성하는데 필요한 파일, 소스 코드, 메타 데이터(Dockerfile, .dockerignore 등) 등을 담고 있는 디렉토리
- Dockerfile이 존재하는 디렉토리
- 빌드 컨텍스트는 docker build 명령어의 마지막에 지정한 위치에 있는 파일 전체를 포함(Dockerfile뿐만 아니라 main.go 등의 파일까지!)   
&ensp; → 현재 디렉토리인 경우 . 으로 처리/ 위치가 다르면 그 위치를 지정해야 한다
- 깃 허브와 같은 외부 URL에서 Dockerfile을 읽어들인다면 해당 저장소에 있는 파일과 서브 모듈을 포함
- 단순 파일뿐 아니라 하위 디렉토리도 전부 포함하게 되므로 빌드에 불필요한 파일이 포함된다면 빌드 속도가 저하되고, 호스트의 메모리를 지나치게 점유하는 문제 발생
- .dockerignore 파일을 작성하면 명시된 이름의 파일을 컨텍스트에서 제외 가능
- .dockerignore 파일은 Dockerfile과 동일한 위치에 저장
- .dockerignore 파일은 용량을 줄이는 목적도 있지만 보안적 측면도 있다   
&ensp; ex) 서버가 외부에 있을 때 관련 context를 tar파일로 만들어 전달하는데 이 때 tar파일에 기밀성을 요하는 파일(key파일 암호파일 등)들이 들어가면 안되기 때문에 그런 파일들은 .dockerignore에 추가한다!


