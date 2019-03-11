---
layout: spring-framework-post
title: "Configuration, Bean 애너테이션"
date: 2018-04-23
excerpt: "Bean 및 Configuration 애너테이션 기본 개념"
tags: [springframework,jsr330]
spring-framework: true
comments: true
---

Spring의 새로운 **Java 구성** 지원의 중심은 **`@Configuration`-annotated 클래스**와 **`@Bean`-annotated 메소드**이다.  

`@Bean` 어노테이션은 메소드가 **Spring IoC 컨테이너에 의해 관리** 될 새로운 **객체를 인스턴스화**,  
**구성 및 초기화**한다는 것을 나타내는 데 사용된다.  
Spring의 `<beans/>` XML 설정에 익숙한 분들을 위해 `@Bean` 주석은 `<bean/>` 요소와 **동일한 역할**을합니다.  
Spring `@Component`와 함께 `@Bean` annotated 메소드를 사용할 수 있지만 `@Configuration` 빈과 함께 자주 사용됩니다.  

`@Configuration`을 사용하여 클래스에 주석을다는 것은 주된 **목적**이 **Bean 정의의 소스**임을 나타냅니다.  

게다가 `@Configuration` 클래스는 같은 클래스에서 다른 `@Bean` 메소드를 호출함으로써 **bean 간 의존성을 정의** 할 수 있게 한다.   
가장 단순한 가능한 `@Configuration` 클래스는 다음과 같이 읽을 수 있습니다.  
~~~java
@Configuration
public class AppConfig {

    @Bean
    public MyService myService() {
        return new MyServiceImpl();
    }
}
~~~
위의 AppConfig 클래스는 다음 Spring `<beans/>` XML과 **동일**합니다.
~~~xml
<beans>
    <bean id="myService" class="com.acme.services.MyServiceImpl"/>
</beans>
~~~

### 전체 `@Configuration` vs 'lite'`@Bean` 모드?

**`@Bean` 메서드가 `@Configuration`으로 주석 처리되지 않은 클래스 내에서 선언 될 때** 'lite' 모드에서 처리되는 것으로 간주됩니다.  
`@Component` 또는 평범한 구식 클래스에서 선언 된 Bean 메소드는 **'lite'로 간주**되며,  
포함하는 클래스의 다른 주요 목적과 `@Bean` 메소드가 거기에 보너스 일뿐입니다.  
예를 들어, 서비스 구성 요소는 적용 가능한 각 구성 요소 클래스의 추가 `@Bean` 메서드를 통해 **관리 뷰를 컨테이너에 노출**시킬 수 있습니다.  
이러한 시나리오에서 `@Bean` 메서드는 **단순한 범용 팩토리 메서드 메커니즘**입니다.


전체 `@Configuration`과 달리 lite `@Bean` 메소드는 **bean 간 의존성을 선언 할 수 없습니다.**  
대신, 그들은 포함하는 구성 요소의 내부 상태와 선택적으로 선언 할 수있는 인수에 따라 작동합니다.  
따라서 이러한 `@Bean` 메서드는 다른 `@Bean` 메서드를 호출하지 않아야합니다.  
이러한 각각의 메소드는 특별한 런타임 시맨틱없이 문자 그대로 단지 **특정 빈 참조에 대한 팩토리 메소드** 입니다.  
여기서 긍정적 인 부작용은 런타임에 **CGLIB** 서브 클래싱을 적용 할 필요가 없으므로  
**클래스 디자인 측면에서 제한이 없다**는 것입니다 (즉, 포함 클래스가 최종 클래스 일 수 있음).  

일반적인 시나리오에서 `@Bean` 메서드는 `@Configuration` 클래스 내에서 선언되므로  
'전체'모드가 항상 사용되고 **크로스 메서드 참조**가 **컨테이너의 수명주기 관리로 리디렉션**됩니다.  
이렇게하면 **동일한 `@Bean` 메소드**가 **실수로 '정상'모드에서 작동 할 때  
추적하기 어려운 미묘한 버그**를 줄이는 데 도움이되는 일반 Java 호출을 통해 호출되는 것을 방지 할 수 있습니다.  
