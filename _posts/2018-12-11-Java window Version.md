---
layout: post-java
title: "Java window Version"
date: 2018-12-11
excerpt: "java 윈도우 버전 설정"
tags: [java,version]
java-basic: true
comments: true
---

**java 윈도우 버전 설정**  


## 레지스트리 확인

`ctrl + r` > `regedit` 입력  

경로  
 - 64비트  
`HKEY_LOCAL_MACHINE` > `SOFTWARE` > `JavaSoft`  

 - 32비트  
`HKEY_LOCAL_MACHINE` > `SOFTWARE` > `WOW6432Node` > `JavaSoft`  


## 제어판 확인

 제어판 > java 검색  

 Java 제어판 > Java > Java Runtime Environment 설정  
