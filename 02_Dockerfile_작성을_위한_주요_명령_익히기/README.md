# Dockerfile 주요 명령

- FROM: 베이스 이미지 지정 명령(예: FROM httpd:alpine)
- LABEL: 버전 정보, 작성자와 같은 이미지 설명을 작성하기 위한 명령(예: LABEL version="1.0.0")
- CMD: docker 컨테이너가 시작할 때, 실행하는 쉘 명령을 지정하는 명령. RUN과 비슷하지만, RUN은 이미지 작성시 실행하는 명령이고, CMD는 컨테이너를 시작할 때 실행하는 명령 (예: CMD ['python', 'app.py'])
- RUN: 쉘 명령을 실행하는 명령 (예: RUN["apt-get", "install", "nginx"]). RUN은 이미지 작성 시 실행되며, 일종의 새로운 이미지 layer를 만드는 역할

# Dockerfile로 이미지 작성

```bash
$ docker build 옵션 Dockerfile_경로
```

## 주요 옵션

- -t 또는 --tag: 이미지 이름 설정
- -f: 이미지 빌드 시 디폴트로 Dockerfile 파일명으로 된 파일을 찾아서 이미지를 빌드하지만, 이 외의 파일명으로 이미지를 빌드할 경우 해당 옵션을 사용해 파일명 지정 가능
- --pull=true/false: FROM으로 지정된 이미지는 한 번 다운로드하면, 이미지 생성 마다 새로 다운받지 않고, 기존에 다운받은 이미지를 사용하지만, 해당 옵션을 사용하면 이미지 생성마다 새로 다운로드를 받으라는 옵션

## LABEL

- LABEL은 [key]=[value] 형식으로 메타 데이터들을 넣을 수 있는 기능
- 보통 저자, 버전, 설명, 작성일자 등을 각각 key 이름을 정하고, 값을 넣는 경우가 있음

```bash
FROM httpd:alpine
LABEL maintainer="zop1234@hanmail.net"
LABEL version="1.0.0"
LABEL description="A test docker image to understand Docker"
```

- 테스트

```bash
$ docker build --tag myweb .
```

## COPY
```bash
FROM httpd:alpine
LABEL maintainer="zop1234@hanmail.net"
LABEL version="1.0.0"
LABEL description="A test docker image to understand Docker"

COPY ./2021_DEV_HTML /usr/local/apache2/htdocs
```

- 이미지 빌드 및 실행

```bash
# Dockerfile이 있는 폴더에서 myweb 이미지 이름으로 이미지 빌드
$ docker build --tag myweb ./

# myweb 이미지를 포트 매핑한 상태로, 백그라운드 실행 (컨테이너명은 apacheweb)
$ docker run -d -p 9999:80 --name apacheweb myweb

- 이미지 조사하기

```bash
$ docker inspect myweb

"Config": {
  "Cmd": [
    "httpd-foreground"
  ],
  "Labels": {
    "description": "A test docker image to understand Docker",
    "maintainer": "zop1234@hanmail.net",
    "version": "1.0.0"
  }
}
```

## CMD

- 다음 세 가지 형태로 CMD 명령 작성 가능
  - 명령어, 인자를 리스트처럼 작성하는 형태(해당 방식을 도커에선 추천)
  ```bash
  CMD ["executable", "param1", "param2", ...]

  # 예
  CMD ["/bin/sh", "-c", "echo", "Hello"]
  ```

  - ENTRYPOINT 명령어에 인자를 리스트처럼 작성하는 형태
  ```bash
  CMD ["param1", "param2", ...]
  ```

  - 쉘 명령처럼 작성하는 형태
  ```bash
  CMD <command> <param1> <param2> ...
  ```

- CMD는 하나의 Dockerfile에서 한 가지만 설정되며, 만약 CMD 설정이 여러 개일 경우, 맨 마지막에 설정된 CMD 설정만 적용됨

- httpd:alpine 기반 Dockerfile 작성하기
```bash
FROM httpd:alpine
LABEL maintainer="zop1234@hanmail.net"
LABEL version="1.0.0"
LABEL description="A test docker image to understand Docker"

COPY ./2021_DEV_HTML /usr/local/apache2/htdocs

CMD ["/bin/sh", "-c", "httpd-foreground"]
```

- 컨테이너 에러 또는 출력 결과 확인하기
```bash
$ docker logs [컨테이너ID_또는_이름]

# 예
$ docker logs httpdweb

- CMD 변경해보기

```bash
FROM httpd:alpine
LABEL maintainer="zop1234@hanmail.net"
LABEL version="1.0.0"
LABEL description="A test docker image to understand Docker"

COPY ./2021_DEV_HTML /usr/local/apache2/htdocs

CMD ["/bin/sh"]
```

``` bash
# 이미지 생성
$ docker build --tag myweb2 ./

# -dit 옵션으로 터미널 붙이고, 백그라운드 실행 후 포트 붙이고, 컨테이너 중지시 삭제
$ docker run -dit -p 9999:80 --name httpdweb2 --rm myweb2
```

- 이렇게 하게 되면 로컬 웹 사이트 접속이 안됨
- httpd는 기본적으로 실행되면 첫 번째로 httpd-foreground 명령어를 실행해서 웹 서버를 띄우지만, CMD 변경으로 인해 터미널 실행만 하고 종료됨

- CMD 명령 덮어씌우기

```bash
$ docker run -dit -p 9999:80 --name httpdweb2 --rm myweb2 /bin/sh -c httpd-foreground
```

## ENTRYPOINT
- ENTRYPOINT는 docker run 시에 함께 실행해야 하는 명령을 기입할 때 사용
- CMD와 차이는 우선 순위가 ENTRYPOINT가 더 높음
- CMD 명령에 덮어씌워지지 않기 위해 사용

```bash
FROM httpd:alpine

LABEL maintainer="zop1234@hanmail.net"

COPY ./2021_DEV_HTML /usr/local/apache2/htdocs

ENTRYPOINT ["/bin/sh"]
```
- docker inspect 명령을 통해 확인해 보면 Entrypoint 부분에 들어가 있는 것을 확인할 수 있음


## RUN
- docker는 이미지 생성시, 각 단계를 layer로 나누어 작성함
- RUN 명령은 이미지 생성 시, 일종의 layer를 만들 수 있는 명령으로, 보통 베이스 이미지에 패키지(프로그램)을 설치하여 새로운 이미지를 만들 때 많이 사용

### 예: ubuntu 18.04 버전에 apache2를 설치 후 나만의 웹 복사 후 웹서버 구동

```bash
FROM ubuntu:18.04
LABEL maintainer="zop1234@hanmail.net"

RUN apt-get update
RUN apt-get install -y apache2 apt-utils

COPY ./2021_DEV_HTML /var/www/html/

ENTRYPOINT ["usr/sbin/apache2ctl", "-D", "FOREGROUND"]
```