---
layout: java-basic-post
title: "StringBuilder vs StringBuffer"
date: 2019-07-29
excerpt: ""
tags: [String,StringBuilder,StringBuffer]
java-basic: true
comments: true
---

## `String`
`String`은 불변 객체 입니다.  

문자열을 연결할 때 `+` 연산자를 사용할 수 있습니다.  

`+` 연산을 통해서 `String` 문자열을 연결하게 되면
기존 `String` 객체는 `Garbage Collector`에 의해 제거되며 새로운
인스턴스를 생성하게 됩니다.  
~~~java
String a = "a";
String b = "b";

String c = a + b;
~~~
`a` 객체와 `b` 객체는 `Garbage Collector`에 의해 제거되며
문자열 `ab`가 새로운 `c`객체 로 메모리에 올라갑니다.  


문자열 연결은 `StringBuilder(또는 StringBuffer)`클래스를 통해 구현 됩니다.  

이 클래스의 생성자 또는 `null` 인수를 건네 주면 `NullPointerException` 이 발생합니다.  

new 연산자로 `String` 객체를 인스턴스화 시키게 되면 같은 문자열 이더라도
다른 주소를 참조하게 됩니다.
~~~java
String str = new String("str");
~~~

하지만 문자열을 바로 선언한 경우는 `String`은 불변객체이기 때문에 이미 메모리에 올라가 있는 동일한 문자열의 주소를 참조하게 됩니다.  
~~~java
String str = "str";
~~~



## `StringBuffer`

`StringBuffer` 는 `String`과는 달리 가변 객체입니다.  
`Thread`로 부터 안전합니다.  
다중 `Thread` 환경에서 안전하게 사용할 수 있습니다.  

`StringBuffer`를 사용하기 전에 우선 `StringBuilder`의 사용을 고려해야 합니다.  

`StringBuffer`는 Thread 안전성을 보장하기 때문에 `StringBuilder` 보다 성능이 좋지 않습니다.    


## `StringBuilder`
`StringBuffer`와 호환되는 API를 제공하지만 동기화가 제공되지 않습니다.  
이 클래스는 StringBuffer를 대체하기 위해 단일 Trhead 환경에서 사용하기 위해 설계되었습니다.  

`StringBuiller` 인스턴스는 다중 Thread 환경에서 사용하기에는 안전하지 않습니다.  
동기화가 필요한 경우에는 `StringBuffer`를 사용해야 합니다.  


## 조금 더 상세하고 정확한 내용을 위해 수정이 필요함.  
