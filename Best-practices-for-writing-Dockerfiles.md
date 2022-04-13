# Best practices for writing Dockerfiles

[https://docs.docker.com/develop/develop-images/dockerfile_best-practices/](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

- Instruction → Layer; 이전 layer의 delta가 stacked
- 📝
    - [ ]  

### ****Create ephemeral containers****

“Ephemeral”

- 중단, 삭제된 후 최소 준비와 설정으로 재생성(rebuild) 될 수 있어야 한다
- Stateless; [https://12factor.net/ko/processes](https://12factor.net/ko/processes)

### ****Understand build context****

Build Context

- `docker build` 인자
    - Dockerfile은 기본 현재(current) 디렉토리라고 생각함
    - `-f` 옵션으로 지정할 수도 있음
- Dockerfile 위치와 관계 없이, 모든 현재 하위(recursive) 파일, 디렉토리가 도커 대몬(엔진)에 전달됨
    - build 로그에서 큰 용량이 전달되는지 확인할 것
    - 📝
        - 예제처럼 (COPY 하거나) 사용되는 경우만 전달되는것 같다
        - 같은 컨텍스트의 파일은 이미지 레이어가 아닌 도커 대몬이 캐시하는거 같다.
            - 한번 보낸 컨텍스트를, `--no-cache` 를 쓰더라도, 다시 전송하지 않는다.
            - 파일을 새로 만들면 다시 전송한다
        - 의문이 많아서 질문함: [https://stackoverflow.com/questions/71838211/docker-build-context-when-it-transferred-and-does-it-cached-by-the-runtime](https://stackoverflow.com/questions/71838211/docker-build-context-when-it-transferred-and-does-it-cached-by-the-runtime)
        
        ```go
        $ mkdir -p contexts/small contexts/large
        $ cat Dockerfile
        FROM alpine:3.15.4
        COPY file /file
        
        $ docker build --no-cache -f Dockefile -t small contexts/small
        [+] Building 1.2s (7/7) FINISHED
         => [internal] load build definition from Dockefile                                                            0.0s
         => => transferring dockerfile: 76B                                                                            0.0s
         => [internal] load .dockerignore                                                                              0.0s
         => => transferring context: 2B                                                                                0.0s
         => [internal] load metadata for docker.io/library/alpine:3.15.4                                               1.0s
         => [internal] load build context                                                                              0.0s
         => => transferring context: 23B                                                                               0.0s
         => CACHED [1/2] FROM docker.io/library/alpine:3.15.4@sha256:4edbd2beb5f78b1014028f4fbb99f3237d9561100b6881aa  0.0s
         => [2/2] COPY file /file                                                                                      0.0s
         => exporting to image                                                                                         0.0s
         => => exporting layers                                                                                        0.0s
         => => writing image sha256:c6aea094f2750789c9780f658127bab7fc2b2b38850c6d9c3eea9a5246ed2f4a                   0.0s
         => => naming to docker.io/library/small   
        
        $ docker build --no-cache -f Dockefile -t large contexts/large --progress=plain
        #1 [internal] load build definition from Dockefile
        #1 sha256:8d2344eab81610ffce02dcd604dfe31c76eb2e996b8fcc9eeda2353b7a5a69fb
        #1 transferring dockerfile: 35B done
        #1 DONE 0.0s
        
        #2 [internal] load .dockerignore
        #2 sha256:09382d23523ee21758f6cef3a54669247c7df1d15bb351de9197cb038e57a371
        #2 transferring context: 2B done
        #2 DONE 0.0s
        
        #3 [internal] load metadata for docker.io/library/alpine:3.15.4
        #3 sha256:06edcfaac690ebf32125388172af4c413e1aa88e77652c8af884b4ece9954b22
        #3 DONE 0.9s
        
        #4 [1/2] FROM docker.io/library/alpine:3.15.4@sha256:4edbd2beb5f78b1014028f4fbb99f3237d9561100b6881aabbf5acce2c4f9454
        #4 sha256:414983e10d597598ab8c0f573fc17cb2e87fff932b8c5a6b0b3a48d55a35e7a2
        #4 CACHED
        
        #5 [internal] load build context
        #5 sha256:ae2c66b2a66f36cac93166cf5c8f1d035267077db9b8d5505fda64dda97895b8
        #5 transferring context: 28B done                                                                    0.0s
        ```
        
    
    ```bash
     Sending build context to Docker daemon xxx.xMB
    # 로그가 바뀌었다
    => [internal] load build context                                                                              2.7s
    => => transferring context: 104.88MB
    ```
    
- Dockerfile 위치와 관계 없이, 모든 현재 하위(recursive) 파일, 디렉토리가 도커 대몬(엔진)에 전달됨
    - build 로그에서 큰 용량이 전달되는지 확인할 것
    - 📝
        - 예제처럼 (COPY 하거나) 사용되는 경우만 전달되는것 같다
        - 같은 컨텍스트의 파일은 이미지 레이어가 아닌 도커 대몬이 캐시하는거 같다.
            - 한번 보낸 컨텍스트를, `--no-cache` 를 쓰더라도, 다시 전송하지 않는다.
            - 파일을 새로 만들면 다시 전송한다
        - 의문이 많아서 질문함: [https://stackoverflow.com/questions/71838211/docker-build-context-when-it-transferred-and-does-it-cached-by-the-runtime](https://stackoverflow.com/questions/71838211/docker-build-context-when-it-transferred-and-does-it-cached-by-the-runtime)
        
        ```go
        $ mkdir -p contexts/small contexts/large
        $ cat Dockerfile
        FROM alpine:3.15.4
        COPY file /file
        
        $ docker build --no-cache -f Dockefile -t small contexts/small
        [+] Building 1.2s (7/7) FINISHED
         => [internal] load build definition from Dockefile                                                            0.0s
         => => transferring dockerfile: 76B                                                                            0.0s
         => [internal] load .dockerignore                                                                              0.0s
         => => transferring context: 2B                                                                                0.0s
         => [internal] load metadata for docker.io/library/alpine:3.15.4                                               1.0s
         => [internal] load build context                                                                              0.0s
         => => transferring context: 23B                                                                               0.0s
         => CACHED [1/2] FROM docker.io/library/alpine:3.15.4@sha256:4edbd2beb5f78b1014028f4fbb99f3237d9561100b6881aa  0.0s
         => [2/2] COPY file /file                                                                                      0.0s
         => exporting to image                                                                                         0.0s
         => => exporting layers                                                                                        0.0s
         => => writing image sha256:c6aea094f2750789c9780f658127bab7fc2b2b38850c6d9c3eea9a5246ed2f4a                   0.0s
         => => naming to docker.io/library/small   
        
        $ docker build --no-cache -f Dockefile -t large contexts/large --progress=plain
        #1 [internal] load build definition from Dockefile
        #1 sha256:8d2344eab81610ffce02dcd604dfe31c76eb2e996b8fcc9eeda2353b7a5a69fb
        #1 transferring dockerfile: 35B done
        #1 DONE 0.0s
        
        #2 [internal] load .dockerignore
        #2 sha256:09382d23523ee21758f6cef3a54669247c7df1d15bb351de9197cb038e57a371
        #2 transferring context: 2B done
        #2 DONE 0.0s
        
        #3 [internal] load metadata for docker.io/library/alpine:3.15.4
        #3 sha256:06edcfaac690ebf32125388172af4c413e1aa88e77652c8af884b4ece9954b22
        #3 DONE 0.9s
        
        #4 [1/2] FROM docker.io/library/alpine:3.15.4@sha256:4edbd2beb5f78b1014028f4fbb99f3237d9561100b6881aabbf5acce2c4f9454
        #4 sha256:414983e10d597598ab8c0f573fc17cb2e87fff932b8c5a6b0b3a48d55a35e7a2
        #4 CACHED
        
        #5 [internal] load build context
        #5 sha256:ae2c66b2a66f36cac93166cf5c8f1d035267077db9b8d5505fda64dda97895b8
        #5 transferring context: 28B done                                                                    0.0s
        ```
        
    
    ```bash
     Sending build context to Docker daemon xxx.xMB
    # 로그가 바뀌었다
    => [internal] load build context                                                                              2.7s
    => => transferring context: 104.88MB
    ```
    

### ****Pipe Dockerfile through `stdin`**

- Dockefile + build context를 stdin으로 보낼 수 있다.
    - Dockerfile도 build context이다.
    - Dockerfile은 최소한의 build context이다.

```bash
$ echo -e 'FROM busybox\nRUN echo "hello world"' | docker build -
# 또는
docker build -<<EOF
FROM busybox
RUN echo "hello world"
EOF
```

- 하이픈(`-`)은 PATH 위치를 받는다
- Dockerfile외의 build context가 필요 없을 때 유용하다
    - 또는 .dockerignore를 통해 필요 없는 build context를 제외하여 빌드 속도를 올릴 수 있다
- `docker build [OPTIONS] -f- PATH`
    - PATH에 로컬 또는 remote 경로 둘 다 올 수 있다
    - remote 를 쓸 경우, 로컬에 git clone 후 도커 대몬에 보낸다

### ****Exclude with .dockerignore****

[https://docs.docker.com/engine/reference/builder/#dockerignore-file](https://docs.docker.com/engine/reference/builder/#dockerignore-file)

### ****Use multi-stage builds****

- 이미지 크기를 크게 줄여준다.
    - 과거엔 필요한 instructions 이지만 이미지 레이어엔 포함시키고 싶지 않은 것들을 어렵게 처리함
    - 대표적으로 앱 빌드와 실행 중 이미지엔 실행만 하고 싶은 경우이다
- Inst in Dockerfile: Order them from the less frequently changed to the more frequently changed
    - Install tools you need to build your application
    - Install or update library dependencies
    - Generate your application

[****Use multi-stage builds****](https://www.notion.so/Use-multi-stage-builds-71e178a836de42f2a8ae29dcac496d56)

### ****Don’t install unnecessary packages****

### ****Decouple applications****

- 그러나 multiple worker processes, one process per request 인것도 있다.

### ****Minimize the number of layers****

- Only the instructions `RUN`, `COPY`, `ADD` create layers. Other instructions create temporary intermediate images, and do not increase the size of the build.

### ****Sort multi-line arguments****

### **Leverage build cache**

- 캐시 규칙
    - 캐시 된 부모 이미지에서 시작한다면, 다음 명령을 모든 파상된 자식 이미지와 비교한다.
        - 같은 명령으로 빌드하는지 본다?
        - 아니면 캐시 깬다
    - 대부분은 자식 이미지 하나만 비교해도 되지만, 그렇지 않은 경우가 있음
    - `ADD` , `COPY` 는 파일 내용을 보고 체크섬을 각 파일에 계산해둔다.
        - last-modified 와 last-accessed time은 체크섬에 포함되지 않는다.
    - 이 체크섬으로 캐시 깰지 여부를 확인
    - 컨테이너 내의 파일은 캐시 체크섬(여부)의 확인 대상이 아니다
        - e.g. `RUN apt-get -y update`  의 빌드 시점에서 어떤 파일을 받는지 확인 안한다. 인자 문자열 자체만 캐시 대상이다
- 캐시가 깨지면 이어지는 Dockerfile내의 명령은 모두 새 이미지를 만든다.

---

## ****Dockerfile instructions****

### ****FROM****

- official, alpine을 쓸 것

### LABEL

- 역할
    - 프로젝트 별로 정리
    - 라이선스 기록
    - 자동화
    - ...
- key val로 정의
- string 값은 quoted 또는 escaped 되어야 함
- 한 줄에 multi key val을 쓰거나 \ 로 개행 후 여러개 쓰는 것이 가능하다

### RUN

- 여러 run 은 한 줄에 쓰고, 여러 줄에 \ 로 나눠라
- apt-get
    - “cache-busting”
    - update && install ... (apt list 삭제)
    - update, install 라인을 따뤄 뒀을 시 캐시 레이어를 사용한다면 update가 적용되지 않을 수 있음
    - 또는 패키지 버전을 고정하여 설치
        - update + 1.1.* 처럼 쓸 수도 있음(like pessimitic operator)
    - apt list 삭제 → 이미지 크기 줄이기
- Using pipes
    - `set -o pipefail && ... | ...` : 모든 파이프 명령 성공 확인
        - 모든 쉘 지원하는 옵션이 아니다. bash는 문제 없는 듯, 확실하게 하려면 `/bin/bash -c "set -o pipefail && ... | ... "`
    - Docker는 interpreter 로 `sh -c` 를 통해 RUN 실행해서, 파이프 마지막 명령의 성공(종료 코드)여부만 확인한다. 전 파이프 명령이 성공했는지 확인하지 않는다

### CMD

- CMD ["executable", "param1", "param2"…] 형태
- interative shell이 올 수도 있다
- ENTRYPOINT와 함께 쓰이면 CMD  ["param1", "param2"…]

### EXPOSE

- 컨테이너가 listen하는 포트이기 때문에 traditional(well known)포트를 써야 한다
    - e.g. httpd 80, mongo 27017

### ENV

- RUN에서 사용하려면 ENV 대신 export && cmd && unset 사용할 것
    - 다른 레이어에서 unset하는 것은 효과가 없다

```bash
$ cat Dockerfile
# syntax=docker/dockerfile:1
FROM alpine
ENV ADMIN_USER="mark"
RUN echo $ADMIN_USER > ./mark
RUN unset ADMIN_USER

$ docker build -t test:before .

$ docker run --rm test sh -c 'echo $ADMIN_USER'

mark

$ cat Dockerfile 
# syntax=docker/dockerfile:1
FROM alpine
RUN export ADMIN_USER="mark" \
    && echo $ADMIN_USER > ./mark \
    && unset ADMIN_USER

$ docker build -t test:after .

$ docker run --rm test sh -c 'echo $ADMIN_USER'

```

### ****ADD or COPY****

- prefer COPY over ADD
    - 더 투명하다(transparent)
    - COPY: 로컬 파일을 복사하는 일만한다
    - ADD: 추가적인 기능
        - (로컬에서만) **tar 추출** → 이 용도로만 쓰는 것이 좋다
        - 원격 URL에서 복사
- COPY는 컨텍스트마다 각각 써서(최대한 잘게 쪼개서) cache 무효화를 줄일 것
    - 미리 /tmp 를 전부 복제하는것 보다, 캐시가 깨질 수도 있는 requirements 설치 후 나머지를 COPY 하는 것 캐시 효율적

```bash
COPY requirements.txt /tmp/
RUN pip install --requirement /tmp/requirements.txt
COPY . /tmp/
```

- ADD의 원격 URL을 fetch하는 기능은 웬만하면 쓰지 말 것 → RUN curl, wget으로 대체
    - 어차피 후에 RUN을 통해 사용될 것이면 레이어를 하나 줄일 수 있고(curl ... && 을 통해)
    - 받은 후 바로 지울 수도 있다

### ENTRYPOINT

- 이미지의 **메인 명령**으로 쓰는 것이 최고
    - CMD는 default flag(param)
- `docker run` 실행 시 command(args)가 CMD 치환
- helper script를 호출하는 패턴

```bash
#!/bin/bash
# docker-entrypoint.sh
set -e

if [ "$1" = 'postgres' ]; then
    chown -R postgres "$PGDATA"

    if [ -z "$(ls -A "$PGDATA")" ]; then
        gosu postgres initdb
    fi

    exec gosu postgres "$@"
fi

exec "$@"
```

```docker
COPY ./docker-entrypoint.sh /
ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["postgres"]
```

```bash
$ docker run postgres # default
$ docker run postgres postgres --help # print helps
$ docker run --rm -it postgres bash # run a shell
```

### VOLUME

- DB storage
- conf
- files/directory where conainter would create
- mutable part / user-serviceable part

### USER

- privilege가 필요 없는 서비스를 실행한다면 non root 유저로 설정할 것
- 시작 시 필요한 유저와 그룹 추가
- adduser, addgroup(alpine, busybox 1.34.1은 useradd, usergroup을 제공하지 않는다)
- [https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#user](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#user)
- sudo 사용하지 말것; 문제될 수 있는 행동
    - 설치 시
    - TTY
    - 시그널 포워딩
- USER 스위칭을 줄여 레이어 복잡도를 낮출 것

### WORKDIR

- 절대경로 사용할 것
- RUN cd 는 쓰지 말 것

### ONBUILD

...?