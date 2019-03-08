---
layout: spring-framework-post
title: "Spring의 Pointcut API"
date: 2018-04-25
excerpt: "-"
tags: [springframework,aop]
spring-framework: true
comments: true
---

## Concepts(개념)

**Spring의 포인트 컷 모델**은 조언 *유형과 상관없이* **pointcut 재사용을 가능**하게한다.  
**동일한 포인트 컷을 사용**하여 **다른 조언을 타겟팅** 할 수 있습니다.  
`org.springframework.aop.Pointcut` 인터페이스는  
**특정 클래스** 및 **메소드에 대한 조언**을 대상으로하는 데 사용되는 **중앙 인터페이스**입니다.  
전체 인터페이스는 다음과 같습니다.  
~~~java
public interface Pointcut {

    ClassFilter getClassFilter();

    MethodMatcher getMethodMatcher();

}
~~~
Pointcut 인터페이스를 두 부분으로 나누면  
**클래스**와 **메소드 매칭 부분을 재사용** 할 수 있고 **다른 메소드 매처**와 `union` 을 수행하는 것과 같이  
***세밀한 합성 작업을 수행 할 수 있습니다.***  
`ClassFilter` 인터페이스는 `Pointcut`을 **지정된 대상 클래스 집합으로 제한**하는 데 사용됩니다.  
`matches()` 메서드가 항상 **true를 반환**하면 **모든 대상 클래스가 일치**합니다.  
~~~java
public interface ClassFilter {

    boolean matches(Class clazz);
}
~~~

**`MethodMatcher` 인터페이스**는 일반적으로 더 **중요**합니다.  
전체 인터페이스는 다음과 같습니다.  
~~~java
public interface MethodMatcher {

    boolean matches(Method m, Class targetClass);

    boolean isRuntime();

    boolean matches(Method m, Class targetClass, Object[] args);
}
~~~
`matches(Method, Class)` 메서드는 이 `pointcut`이 **대상 클래스의 주어진 메소드**와 **일치하는지 여부를 테스트**하는데 사용 됩니다.  
이 평가는 **모든 메소드 호출에 대한 테스트의 필요성을 피하기 위해** ***AOP 프록시가 작성 될 때 수행 될 수 있습니다.***  
주어진 메소드에 대해 `2-argument matches` 메소드가 `true`를 리턴하고  
`MethodMatcher`의 `isRuntime()` 메소드가 `true`를 리턴하면 **모든 메소드 호출시** `3-argument matches` 메소드가 호출됩니다.  
이렇게 하면 **대상 조언이 실행되기 바로 전**에 ***메서드 호출에 전달 된 인수를 Pointcut에서 볼 수 있습니다.***  

대부분의 `MethodMatcher`는 **정적**입니다.  
즉, `isRuntime()` 메서드가 `false`를 반환한다는 것을 의미합니다.  
이 경우, `3-argument matches` 메소드는 **절대로 호출되지 않습니다.**  

---
가능한 경우 **AOP 프록시가 생성 될 때**  
AOP 프레임 워크가 **pointcut 평가 결과를 캐시 할 수 있도록** ***pointcuts를 정적으로 만드십시오.***

---

## Operations on pointcuts(pointcuts에 대한 작업)
`Spring`은 `pointcut`에서의 **작업을 지원**합니다.  
특히, **union**과 **intersection**.  

- Union은 pointcut가 일치하는 메소드를 의미합니다.  
- 교차점(`Intersection`)은 두 `pointcuts`가 일치하는 메소드를 의미합니다.  
- 일반적으로 `Union`이 더 유용합니다.  
- **pointcut**은 `org.springframework.aop.support.Pointcuts` 클래스의 정적 메소드를 사용하거나  
같은 패키지에서 `ComposablePointcut` 클래스를 사용하여 작성할 수 있습니다.  
그러나 **AspectJ pointcut 표현식**을 사용하는 것이 **보통 더 쉬운 방법**이다.  

## AspectJ expression pointcuts(AspectJ 표현식 포인트 컷)
**2.0 이후**로 Spring에서 사용되는 **가장 중요한 pointcut 유형**은 `org.springframework.aop.aspectj.AspectJExpressionPointcut`입니다.  
이것은 **AspectJ 제공 라이브러리를 사용**하여 **AspectJ pointcut 표현 문자열을 파싱하는 pointcut**이다.  

지원되는 **AspectJ pointcut primitives**에 대한 설명은 이전 장을 참조하십시오.  

## Convenience pointcut implementations(편리한 pointcut 구현)
**Spring**은 **몇 가지 편리한 pointcut 구현을 제공**한다.  
일부는 즉시 사용할 수 있습니다. 다른 것들은 응용 프로그램 특정 pointcut에서 하위 클래스로 만들어졌습니다.  

#### Static pointcuts(정적 pointcuts)
**정적 pointcuts**는 **메서드** 및 **대상 클래스를 기반**으로하며 **메서드의 인수를 고려할 수 없습니다.**  
**정적 포인트 컷**은 대부분의 용도에 충분하며 **가장 좋습니다.**  
**메소드가 처음 호출 될 때** Spring이 **정적 pointcut**을 **한 번만 평가**할 수 있습니다.  
: 그 후에 **각 메소드 호출**로 **포인트 컷을 다시 평가할 필요가 없습니다.**  

Spring에 포함 된 일부 정적 pointcut 구현을 고려해 보겠습니다.  

#### Regular expression pointcuts(정규식 pointcuts)  
**정적 인 pointcut를 지정**하는 **한 가지 분명한 방법**은 **정규식**입니다.  
Spring 외에도 여러 AOP 프레임 워크가 이를 가능하게합니다.  
`org.springframework.aop.support.JdkRegexpMethodPointcut`은 **JDK의 정규식 지원을 사용**하는 **일반 정규 표현식 포인트 컷**입니다.  

`JdkRegexpMethodPointcut` **클래스를 사용**하여 **패턴 문자열 목록을 제공** 할 수 있습니다.  
**이들 중 하나라도 일치**하면 **포인트 컷은 true로 평가**됩니다. (*결과적으로 이러한 pointcut의 조합*이 **효과적**입니다.)  

사용법은 다음과 같습니다.  
~~~xml
<bean id="settersAndAbsquatulatePointcut"
        class="org.springframework.aop.support.JdkRegexpMethodPointcut">
    <property name="patterns">
        <list>
            <value>.*set.*</value>
            <value>.*absquatulate</value>
        </list>
    </property>
</bean>
~~~

**Spring**은 **Advice를 참조 할 수있는 편의 클래스** 인 `RegexpMethodPointcutAdvisor`를 **제공**한다.  
(**Advice**는 **advice 이전에 interceptor가 될 수 있고**, **advice 전에는 advice가 던져 질 수 있음을 기억하자.**)  
배후에서 **Spring**은 `JdkRegexpMethodPointcut`을 **사용**합니다.  
`RegexpMethodPointcutAdvisor`를 **사용**하면  
다음과 같이 하나의 `bean`이 `pointcut`과 `advice`를 **모두 캡슐화**하므로 배선(wiring)이 간단 해집니다.  

~~~xml
<bean id="settersAndAbsquatulateAdvisor"
        class="org.springframework.aop.support.RegexpMethodPointcutAdvisor">
    <property name="advice">
        <ref bean="beanNameOfAopAllianceInterceptor"/>
    </property>
    <property name="patterns">
        <list>
            <value>.*set.*</value>
            <value>.*absquatulate</value>
        </list>
    </property>
</bean>
~~~

`RegexpMethodPointcutAdvisor`는 **모든 조언 유형**과 **함께 사용**할 수 있습니다.  

#### Attribute-driven pointcuts(속성 중심의 pointcuts)
**중요한 유형의 정적 포인트 컷**은 **메타 데이터 중심의 pointcut**입니다.  
**메타 데이터 속성 값** (일반적으로 **소스 레벨 메타 데이터**)이 **사용**됩니다.  

#### Dynamic pointcuts(동적 pointcuts)
**동적 인 pointcut**는 정적 인 pointcut보다 **더 비싸다(costlier)**.  
그들은 **메소드 정보**와 **정적 정보를 고려**합니다.  
이것은 모든 메소드 호출로 평가되어야한다는 것을 의미합니다.  
인수가 달라지므로 **결과를 캐시 할 수 없습니다.**  

주된 예는 **제어흐름(control flow) 포인트 컷**입니다.  

#### Control flow pointcuts(제어 흐름 포인트 컷)
`Spring control flow pointcut`은 개념적으로 `AspectJ cflow pointcut`과 **비슷하지만 덜 강력**합니다.  
(현재는 다른 pointcut과 일치하는 join point 아래에서 pointcut이 실행되도록 지정하는 방법이 없습니다.)  
**제어 흐름 포인트 컷**은 **현재 호출 스택과 일치**합니다.  
예를 들어, `com.mycompany.web` 패키지의 메소드 또는 `SomeCaller` 클래스의 메소드에 의해 **조인 포인트가 호출 된 경우 호출 될 수 있습니다.**  

---
**제어 흐름 pointcut**는 다른 동적 pointcut보다 **런타임에서 평가하는 것이 훨씬 더 비쌉니다.**  
**Java 1.4**에서 **비용**은 **다른 동적 인 pointcut의 5 배**입니다.

---


## Pointcut superclasses(Pointcut 슈퍼 클래스)

`Spring`은 자신만의 `pointcut`을 구현하는데 도움이되는 유용한 `pointcut` **수퍼 클래스를 제공**한다.  
**정적인 pointcut**이 **가장 유용**하기 때문에 아래와 같이 `StaticMethodMatcherPointcut`을 **서브 클래스화**할 수 있습니다.  
이를 위해서는 **하나의 추상 메소드 만 구현**하면 됩니다 (**다른 메소드를 오버라이드**하여 **비헤이비어**를 **사용자 정의** 할 수도 있음).  
~~~java
class TestStaticPointcut extends StaticMethodMatcherPointcut {

    public boolean matches(Method m, Class targetClass) {
        // return true if custom criteria match
    }
}
~~~
**동적인 pointcut**을 위한 **수퍼 클래스**도 있습니다.  
**Spring 1.0 RC2** 이상의 **advice 타입**을 가진 **커스텀 pointcut**을 사용할 수있다.  

## Custom pointcuts(사용자 정의 pointcuts)
**Spring AOP**의 **pointcut**은 언어 기능 (AspectJ에서와 같이)이 아닌 **Java 클래스**이기 때문에  
**정적 또는 동적** 인 **사용자 정의 pointcut을 선언** 할 수있다.  
**Spring**의 **사용자 정의 pointcut**은 **임의적으로 복잡** 할 수 있습니다.  
그러나 가능한 경우 **AspectJ pointcut** ***표현식 언어를 사용하는 것이 좋습니다.***  

---
**Spring의 최신 버전**은 **JAC에서 제공**하는 **"의미 적 포인트 컷(semantic pointcuts)"을 지원**합니다.  
예를 들어, "**대상 객체**에서 **인스턴스 변수를 변경**하는 **모든 메소드**"  

---
