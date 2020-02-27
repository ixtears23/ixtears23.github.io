---
layout: post
title: "Dockerfile reference"
date: 2020-02-27
tags: [docker]
excerpt: ""
docker: true
comments: true
---

[출처:Dockerfile reference](https://docs.docker.com/engine/reference/builder/)

Docker는 Dockerfile에서 지시 사항을 읽음으로써 이미지를 자동으로 빌드 할 수 있습니다. 
Dockerfile은 이미지를 어셈블하기 위해 사용자가 명령 행에서 호출 할 수있는 모든 명령을 포함하는 텍스트 문서입니다. 
`docker build`를 사용하면 여러 명령 행 명령을 연속적으로 실행하는 자동화 된 빌드를 작성할 수 있습니다.  
이 페이지는 Dockerfile에서 사용할 수있는 명령을 설명합니다. 

이 페이지를 다 읽은 후에는 `Dockerfile Best Practices` 를 참조하십시오.  

## Usage 
  
`docker build`명령은 `Dockerfile` 및 컨텍스트에서 이미지를 빌드합니다. 
빌드 컨텍스트는 지정된 위치 `PATH`또는 `URL`에있는 파일 세트입니다. 
`PATH`는 로컬 파일 시스템의 디렉토리입니다. `URL`은 Git 저장소 위치입니다.  

컨텍스트는 재귀 적으로 처리됩니다. 따라서 `PATH`에는 하위 디렉토리가 포함되고 
`URL`에는 리포지토리 및 해당 하위 모듈이 포함됩니다. 
이 예제는 **현재 디렉토리를 컨텍스트로 사용하는 빌드 명령** 을 보여줍니다.  

~~~
$ docker build .
Sending build context to Docker daemon  6.51 MB
...
~~~

`build`는 `CLI`가 아닌 `Docker daemon`에 의해 실행됩니다.  

빌드 프로세스가 수행하는 첫 번째 작업은 전체 컨텍스트를 재귀 적으로 데몬에 보내는 것입니다. 
대부분의 경우 빈 디렉토리를 컨텍스트로 시작하고 
Dockerfile을 해당 디렉토리에 유지하는 것이 가장 좋습니다. 
Dockerfile을 빌드하는 데 필요한 파일 만 추가하십시오.  

> **Warning:** 루트 디렉토리 `/`를 `PATH`로 사용하면
> 빌드가 하드 드라이브의 전체 내용을 Docker 데몬으로 전송하게 되므로
> 루트 디렉토리 `/`를 `PATH`로 사용하지 마십시오.   


빌드 컨텍스트에서 파일을 사용하기 위해 `Dockerfile`은 명령에 지정된 파일 (예:`COPY` 명령)을 참조합니다. 
빌드 성능을 향상 시키려면 컨텍스트 디렉토리에 `.dockerignore` 파일을 추가하여 
파일 및 디렉토리를 제외하십시오.   

전통적으로 `Dockerfile`은 `Dockerfile`이라고 하며 컨텍스트의 루트에 있습니다.
`docker build`와 함께 `-f` 플래그를 사용하면 파일 시스템의 어느 곳에서나 Dockerfile을 가리킬 수 있습니다.

~~~
$ docker build -f /path/to/a/Dockerfile .
~~~

빌드가 성공하면 새 이미지를 저장할 저장소 및 태그를 지정할 수 있습니다.  

~~~
$ docker build -t shykes/myapp .
~~~


빌드 후 이미지를 여러 저장소에 태그하려면 `build` 명령을 실행할 때 여러 개의 `-t` 매개 변수를 추가하십시오.  

~~~
$ docker build -t shykes/myapp:1.0.2 -t shykes/myapp:latest .
~~~

Docker 데몬은 `Dockerfile`에서 지시 사항을 실행하기 전에
`Dockerfile`의 예비 유효성 검증을 수행하고 구문이 올바르지 않으면 오류를 리턴합니다.  

~~~
$ docker build -t test/myapp .
Sending build context to Docker daemon 2.048 kB
Error response from daemon: Unknown instruction: RUNCMD
~~~

Docker 데몬은 `Dockerfile`에서 명령을 하나씩 실행하여 필요한 경우
새 이미지의 ID를 출력하기 전에 각 명령의 결과를 새 이미지에 커밋합니다.
Docker 데몬은 보낸 컨텍스트를 자동으로 정리합니다.  

각 명령어는 독립적으로 실행되며 새 이미지가 만들어 지므로 `RUN cd /tmp`는 다음 명령어에 영향을 미치지 않습니다.  

Docker는 가능할 때마다 중간 이미지 (캐시)를 재사용하여 `docker build` 프로세스를 크게 가속화 합니다.
이는 콘솔 출력에서 `Using cache` 메시지로 표시됩니다.  

~~~
$ docker build -t svendowideit/ambassador .
Sending build context to Docker daemon 15.36 kB
Step 1/4 : FROM alpine:3.2
 ---> 31f630c65071
Step 2/4 : MAINTAINER SvenDowideit@home.org.au
 ---> Using cache
 ---> 2a1c91448f5f
Step 3/4 : RUN apk update &&      apk add socat &&        rm -r /var/cache/
 ---> Using cache
 ---> 21ed6e7fbb73
Step 4/4 : CMD env | grep _TCP= | (sed 's/.*_PORT_\([0-9]*\)_TCP=tcp:\/\/\(.*\):\(.*\)/socat -t 100000000 TCP4-LISTEN:\1,fork,reuseaddr TCP4:\2:\3 \&/' && echo wait) | sh
 ---> Using cache
 ---> 7ea8aef582cc
Successfully built 7ea8aef582cc
~~~

빌드 캐시는 로컬 상위 체인이있는 이미지에서만 사용됩니다.
이는 이러한 이미지가 이전 빌드에서 생성되었거나 전체 이미지 체인에 `docker load`가로드되었음을 의미합니다.
특정 이미지의 빌드 캐시를 사용하려면 `--cache-from` 옵션으로 이미지를 지정할 수 있습니다.
`--cache-from`으로 지정된 이미지에는 상위 체인이 필요하지 않으며 다른 레지스트리에서 가져올 수 있습니다.  


빌드가 끝나면 저장소를 레지스트리에 푸시할 준비가되었습니다.  

......













