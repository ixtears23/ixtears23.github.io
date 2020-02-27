---
layout: post
title: "Docker-compose의  up, run, start의 차이?"
date: 2020-02-26
tags: [docker]
excerpt: ""
docker: true
comments: true
---

[출처:whats-the-difference-between-up-run-and-start](https://docs.docker.com/compose/faq/#whats-the-difference-between-up-run-and-start)

일반적으로 `docker-compose up`을 원합니다. 
`docker-compose.yml`에 정의 된 모든 서비스를 시작하거나 다시 시작하려면 `up`을 사용하십시오. 
기본 "attached" 모드에서는 모든 컨테이너의 모든 로그가 표시됩니다. 
"detached"모드 (`-d`)에서 컨테이너를 시작한 후 작성이 종료되지만 컨테이너는 백그라운드에서 계속 실행됩니다.  

`docker-compose run` 명령은 “일회성” 또는 “특별” 작업을 실행하기위한 것입니다. 
실행하려는 서비스 이름이 필요하며 실행중인 서비스가 의존하는 서비스의 컨테이너 만 시작합니다.  

`run`을 사용하여 테스트를 실행하거나 데이터 볼륨 컨테이너에 데이터 제거 또는 추가와 같은 관리 작업을 수행하십시오.  

`run` 명령은 컨테이너에 대화식 터미널을 열고 
컨테이너의 프로세스 종료 상태와 일치하는 종료 상태를 반환한다는 점에서 `docker run -ti`와 같이 작동합니다.  

`docker-compose start` 명령은 이전에 생성되었지만 중지 된 컨테이너를 다시 시작하는 데만 유용합니다. 
새로운 컨테이너를 생성하지 않습니다.  




