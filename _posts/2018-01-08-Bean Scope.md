---
layout: spring-framework-post
title: "Bean Scope"
date: 2018-01-08
excerpt: ""
tags: [springframework,bean]
spring-framework: true
comments: true
---

# Bean
### singleton scope

싱글 톤 빈의 하나의 공유 인스턴스 만 관리되며 해당 빈 정의와 일치하는 id가있는 빈에 대한 모든 요청은
그 하나의 특정 빈 인스턴스가 Spring 컨테이너에 의해 리턴됩니다.  
다시 말해, Bean 정의를 정의 할 때 단일 톤으로 범위가 지정되면
Spring IoC 컨테이너는 해당 Bean 정의로 정의 된 객체의 인스턴스를 정확히 하나 생성합니다.  
이 단일 인스턴스는 이러한 싱글 톤 빈의 캐시에 저장되며
그 이후의 모든 요청과 해당 명명 된 Bean에 대한 참조는 캐시 된 객체를 반환합니다.  
하나의 인스턴스 만 생성됩니다.  
이 동일한 공유 인스턴스가 각 공동 작업 객체에 주입됩니다.  

Singleton Bean에 대한 Spring의 개념은 Gang of Four (GoF) 패턴 책에 정의 된 Singleton 패턴과 다릅니다.  
GoF Singleton은 ClassLoader 당 하나의 특정 클래스 인스턴스 만 생성되도록 객체의 범위를 하드 코딩합니다.  
Spring 싱글 톤의 범위는 컨테이너 및 콩마다 가장 잘 묘사됩니다.  
즉, 단일 Spring 컨테이너에서 특정 클래스에 대해 하나의 bean을 정의하면
Spring 컨테이너는 해당 bean 정의로 정의 된 클래스의 인스턴스 하나만 생성합니다.  
싱글 톤 범위는 Spring의 기본 범위입니다.


XML에서 bean을 하나의 싱글 톤으로 정의한 예제입니다.  
scope를 설정하지 않으면 싱글 톤 scope가 기본값 입니다.  

~~~xml
<bean id="accountService" class="com.foo.DefaultAccountService"/>
<bean id="accountService" class="com.foo.DefaultAccountService" scope="singleton"/>
~~~

### The prototype scope

bean deployment의 non-singleton, prototype 범위는 특정 bean에 대한 요청이 이루어질 때마다 새로운 bean 인스턴스를 생성합니다.  
즉, bean은 다른 bean으로 주입되거나 컨테이너에서 getBean () 메소드 호출을 통해 bean을 요청합니다.  
원칙적으로 모든 stateful bean에 대한 프로토 타입 범위와 stateless beans에 대한 singleton 범위를 사용하십시오.  
프로토 타입이 bean을 공동 작업하여 참조 될 때마다 새로운 빈 인스턴스가 생성됩니다.  

~~~xml
<bean id="accountService" class="com.foo.DefaultAccountService" scope="prototype"/>
~~~

다른 범위와 달리 Spring은 프로토 타입 빈의 전체 라이프 사이클을 관리하지 않습니다.  
컨테이너는 프로토 타입 객체를 인스턴스화, 구성 및 어셈블 한 다음 프로토 타입 인스턴스를 더 이상 기록하지 않고 클라이언트에 전달합니다.  
따라서 초기화 라이프 사이클 콜백 메소드는 범위에 관계없이 모든 객체에서 호출되지만
프로토 타입의 경우 구성된 라이프 사이클 콜백은 호출되지 않습니다.  

클라이언트 코드는 프로토 타입 범위 객체를 정리하고 프로토 타입 bean이 보유하고있는 값 비싼 리소스를 릴리스해야합니다.  



JSR-250 `@PostConstruct`및 `@PreDestroy`주석은 일반적으로 현대 스프링 응용 프로그램에서 수명주기 콜백을받는 모범 사례로 간주됩니다.  
이러한 주석을 사용하면 빈이 Spring 고유 인터페이스와 결합되지 않는다는 것을 의미합니다.  
자세한 내용은 `@PostConstruct` 및 `@PreDestroy` 를 참조하십시오.  

JSR-250 주석을 사용하고 싶지 않지만 여전히 결합을 제거하려는 경우
init-method 및 destroy-method 객체 정의 메타 데이터를 사용하는 것이 좋습니다.  
