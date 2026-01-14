# Dockerfile 주요 명령

- FROM: 베이스 이미지 지정 명령(예: FROM httpd:alpine)
- LABEL: 버전 정보, 작성자와 같은 이미지 설명을 작성하기 위한 명령(예: LABEL version="1.0.0")
- CMD: docker 컨테이너가 시작할 때, 실행하는 쉘 명령을 지정하는 명령. RUN과 비슷하지만, RUN은 이미지 작성시 실행하는 명령이고, CMD는 컨테이너를 시작할 때 실행하는 명령 (예: CMD ['python', 'app.py'])
- RUN: 쉘 명령을 실행하는 명령 (예: RUN["apt-get", "install", "nginx"]). RUN은 이미지 작성 시 실행되며, 일종의 새로운 이미지 layer를 만드는 역할

# Dockerfile로 이미지 작성

```bash
docker build 옵션 Dockerfile_경로
```
## 주요 옵션

- -t 또는 --tag: 이미지 이름 설정
- -f: 이미지 빌드 시 디폴트로 Dockerfile 파일명으로 된 파일을 찾아서 이미지를 빌드하지만, 이 외의 파일명으로 이미지를 빌드할 경우 해당 옵션을 사용해 파일명 지정 가능
- --pull=true/false: FROM으로 지정된 이미지는 한 번 다운로드하면, 이미지 생성 마다 새로 다운받지 않고, 기존에 다운받은 이미지를 사용하지만, 해당 옵션을 사용하면 이미지 생성마다 새로 다운로드를 받으라는 옵션
