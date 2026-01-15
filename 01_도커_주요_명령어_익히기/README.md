# Docker Container 관련 주요 명령

## 컨테이너 생성, 시작, 실행
---
```bash
$ docker create ubuntu # docker create [원하는 컨테이너 이름] [이미지 이름]
$ docker start my-ubuntu # docker start [컨테이너 이름]
$ docker run -it ubuntu # docker run [이미지 이름]
```

- create는 오로지 컨테이너 '생성만'
- start는 이미 '생성된'컨테이너를 '시작'
- run은 '생성 및 시작'을 '한 번에'처리

### docker run 주요 옵션
- -i: 컨테이너 입력(STDIN)을 열어놓는 옵션(주로 -t와 함께 사용됨)
- -t: 가상 터미널(tty)을 할당하는 옵션(주로 -i와 함께 사용됨)
- --name: 컨테이너 이름을 설정하는 옵션
- -d: 컨테이너를 백그라운드에서 실행하는 옵션
- --rm: 컨테이너 종료시 컨테이너를 자동으로 삭제하는 옵션
- -p: 호스트와 컨테이너 포트를 연결하는 옵션
- -v: 호스트와 컨테이너 디렉토리를 연결하는 옵션

## Docker Container 관련 주요 명령
- docker ps: 현재 실행 중인 컨테이너 확인
- docker ps -a: 실행 중이지 않은 컨테이너까지 포함해서, 전체 컨테이너 확인
- docker rm [삭제할 컨테이너 이름 또는 id]: 컨테이너 삭제
- docker container stats: 실행 중인 컨테이너 사용 리소스 확인
- docker exec [옵션] [컨테이너 이름 or id] [명령 인자]: 실행 중인 컨테이너에 명령 실행
  - docker exec 같은 경우엔 백그라운드로 httpd 웹 서버 컨테이너를 띄워놓고 이 명령으로 접속해 명령을 실행할 수 있음
  - 예시: docker exec -it apacheweb /bin/sh


