# Docker-node

docker 를 이용하여 간단한 node app 실행해보기.

## Getting Started

### Start App

```bash
npm i
npm run start
```

### Using Docker

```bash
docker build ./
docker run -p {browserPort}:{dockerPort} {image}
``` 

### 변경사항이 생길때 마다 image build 막기(volume)

```bash
docker build ./
docker run -d -p {browserPort}:{dockerPort} -v /usr/src/app/node_modules -v $(pwd):/usr/src/app {image}

# 변경 후
docker stop {id}
docker run -d -p {browserPort}:{dockerPort} -v /usr/src/app/node_modules -v $(pwd):/usr/src/app {image}
``` 

- `-d(detach)` 옵션은 docker 실행 후 터미널로 돌아오도록 해주는 옵션.
- COPY 를 사용하여 복사하면 파일이 변경될 때마다 image 를 다시 build 해줘야함. 이때 docker volume 기능을 사용하면
build 를 다시 해주지 않아도 됨. 컨테이너 내부에 파일을 복사해서 이동하는 것이 아닌 로컬 파일을 참조 하는 것.
- -v {제외할 폴더 또는 파일} -v {참조 경로}:{working directory}

-------------------------------------------------

## Base image(FROM)
- node:10 이미지를 사용함.
- npm 이 설치되어 있는 이미지를 사용해야 함.

## COPY
- COPY <파일 경로> <복사할 경로>
- node 이미지 파일 스냅샷에는 현재 디렉토리의 파일(package.json, server.js...) 들이 포함되어
있지 않기 때문에 COPY 명령어를 사용하여 파일들을 도커 컨테이너 안으로 복사함.

## `-p(port)` option 사용이유
- docker 실행 후 브라우저에 실행 port 로 접속해보면 접근이 되지 않음.
- COPY 명령어를 사용하여 파일을 복사해줬던 것 처럼 네트워크도 연결 시켜주어야 함.
- 즉, 도커 컨테이너 내부의 네트워크를 로컬 네트워크에 연결을 시켜줘야 접근 가능함.

## WORKDIR

이미지 안에서 어플리케이션 소스 코드를 갖고 있을 디렉토리를 생성하는 것. 
이 디렉토리가 어플리케이션의 working directory 가 된다.

working directory 가 따로 있어야 하는 이유
- 현재 node 이미지 속의 root 디렉토리에는 home, bin, dev 등의 파일들이 있다.
- 혹시 이 중에서 원래 이미지에 있던 파일과 이름이 같다면 원래있던 것이 덮어씌어져 버린다.
- 모든 파일이 한 디렉토리에 들어가 버리니 깔끔하지 않다.

workdir 을 설정해주면 컨테이너에 접근할때 루트가 아닌 workdir 을 기본으로 접근하게 된다.
