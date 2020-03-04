---
layout: docker-post
title: "docker-compose jenkins issue"
date: 2020-02-27
tags: [docker]
excerpt: ""
docker: true
comments: true
---


# docker-compose 를 이용해서 container를 올린 경우 ISSUE


## 1. Permission denied 에러 해결하기


#### jenkins 컨테이너를 올리기 위한 `docker-compose-jenkins.yml` 파일 내용을 확인한다.
~~~
[root@localhost ~]# cat docker-compose-jenkins.yml
version: '3.3'
services:
  jenkins:
    image: jenkins:latest
    restart: always
    container_name: "jenkins"
    ports:
      - "8080:8080"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /second/ftp_home/docker_data/jenkins_home:/var/jenkins_home
    environment:
      TZ: Asia/Seoul
~~~


#### `docker-compose up` 명령을 사용해서 컨테이너를 올린다.
~~~
[root@localhost ~]# docker-compose -f docker-compose-jenkins.yml up -d
~~~

#### 컨테이너를 확인하다.
~~~
[root@localhost ~]# docker ps -a
CONTAINER ID        IMAGE                     COMMAND                  CREATED             STATUS                          PORTS                                                 NAMES
5a214df5304e        jenkins:latest            "/bin/tini -- /usr..."   2 minutes ago       Restarting (1) 39 seconds ago                                                         jenkins
~~~

#### 컨테이너를 확인하게 되면 생성은 되었지만 실행되고 있지 않는걸 확인할 수 있다. 로그를 보자.
~~~
[root@localhost ~]# docker container logs jenkins
touch: cannot touch '/var/jenkins_home/copy_reference_file.log': Permission denied
Can not write to /var/jenkins_home/copy_reference_file.log. Wrong volume permissions?
~~~

#### Permission denied 에러를 확인할 수 있다.

#### 여기서 `docker-compose-jenkins.yml` 파일의 jenkins 서비스에 user를 추가하자.
~~~
[root@localhost ~]# nano docker-compose-jenkins.yml
version: '3.3'
services:
  jenkins:
    image: jenkins:latest
    restart: always
    container_name: "jenkins"
    user:root
    ports:
      - "8080:8080"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /second/ftp_home/docker_data/jenkins_home:/var/jenkins_home
    environment:
      TZ: Asia/Seoul
~~~

#### 그리고는 기존에 생성한 컨테이너를 삭제한다.
~~~
[root@localhost ~]# docker rm -f jenkins
jenkins
~~~

#### 다시 `docker-compose up` 명령어를 실행해서 container를 생성해서 올려보자.
~~~
[root@localhost ~]# docker-compose -f docker-compose-jenkins.yml up -d
~~~

#### 컨테이너 상태 확인
~~~
[root@localhost ~]# docker ps -a
CONTAINER ID        IMAGE                     COMMAND                  CREATED             STATUS                  PORTS                                                 NAMES
ccc633fedefa        jenkins:latest            "/bin/tini -- /usr..."   4 seconds ago       Up 3 seconds            8080/tcp, 0.0.0.0:8000->8000/tcp, 50000/tcp           jenkins
~~~

#### 컨테이너 로그 확인
~~~
[root@localhost ~]# docker container logs jenkins
~~~

#### 컨테이너 상태와 로그 확인 시 컨테이너가 정상적으로 올라간 걸 확인할 수 있다.


#### 한번 browser를 사용해서 해당 컨테이너에 올라간 jenkins에 접속해 보자.

~~~
http://[컨테이너를 올린 서버의 ip]:8080
~~~

#### 8080 포트 접근시 사이트에 연결할 수 없음 이라면서 접근이 불가능 하다.


## 2. 컨테이너의 포트에 연결할 수 없는 문제 해결하기

#### 문제는 이미지의 도커 파일을 보면 확인할 수 있다.

jenkins 이미지는 Dockerfile 을 사용해서 빌드됐을 것이다.  

jenkins 이미지의 Dockerfile을 확인하기 위해서 [docker hub](https://hub.docker.com/) 에 접속한다.  
접속하게 되면 상단에 검색어를 `jenkins` 로 검색을 한다.  
그러면 jenkins 이미지의 Dowonloads와 Stars 를 가장 많이 받은 Jenkins Image를 확인할 수 있다.  
클릭해서 들어가 보자.  
들어왔으면 아래 내용을 읽어보면 Supported tags and respective Dockerfile links 이라는 타이틀이 있는 부분에  
Dockerfile 링크를 확인할 수 있다.  
해당 링크를 타고 들어가면 github에 올려진 Dockerfile 을 확인할 수 있다.  
여기서 유심히 봐야할 부분은 EXPOSE 부분이다.

`EXPOSE`는 해당 이미지가 host와 통신하기 위해 열어둔 포트를 설정하는 옵션이다.  

`EXPOSE` 부분을 찾아보면 `${http_port}` 와 `${agent_port}` 두개의 설정이 되어 있다.  

주석에 달려있는 내용대로 기본 웹 인터페이스와 연결하므로 `${http_port}` 와 연결되도록 설정을 바꾼다.  

`${http_port}` 의 값은 Dockerfile 상단부 `ARG http_port=8080` 로 8080 포트로 설정되어 있는걸 확인할 수 있다.  

다시 shell 로 돌아와서 `nano docker-compose-jenkins.yml` 명령어를 사용해서 아래와 같이 ports를 수정하자.

~~~
version: '3.3'
services:
  jenkins:
    image: jenkins:latest
    restart: always
    container_name: "jenkins"
    user: root
    ports:
      - "8080:8080"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /second/ftp_home/docker_data/jenkins_home:/var/jenkins_home
    environment:
      TZ: Asia/Seoul
~~~

#### 참고로 ports: 에서 왼쪽은 client 와 연결되는 포트이고 오른쪽은 이미지 내부 포트이다.
#### 이미지 내부포트와 연결해야 하므로 왼쪽은 어떤 포트이든 사용가능한 포트면 상관 없다.
#### 예) 9000:8080, 80:8080, 12000:8080 모두 가능  


#### 다시 컨테이너를 올려보자. 삭제하고 다시 올릴 필요는 없다.
~~~
[root@localhost ~]# docker-compose -f docker-compose-jenkins.yml up -d
~~~


#### 컨테이너 상태 확인
~~~
[root@localhost ~]# docker ps -a
CONTAINER ID        IMAGE                     COMMAND                  CREATED             STATUS                  PORTS                                                 NAMES
642248c36d07        jenkins:latest            "/bin/tini -- /usr..."   45 seconds ago      Up 44 seconds           0.0.0.0:8080->8080/tcp, 50000/tcp                     jenkins
~~~

#### ports 부분을 확인하면 0.0.0.0:8080->8080 부분을 확인할 수 있다.

#### 한번 브라우저로 접속해보자.

~~~
http://[컨테이너를 올린 서버의 ip]:8080
~~~


#### 정상적으로 jenkins 홈페이지에 접속되는 걸 확인할 수 있다.








