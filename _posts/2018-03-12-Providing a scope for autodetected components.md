---
layout: spring-framework-post
title: "@Scope annotation"
date: 2018-03-13
tags: [springframework]
excerpt: "자동 감지된 bean 은 singleton 입니다."
spring-framework: true
comments: true
---


일반적으로 Spring에서 관리하는 구성 요소와 마찬가지로
**자동 감지** 된 구성 요소의 **기본 범위**와 ***가장 일반적인 범위는*** **싱글 톤**입니다.  
*그러나 때로는* **`@Scope` 주석을 통해** 지정할 수있는 **다른 범위가 필요**합니다.  
어노테이션에 **스코프 이름**을 입력하기 만하면됩니다.  
~~~java
@Scope("prototype")
@Repository
public class MovieFinderImpl implements MovieFinder {
    // ...
}
~~~

웹 관련 범위에 대한 자세한 내용은 요청, 세션, 응용 프로그램 및 WebSocket 범위를 참조하십시오.  

---
어노테이션 기반 접근 방식에 의존하지 않고
범위 분석을 위한 사용자 지정 전략을 제공하려면
`ScopeMetadataResolver` 인터페이스를 구현하고
기본 인수가 없는 생성자를 포함시켜야합니다.  
그런 다음 스캐너를 구성 할 때 정규화 된 클래스 이름을 제공하십시오.  

---

~~~java
@Configuration
@ComponentScan(basePackages = "org.example", scopeResolver = MyScopeResolver.class)
public class AppConfig {
    ...
}
~~~
~~~xml
<beans>
    <context:component-scan base-package="org.example"
            scope-resolver="org.example.MyScopeResolver" />
</beans>
~~~
*특정 싱글 톤 scope가 아닌* **특정 scope를 사용하는 경우**  
`scope`가 **지정된 객체**에 대한 **프록시를 생성**해야 할 수 있습니다.  

추론은 `Scoped beans`에서 **종속성**으로 설명됩니다.  
이를 위해 `scoped-proxy` 속성을 `component-scan` 요소에서 사용할 수 있습니다.  
가능한 세 가지 값은 `no`, `interfaces` 및 `targetClass`입니다.  

예를 들어, 다음 구성은 **표준 JDK 동적 프록시**가됩니다.  
~~~java
@Configuration
@ComponentScan(basePackages = "org.example", scopedProxy = ScopedProxyMode.INTERFACES)
public class AppConfig {
    ...
}
~~~
~~~xml
<beans>
    <context:component-scan base-package="org.example"
        scoped-proxy="interfaces" />
</beans>
~~~
