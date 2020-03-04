---
layout: docker-post
title: "Docker jenkins update"
date: 2020-02-27
tags: [docker,jenkins]
excerpt: ""
docker: true
comments: true
---


# Docker jenkins update 방법

docker jenkins 를 설치하고 나면...

브라우저로 jenkins에 접속 한 뒤 jenkins 관리에 보면 빨간줄이 주루루루룩 뜨는것을 볼 수 있다.


### jenkins war 파일 링크 복사


여기서 !!

아래에 보다보면 다운로드 하는 부분을 볼 수 있다. 아쉽지만 캡처를 하지 못함.

해당 링크를 클릭하면 war 파일을 다운로드 받는데.. 여기서 다운로드 받지 말고

해당 링크 주소를 복사해 둔다.  


컨테이너를 올릴 때 docker-compose.yml 파일에 user: root 로 잡아서 그런지(추측임..)

### jenkins 컨테이너 내부의 쉘 진입

~~~
docker container exec -u 0 -it jenkins bash
~~~

다른 안내문들을 보면 `-u 0` 을 붙여서 root 계정으로 접속하라고 말한다.  

여하튼 실행중인 컨테이너의 bash shell에 접속한다.  


여기서 아까 복사해 둔 war파일을 다운로드한다. 위치는 어디든 상관없다. 어차피 이동시킬 것이기 때문에. 


### update version의 war 파일 다운로드

~~~
wget 아까복사한linkurl
~~~

`wget` 는 인터넷에서 파일을 다운로드 하기 위한 명령 행 유틸이다.  


다운로드가 완료되면 다운로드 받은 war 파일을 이동시킨다.  


### war 파일 이동

~~~
mv ./jenkins.war /usr/share/jenkins
~~~


파일은 이동했지만. 파일 소유자와 소유그룹을 변경해 줘야 한다.  
파일 소유자와 소유그룹을 변경해 주지 않으면 컨테이너를 다시 재시작해도 update 되지 않는다.  

###  파일 소유자 소유그룹 변경

~~~
chown jenkins:jenkins /usr/share/jenkins/jenkins.war
~~~

### bash shell 빠져 나가기

~~~
exit
~~~


### container 재시작

~~~
docker restart jenkins
~~~

### update 확인

브라우저에  다시 접속해서 확인해보면 update가 된 것을 확인할 수 있다.  







