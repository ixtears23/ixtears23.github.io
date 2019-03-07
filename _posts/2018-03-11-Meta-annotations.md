---
layout: post
title: "Meta-annotations"
date: 2018-03-11
tags: [springframework,annotation]
spring-framework: true
comments: true
---

# Meta-annotations

**Spring에서 제공하는 많은 annotation**은 여러분의 코드에서 *meta-annotations* 으로 사용할 수 있습니다.  

*meta-annotation*은 단순히 **다른 annotation에 적용 할 수있는 annotation**입니다.  
예를 들어 위에서 언급 한 `@Service` 어노테이션은 `@Component` 메타 주석으로 표시됩니다.  

~~~java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component // Spring은 이것을보고 @Service를 @Component와 같은 방식으로 처리합니다.
public @interface Service {

    // ....
}
~~~

**메타 어노테이션을 결합**하여 **합성 어노테이션**을 만들 수도 있습니다.  
예를 들어, *Spring MVC의* **`@RestController` 어노테이션**은 **`@Controller`와 `@ResponseBody`로 구성**된다.  

또한 **작성된 어노테이션**은 선택적으로 *사용자 정의가 가능하도록 메타 어노테이션의* **속성을 재 선언** 할 수 있습니다.  
이는 **메타 어노테이션 속성의 하위 집합 만 노출**하려는 경우 **특히 유용** 할 수 있습니다.  
예를 들어 Spring의 **`@SessionScope` 어노테이션**은 **scope name을 세션에 하드 코드**하지만  
**proxyMode를 사용자 정의** 할 수 있습니다.  
~~~java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Scope(WebApplicationContext.SCOPE_SESSION)
public @interface SessionScope {

    /**
     * {@link Scope#proxyMode}의 Alias(별칭) 입니다.
     * 기본값은 {@link ScopedProxyMode#TARGET_CLASS}.
     */
    @AliasFor(annotation = Scope.class)
    ScopedProxyMode proxyMode() default ScopedProxyMode.TARGET_CLASS;

}
~~~


`@SessionScope`은 다음과 같이 **proxyMode를 선언하지 않고 사용할 수 있습니다.**  
~~~java
@Service
@SessionScope
public class SessionScopedService {
    // ...
}
~~~

또는 다음과 같이 **proxyMode에 대한 재정의 된 값을 사용**합니다.
~~~java
@Service
@SessionScope(proxyMode = ScopedProxyMode.INTERFACES)
public class SessionScopedUserService implements UserService {
    // ...
}
~~~

자세한 내용은 [Spring Annotation Programming Model위키 페이지](https://github.com/spring-projects/spring-framework/wiki/Spring-Annotation-Programming-Model) 를 참조하십시오.
