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
