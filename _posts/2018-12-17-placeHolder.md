---
layout: post
title: "PlaceHolder"
date: 2018-12-17
excerpt: "properties 설정"
tags: [springframework,placeHolder,properties]
spring-framework: true
comments: true
---


### 스프링 xml 설정에서 placeholder SPEL로 사용하는 방법
~~~xml
<context:property-placeholder location="classpath:org/uengine/uengine_#{ systemProperties['SYSTYPE']}.properties" />
~~~
**위 코드 처럼 `#{ systemProperties['SYSTYPE']} ` 스프링 `SPEL` 을 사용하려면**  
**`TOMCAT`에서 설정한다.**  

**톰캣 서버 탭에서 톰캣 서버를 더블클릭한다.**  
![01](https://github.com/ixtears23/docs/blob/master/spring/img/01.PNG?raw=true)  

**Open launch configuration 을 클릭한다.**  
![02](https://github.com/ixtears23/docs/blob/master/spring/img/02.PNG?raw=true)  

**아래 창이 뜬다.**  

![03](https://github.com/ixtears23/docs/blob/master/spring/img/03.PNG?raw=true)  

**`-D` 옵션으로 `SYSTYPE`에 값을 주면**  
**`#{systemProperties['SYSTYPE']}` 으로 읽을 수 있다.**  


-D 를 붙이는 이유 [stackoverflow](https://stackoverflow.com/questions/44745261/why-do-jvm-arguments-start-with-d)
