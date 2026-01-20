# docker 조사하기

## docker history
- 이미지 히스토리 확인
```dockerfile
FROM ubuntu:18.04
LABEL maintainer="zop1234@hanmail.net"

RUN apt-get update
RUN apt-get install -y apache2

COPY ./2021_DEV_HTML /var/www/html

ENTRYPOINT ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
```

```bash
# 이미지 생성
$ docker build --tag myweb -f Dockerfile-ubuntu ./

# 이미지 히스토리 확인
$ docker history myweb
```

- 실행해 보면 FROM, RUN, COPY, ENTRYPOINT 등 명령에 따라 layer가 생성됨을 확인할 수 있음

## docker cp
- 컨테이너에서 특정 파일을 호스트 PC로 가져오는(꺼내오는) 명령
- 특정 파일 확인을 위해 활용

```bash
# 컨테이너 작성
$ docker run -d -p 9999:80 --name mywebserver --rm myweb

# apache2 설정 파일 가져오기
$ docker cp mywebserver:/etc/apache2/sites-available/000-default.conf ./
```

- 반대로 호스트 PC에서 컨테이너에 특정 파일을 넣을 수도 있음

```
# 000-default.conf 파일의 다음 부분을
DocumentRoot /var/www/html

# 다음과 같이 변경 및 저장
DocumentRoot /var/www/html/xxx
```

```bash
# 컨테이너에 넣기
$ docker cp ./000-default.conf mywebserver:/etc/apache2/sites-available/000-default.conf

# 컨테이너 접속하기
$ docker exec -it mywebserver /bin/bash

# 컨테이너 안에서 다음 명령으로 변경된 파일이 들어갔음을 확인
$ cd /etc/apache2/sites-available/
$ cat 000-default.conf

# apache2 재실행
$ apache2ctl restart

# localhost:9999 접속 후 Not Found 에러 확인
$ exit
```

## docker commit
- 컨테이너 변경사항을 이미지 파일로 생성

```bash
$ docker commit 옵션 컨테이너ID_또는_이름 이미지이름[:태그]
```

```bash
# 이미 있는 이미지 이름을 넣으면 덮어 씌워짐
# 다른 이미지명을 넣으면 해당 이미지명으로 별도로 생성됨
# 보통 Dockerfile-dev, Dockerfile-prod 같은 형태로 개발용/서비스용으로 나눌 때 사용하기도 함
$ docker commit -m "add vim" mywebserver new_myweb

# 이미지 history 확인 (ENTRYPOINT 상단에 layer가 추가된 것을 확인할 수 있음)
$ docker history new_mywebserver
```

```bash
# 현재 모든 컨테이너 중지 및 삭제
$ docker stop $(docker ps -a -q)
$ docker rm $(docker ps -a -a)

# 새로운 이미지로 컨테이너 실행
$ docker run -d -p 9999:80 --name mywebserver new_myweb
$ docker exec -it mywebserver /bin/bash
$ cd /var/log/

# localhost:9999 접속 후 access.log 확인
$ cat access.log

# 종료
$ exit
```

## docker diff
- 컨테이너가 실행되면서 본래의 이미지와 비교해 변경된 파일 목록 출력
- 기호
  - A: 파일 또는 디렉토리 추가
  - B: 파일 또는 디렉토리 삭제
  - C: 파일 또는 디렉토리 수정

```bash
# new_myweb 이미지로부터 생성된 myweb 가 실행되면서 지금까지 변경된 내역을 보여줌
$ docker diff myweb
```

## docker inspect
- 이미지와 컨테이너 세부 정보 확인

## docker logs
- 컨테이너의 출력결과(STDOUT)를 확인