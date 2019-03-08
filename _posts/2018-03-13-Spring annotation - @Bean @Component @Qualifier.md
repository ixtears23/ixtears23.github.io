---
layout: spring-framework-post
title: "Spring annotation - @Bean @Component @Qualifier"
date: 2018-03-13
tags: [springframework]
excerpt: "스프링 컴포넌트는 bean 정의 메타 데이터를 컨테이너에 제공할 수있다."
spring-framework: true
comments: true
---


*스프링 컴포넌트는* **`bean` 정의 메타 데이터**를 **컨테이너에 제공** 할 수있다.  
`@Configuration` 어노테이션이 달린 클래스 내에서 `bean` 메타 데이터를 정의하는 데 사용 된 것과
동일한 **`@Bean` 어노테이션**으로 이를 수행한다.  
다음은 간단한 예입니다.  

~~~java
@Component
public class FactoryMethodComponent {

    @Bean
    @Qualifier("public")
    public TestBean publicInstance() {
        return new TestBean("publicInstance");
    }

    public void doWork() {
        // 컴포넌트 메소드 구현 생략
    }
}
~~~

이 클래스는 `doWork ()` 메소드에 포함 된 애플리케이션 특정 코드를 갖는 Spring 컴포넌트이다.  
그러나 메소드 `publicInstance ()`를 참조하는 팩토리 메소드가있는 bean 정의도 제공합니다.  

`@Bean` 어노테이션은 `@Qualifier` 어노테이션을 통해
**한정자 값**과 같은 **팩토리 메소드 및 기타 Bean 정의 특성을 식별**합니다.  

지정할 수 있는 다른 메서드 수준의 어노테이션은
`@Scope`, `@Lazy` 및 사용자 지정 한정자 어노테이션(custom qualifier annotations)입니다.  

---
`@Lazy` 어노테이션은 `@Autowired` 또는 `@Inject`로 표시된 **주입 지점에 배치** 될 수 있습니다.  
이러한 맥락에서, 이것은 **지연-해결 프록시(lazy-resolution proxy)의 주입**으로 이어진다.  

---

앞에서 설명한 것처럼 **Autowired 필드와 메소드**는 `@Bean` 메소드의 `autowiring`에 대한 추가 지원과 함께 지원됩니다.  
~~~java
@Component
public class FactoryMethodComponent {

    private static int i;

    @Bean
    @Qualifier("public")
    public TestBean publicInstance() {
        return new TestBean("publicInstance");
    }

    // 사용자 정의 한정자(custom qualifier) 사용 및 메소드 매개 변수의 자동 와이어 링
    @Bean
    protected TestBean protectedInstance(
            @Qualifier("public") TestBean spouse,
            @Value("#{privateInstance.age}") String country) {
        TestBean tb = new TestBean("protectedInstance", 1);
        tb.setSpouse(spouse);
        tb.setCountry(country);
        return tb;
    }

    @Bean
    private TestBean privateInstance() {
        return new TestBean("privateInstance", i++);
    }

    @Bean
    @RequestScope
    public TestBean requestScopedInstance() {
        return new TestBean("requestScopedInstance", 3);
    }
}
~~~

이 예제에서는 String 메서드 매개 변수 `country`를
`privateInstance`라는 **다른 bean**의 **Age 속성 값**으로 **자동 작성**합니다.  

**Spring 표현식 언어 요소**는 `#{<expression>}` **표기법**을 통해 **특성 값을 정의**합니다.  
`@Value` 어노테이션의 경우 **표현식 텍스트를 해석** 할 때 **bean 이름을 찾도록 표현식 분석기가 미리 구성**됩니다.  

**Spring Framework 4.3**에서,
**요청하는 주입 지점**에 **액세스**하기 위해 **현재 bean의 생성을 트리거**하는
**InjectionPoint 유형의 팩토리 메소드 매개 변수**
(또는보다 **구체적인 하위 클래스 DependencyDescriptor**)를 선언 할 수도 있습니다.  

이것은 *기존 인스턴스를 삽입하는 것이 아니라*
**bean 인스턴스**의 **실제 생성에만 적용**된다는 점에 **유의**하십시오.  
따라서 이 기능은 **프로토 타입 범위의 빈**에 **가장 적합**합니다.  

*다른 스코프의 경우,*
**팩토리 메소드**는 주어진 scope에서
새로운 bean 인스턴스의 생성을 트리거 한 injection point만 보게 됩니다.  

예를 들어, **게으른 싱글 톤 빈(lazy singleton bean)의 생성을 트리거(유발?) 한 종속성**.  
그러한 시나리오에서 제공된 주입 지점 메타 데이터를 의미론적 관심사와 함께 사용하십시오.  
~~~java
@Component
public class FactoryMethodComponent {

    @Bean @Scope("prototype")
    public TestBean prototypeInstance(InjectionPoint injectionPoint) {
        return new TestBean("prototypeInstance for " + injectionPoint.getMember());
    }
}
~~~

일반 *Spring 구성 요소의* `@Bean` 메소드는
Spring `@Configuration` 클래스 내부의 대응되는 메소드와 다르게 처리됩니다.  

***차이점은*** `@Component` 클래스가 **메소드 및 필드의 호출**을 **가로 채기 위해** ***CGLIB로 향상되지 않는다는 것입니다.  
CGLIB 프록싱***은 `@Configuration` 클래스의 `@Bean` 메소드 내의 **메소드 또는 필드를 호출**하여
협업(collaborating) 객체에 대한 **빈 메타 데이터 참조**를 **작성하는 수단**입니다.  

그러한 메소드는 ***일반적인 자바 의미와 함께 호출되는 것이 아니라***
`@Bean` 메소드에 대한 프로그래밍 방식의 호출을 통해 다른 bean을 참조 할 때에도
**Spring Bean**의 **일반적인 수명주기 관리**와 **프록시를 제공**하기 위해 컨테이너를 거치게됩니다.  

반대로, `@Component` 클래스 내의 `@Bean` 메소드에서 **메소드 나 필드를 호출하는 것**은
**특수한 CGLIB 처리** 나 **다른 제약 조건을 적용하지 않는** ***표준 자바 의미론***을 가지고있다.  

---
`@Bean` 메소드를 **`static`으로 선언** 할 수 있습니다.  
포함 된 설정 클래스를 **인스턴스로 생성하지 않고 호출** 할 수 있습니다.  
이는 **`post-processor` 빈을 정의 할 때 특히 유용**합니다.  
`BeanFactoryPostProcessor` 또는 `BeanPostProcessor` 유형의 Bean은
**컨테이너 수명주기 초기**에 **초기화**되므로
**그 시점**에서 ***구성의 다른 부분을 트리거하지 않아야하기 때문입니다.***  

**static `@Bean` 메서드에 대한 호출**은
`@Configuration` 클래스 (위 참조(see above))가 아닌 ***컨테이너에 의해 가로 챌 수 있습니다.***  
이는 기술적 인 한계 때문입니다.   
**CGLIB 서브 클래싱**은 **비정적(non-static) 메소드**를 **오버라이드** 할 수 있습니다.  
결과적으로 또 다른 `@Bean` 메소드에 대한 직접 호출은
표준 Java 의미를 가지게되어 ***독립 인스턴스가 팩토리 메소드 자체에서 곧바로 리턴됩니다.***  

`@Bean` 메소드의 Java 언어 가시성(visibility)은 Spring 컨테이너의 결과 빈 정의에 즉각적인 영향을 미치지 않습니다.  
**`@Configuration` 클래스** 나 **다른 정적 메소드**에도 적합하게 **factory 메소드**를 **자유롭게 선언** 할 수 있습니다.  
***그러나 `@Configuration` 클래스의 일반 `@Bean` 메서드는 재정의 할 수 있어야합니다.***  
즉, **private** 또는 **final**로 **선언하면 안됩니다.**  

`@Bean` 메소드는 *컴포넌트 또는 구성 클래스의* **기본 클래스**뿐만 아니라
*컴포넌트 또는 구성 클래스에 의해 구현 된 인터페이스에서 선언 된* **Java 8 기본 메소드**에서도 발견됩니다.  
따라서 **Spring 4.2**에서 **Java 8 기본 메소드**를 통해 **다중 상속도 가능**하며
**복잡한 구성을 작성**하는 데 **많은 유연성을 허용**합니다.  

마지막으로, *하나의 클래스가 런타임시 사용 가능한 의존성에 의존하여 사용할* **여러 팩토리 메소드의 배열**처럼
***동일한 빈에 대해*** **여러 개의 `@Bean` 메소드** ***를 가질 수 있음에 유의하십시오.***  
이것은 다른 구성 시나리오에서 "greediest"생성자 또는 팩토리 메소드를 선택하는 알고리즘과 같습니다.  
가장 많은 수의 종속성이있는 변형은 생성시 선택됩니다.  
컨테이너가 여러 개의 `@Autowired` 생성자 사이에서 선택하는 것과 유사합니다.  
