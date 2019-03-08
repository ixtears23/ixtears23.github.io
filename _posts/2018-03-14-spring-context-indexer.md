---
layout: post-spring-framework
title: "spring-context-indexer"
date: 2018-04-20
excerpt: "후보 구성 요소의 색인 생성"
tags: [springframework]
spring-framework: true
comments: true
---

# Generating an index of candidate components(후보 구성 요소의 색인 생성)

클래스 경로 검색은 매우 빠르지만  
**컴파일 시간**에 **정적 후보 목록을 작성**하여  
**대규모 응용 프로그램의 시작 성능을 향상**시킬 수 있습니다.  

**색인을 생성**하려면  
구성 요소 검색 지시문의 대상인 **구성 요소가 포함 된 각 모듈**에 **추가 종속성을 추가**하기만 하면 됩니다.  
~~~xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context-indexer</artifactId>
        <version>5.0.5.RELEASE</version>
        <optional>true</optional>
    </dependency>
</dependencies>
~~~

또는 Gradle을 사용합니다.
~~~grooby
dependencies {
    compileOnly("org.springframework:spring-context-indexer:5.0.5.RELEASE")
}
~~~
이 프로세스는 항아리(jar)에 포함될 `META-INF/spring.components` 파일을 생성합니다.  

---
IDE에서 이 모드로 작업 할 때  
**스프링 컨텍스트 인덱서**는 후보 구성 요소가 업데이트 될 때 인덱스가 최신인지 확인하기 위해  
annotation 프로세서로 등록되어야 합니다.

---
---
`META-INF/spring.components`가 **클래스 경로에서 발견**되면 **인덱스가 자동으로 활성화**됩니다.  
인덱스가 일부 라이브러리 (또는 사용 사례)에서 부분적으로 사용 가능하지만  
전체 애플리케이션에 대해 빌드 할 수없는 경우 `spring.index.ignore`를 `true`로 설정하여  
일반 클래스 경로 배열로 대체 할 수 있습니다 (즉, 인덱스가 전혀없는 것처럼).  
시스템 속성 또는 `classpath`의 루트에있는 `spring.properties` 파일에 있습니다.

---
