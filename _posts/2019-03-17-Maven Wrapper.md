---
layout: maven-post
title: "플러그인 사용해서 maven wrapper 생성"
date: 2019-03-17
excerpt: ""
tags: [maven]
maven: true
comments: true
---


Maven 래퍼는 Maven의 특정 버전을 필요로하는 프로젝트 (또는 Maven을 전혀 설치하지 않으려는 사용자)를 위한 훌륭한 선택입니다.  
운영 체제에 여러 버전을 설치하는 대신 프로젝트 별 래퍼 스크립트를 사용할 수 있습니다.  


기존 Maven 프로젝트에 Maven 래퍼를 설정하는 방법을 설명하겠습니다.  

가장 간단한 방법은 적절한 플러그인을 사용하여 플러그인을 자동화하거나 수동 설치를 적용하는 것입니다.  


[takari-maven-plugin](https://github.com/takari/takari-maven-plugin)  

- 먼저, 프로젝트의 메인 폴더에 들어가서 다음 명령(goal-목표)을 실행해야합니다.  
~~~
mvn -N io.takari:maven:wrapper
~~~

- Maven의 버전을 지정할 수도 있습니다.  
~~~
mvn -N io.takari:maven:wrapper -Dmaven=3.5.2
~~~

goal 을 실행하면 프로젝트에 더 많은 파일과 디렉터리가 생성됩니다.  

- mvnw : 완전히 설치된 Maven 대신에 사용되는 실행 가능한 유닉스 셸 스크립트 입니다.  
- mvnw.cmd : 위 스크립트의 배치 버전 입니다.  
- mvn : Maven 래퍼 자바 라이브러리와 그 속성 파일을 가지고있는 숨겨진 폴더 입니다.  

## Unix 계열 시스템에서 사용

~~~
./mvnw clean install
~~~

~~~
./mvnw.cmd clean install
~~~

## springboot 실행

~~~
./mvnw spring-boot:run
~~~
