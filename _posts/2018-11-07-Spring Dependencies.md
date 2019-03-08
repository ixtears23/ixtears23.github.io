---
layout: post-spring-framework
title: "Spring Dependencies"
date: 2018-11-07
excerpt: "Spring 의존성 주입"
tags: [springframework]
spring-framework: true
comments: true
---

## 의존성 주입

`DI(Dependency Injection)`는 **생성자 인수**, **팩토리 메소드**에 대한 인수 또는 구성 후  
**객체 인스턴스에 설정된 속성을 통해서만** 객체가 **종속성 (즉, 자신이 작업하는 다른 객체)을 정의하는 프로세스**입니다.  
**팩토리 메서드에서 반환**됩니다.  그런 다음 **컨테이너는 `Bean`을 작성할 때** 이러한 **종속성을 주입**합니다.  
이 프로세스는 기본적으로 클래스 직접 작성이나 또는 Service Locator 패턴을 사용하여    
Bean 자체의 의존성에 대한 인스턴스화 또는 위치를 제어하는 빈 자체의 역전(따라서 Inversion of Control)입니다.

코드는 **DI 원리로 보다 깔끔**하고, **디커플링은 객체가 의존성을 제공받을 때 더욱 효과적**입니다.  
**객체**는 **종속성을 찾지 않으며 종속성의 위치 나 클래스를 알지 못합니다.**  
따라서 종속성이 인터페이스 또는 추상 기본 클래스에있을 때 클래스 테스트가 쉬워져 단위 테스트에서 스텁 또는 모의 구현을 사용할 수 있습니다.  

DI는 크게 두 가지로 나뉩니다.  
**생성자 기반 종속성 주입**과 **Setter기반 종속성 주입**입니다.


### 생성자 기반 종속성 주입
**생성자 기반 DI**는 컨테이너가 여러 종속 변수를 나타내는 **여러 개의 인수**로 **생성자를 호출**함으로써 수행됩니다.  
`Bean`을 생성하기 위해 **특정 인자를 가진 정적 팩토리 메소드를 호출**하는 것은 ***거의 동일*** 하며,  
이 논의는 **생성자**와 **정적 팩토리 메소드**에 대한 ***인수를 유사하게 취급*** 합니다.  
다음 예제는 **생성자 주입으로 만 종속성 주입** 될 수 있는 클래스를 보여줍니다.  
~~~java
public class SimpleMovieLister {

    // SimpleMovieLister는 MovieFinder에 의존합니다.
    private MovieFinder movieFinder;

    // Spring 컨테이너가 MovieFinder를 생성자로 주입
    public SimpleMovieLister(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // 실제로 삽입 된 MovieFinder를 사용하는 비즈니스 로직은 생략합니다 ...
}
~~~

이 클래스에는 특별한 것이 없다는 것을 주목하십시오.  
이것은 **컨테이너 관련 인터페이스**, **기본 클래스** 또는 **주석**에 대한 **의존성이 없는 POJO**입니다.  

#### 생성자 인수 해결
**생성자 인수 확인 일치**는 **인수의 유형을 사용**하여 발생합니다.  
`bean` 정의의 생성자 인수에 잠재적 인 모호성이 없으면 `bean` 정의에서 생성자 인수가 정의되는 순서는  
**`bean`이 인스턴스화 될 때 해당 인수가 적절한 생성자에 제공되는 순서입니다.**  
다음 클래스를 고려하십시오.
~~~java
package x.y;

public class ThingOne {

    public ThingOne(ThingTwo thingTwo, ThingThree thingThree) {
        // ...
    }
}
~~~

`ThingTwo` 클래스와 `ThingThree` 클래스가 상속과 관련이 없다고 가정하면 잠재적 인 모호성은 없습니다.  
따라서 다음 구성이 제대로 작동하므로 **`<constructor-arg/>` 요소에 생성자 인수 인덱스 또는 유형을 명시 적으로 지정할 필요가 없습니다.**  
~~~xml
<beans>
    <bean id="thingOne" class="x.y.ThingOne">
        <constructor-arg ref="thingTwo"/>
        <constructor-arg ref="thingThree"/>
    </bean>

    <bean id="thingTwo" class="x.y.ThingTwo"/>

    <bean id="thingThree" class="x.y.ThingThree"/>
</beans>
~~~
다른 `bean`이 참조 될 때, 그 타입이 알려지고 일치가 발생할 수 있습니다.(앞의 예제와 마찬가지로)  
`<value>true</value>`와 같은 간단한 유형이 사용되면 `Spring`은 값의 유형을 판별 할 수 없으므로 **도움없이 유형별로 일치시킬 수 없습니다.**  
다음 클래스를 고려하십시오.  
~~~java
package examples;

public class ExampleBean {

    // 궁극적 인 답을 계산하는 년수
    private int years;

    // 생명, 우주, 그리고 모든 것에 대한 해답
    private String ultimateAnswer;

    public ExampleBean(int years, String ultimateAnswer) {
        this.years = years;
        this.ultimateAnswer = ultimateAnswer;
    }
}
~~~

#### 생성자 인수 유형 일치
컨테이너는 `type` 특성을 사용하여 생성자 인수의 형식을 명시 적으로 지정하면 단순 형식과 일치하는 형식을 사용할 수 있습니다.  
다음 예제와 같이  
~~~xml
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg type="int" value="7500000"/>
    <constructor-arg type="java.lang.String" value="42"/>
</bean>
~~~
#### 생성자 인수 인덱스
다음 예제와 같이 **index 속성**을 사용하여 **생성자 인수의 인덱스를 명시 적으로 지정할 수 있습니다.**  
~~~xml
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg index="0" value="7500000"/>
    <constructor-arg index="1" value="42"/>
</bean>
~~~

여러 간단한 값의 모호성을 해결하는 것 외에도 **인덱스를 지정**하면 **생성자가 동일한 유형의 인수가 두 개인 모호성이 해결**됩니다.  

---
#### 중요
	인덱스는 0부터 시작합니다.
---

#### 생성자 인수 이름
다음 예제와 같이 **값 모호성 제거**에 **생성자 매개 변수 이름을 사용**할 수도 있습니다.
~~~xml
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg name="years" value="7500000"/>
    <constructor-arg name="ultimateAnswer" value="42"/>
</bean>
~~~
이 작업을 수행하려면 `Spring`에서 생성자의 매개 변수 이름을 찾을 수 있도록 디버그 플래그를 사용하도록 코드를 컴파일해야합니다.  
디버그 플래그로 코드를 컴파일 할 수 없거나 컴파일하지 않으려면
[@ConstructorProperties](https://docs.oracle.com/javase/6/docs/api/java/beans/ConstructorProperties.html)
`JDK` 어노테이션을 사용하여  
생성자 인수의 이름을 명시 적으로 지정할 수 있습니다.  
샘플 클래스는 다음과 같이 보일 것입니다.  

~~~java
package examples;

public class ExampleBean {

    // 필드 생략

    @ConstructorProperties({"years", "ultimateAnswer"})
    public ExampleBean(int years, String ultimateAnswer) {
        this.years = years;
        this.ultimateAnswer = ultimateAnswer;
    }
}
~~~


### Setter 기반 의존성 주입

**`Setter` 기반 `DI`** 는 **인수가없는 생성자** 또는 **빈을 인스턴스화 하기 위한 인수가 없는 정적 팩토리 메소드**를 호출 한 후  
`bean`에서 `setter` 메소드를 호출하는 컨테이너에 의해 수행됩니다.  

다음 예제에서는 **순수한 `Setter` 주입을 사용**하여 **종속성 주입 만 할 수있는 클래스**를 보여줍니다.  
이 클래스는 일반적인 Java입니다. 이것은 컨테이너 고유 인터페이스, 기본 클래스 또는 주석에 대한 종속성이없는 POJO입니다.  
~~~java
public class SimpleMovieLister {

    // SimpleMovieLister는 MovieFinder에 의존합니다.
    private MovieFinder movieFinder;

    // Spring 컨테이너가 MovieFinder를 삽입 할 수 있도록하는 setter 메소드
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // 실제로 삽입 된 MovieFinder를 사용하는 비즈니스 로직은 생략됩니다 ...
}
~~~
`ApplicationContext`는 자신이 관리하는 `bean`에 대한 **생성자 기반** 및 **`setter` 기반 DI**를 지원합니다.  
또한 **생성자 방식**을 통해 ***이미 일부 종속성이 주입 된 후에도*** **setter 기반 DI를 지원**합니다.  
`PropertyEditor` 인스턴스와 함께 사용하여 하나의 형식에서 다른 형식으로 속성을 변환하는 `BeanDefinition` 형식으로 종속성을 구성합니다.  
그러나 대부분의 `Spring` 사용자는 이러한 클래스를 직접 (프로그래밍 방식으로) 사용하지 않고  
`XML bean` 정의, 주석이 달린 구성 요소(즉, `@Component`, `@Controller` 등으로 주석 된 클래스)  
또는 `@Bean` 메소드를 사용한 `Java` 기반 `@Configuration` 클래스를 기반으로 합니다.  
이러한 소스는 내부적으로 `BeanDefinition`의 인스턴스로 변환되어 전체 `Spring IoC` 컨테이너 인스턴스를 로드하는 데 사용됩니다.  

생성자 기반 또는 setter 기반 DI?
**생성자 기반 및 setter 기반 DI를 혼합** 할 수 있으므로  
**필수 종속성 및 설정 메서드 또는 선택적 종속성에 대한 구성 메서드로** ***생성자를 사용하는 것이 좋습니다.***  
`setter` 메소드에서 **`@Required` 어노테이션**을 사용하면 속성을 **필수 종속성**으로 만들 수 있습니다.  

`Spring` 팀은 **일반적으로** 응용 프로그램 구성 요소를 **변경 불가능한 객체로 구현**하고  
**필요한 종속성이 null이 아닌지 확인하기 위해** ***생성자 삽입을 옹호합니다.***  
또한 **생성자가 주입 한 구성 요소**는 **완전히 초기화 된 상태**에서 항상 **클라이언트 (호출) 코드로 반환**됩니다.  
부수적으로, **많은 수의 생성자 인수**는 **나쁜 코드 냄새**이며, **클래스가 너무 많은 책임을 갖고 있으며 적절한 관심을 분리하기 위해  
리팩토링되어야 함을 의미합니다.**  

**`setter` 주입**은 주로 **클래스 내에서 적절한 기본값을 할당 할 수 있는** ***선택적 종속성에 대해서만 사용해야합니다.***  
그렇지 않으면 **코드에서 종속성을 사용하는 모든 곳**에서 **`null`이 아닌 검사를 수행**해야합니다.  
**`setter` 주입**의 한 가지 **이점**은 `setter` 메소드가 해당 클래스의 객체를 **나중에 재구성**하거나  
**다시 주입** 할 수있게 만드는 것입니다.  
따라서 [JMX MBeans](https://docs.spring.io/spring/docs/5.1.2.RELEASE/spring-framework-reference/integration.html#jmx)
를 통한 관리는 **세터 주입에 대한 매력적인 사용 사례**입니다.  

특정 클래스에 가장 적합한 `DI` 스타일을 사용하십시오.  
때로는 소스가없는 `third-party class`를 다룰 때 선택이 이루어집니다.  
예를 들어 `third-party class`가 **어떠한 `setter` 메소드도 노출시키지 않으면 생성자 주입이 `DI`의 유일하게 사용 가능한 형태** 일 수 있습니다.  

#### 종속성 해결 프로세스
컨테이너는 다음과 같이 Bean 의존성 분석을 수행합니다.  
- **`ApplicationContext`** 는 **모든 `bean`** 을 설명하는 **구성 메타 데이터로 작성되고 초기화**됩니다.
**구성 메타 데이터**는 **`XML`, `Java` 코드 또는 주석**으로 지정할 수 있습니다.
- 각 **`bean`에 대해 종속성**은 **프로퍼티, 생성자 인수 또는 static-factory 메소드에 대한 인수 형식**으로 표현됩니다
(일반 생성자 대신 이를 사용하는 경우).
이러한 종속성은 bean이 실제로 작성 될 때 bean에 제공됩니다.  
- 각 **속성 또는 생성자 인수**는 ***설정하려는 값의*** **실제 정의** 이거나 컨테이너의 **다른 bean에 대한 참조**입니다.  
- 값인 각 **property 또는 생성자 인수**는 지정된 형식에서 **해당 property 또는 생성자 인수**의 **실제 유형으로 변환**됩니다.
기본적으로, Spring은 **문자열 형식으로 제공된 값**을 **int, long, String, boolean** 등과 같은 모든 내장 유형으로 변환 할 수 있습니다.  


`Spring` 컨테이너는 **컨테이너가 생성** 될 때 **각 bean의 설정을 검증**합니다.  
그러나 **`bean properties`** 자체는 **`bean`이 실제로 작성 될 때까지 설정되지 않습니다.**  
**컨테이너가 생성** 될 때 **싱글 랭크이고 미리 인스턴스화되도록 설정된 (기본값) 빈**이 만들어 집니다.  
**범위**는 **`Bean Scopes`에서 정의**됩니다. 그렇지 않으면 `Bean`은 요청 된 경우에만 작성됩니다.  
빈의 생성은 잠재적으로 빈의 그래프가 생성되도록합니다. 빈의 의존성과 그 종속성의 종속성 (등등)이 생성되고 할당되기 때문입니다.  
이러한 종속성들 사이의 resolution 불일치는 늦게 나타납니다. 즉, 영향을받는 빈을 처음 만들 때 나타납니다.  

#### 순환 의존성

주로 생성자 주입을 사용하는 경우 확인할 수 없는 순환 종속성 시나리오를 만들 수 있습니다.  
예)  
클래스 `A`는 생성자 삽입을 통해 클래스 `B`의 인스턴스를 필요로 하고  
클래스 `B`는 생성자 주입을 통해 클래스 `A`의 인스턴스를 필요로합니다.  
클래스 `A`와 `B`가 **서로 주입 되도록 `Bean`을 구성**하면 `Spring IoC` 컨테이너는 **런타임**에  
이 **순환 참조를 감지**하고 `BeanCurrentlyInCreationException`을 던집니다.  

**한 가지 가능한 솔루션**은 생성자가 아닌 **`setter`가 구성**하도록 **일부 클래스의 소스 코드를 편집**하는 것입니다.  
또는 ***생성자 주입을 피하고*** **`setter` 주입 만 사용**하십시오.  
즉, ***권장되지는 않지만*** **`setter` 주입을 사용하여 순환 종속성을 구성** 할 수 있습니다.  

일반적인 경우(순환 종속성이 없음)와 달리, `Bean A`와 `Bean B` 사이의 순환 종속성은 완전히 초기화되기 전에  
`Bean` 중 하나가 다른 것으로 주입되도록합니다 (a classic chicken-and-egg scenario).  

---

일반적으로 Spring이 올바른 일을 할 수 있다고 믿을 수 있습니다.  
컨테이너가로드 될 때 존재하지 않는 bean에 대한 참조 및 순환 종속성과 같은 구성 문제점을 감지합니다.  
Spring은 속성을 설정하고 빈이 실제로 생성 될 때 가능한 한 늦게 의존성을 해결합니다.
즉, 올바르게로드 된 Spring 컨테이너는 나중에 객체 또는 그 의존성 중 하나를 생성하는 데 문제가있는 경우  
객체를 요청할 때 예외를 생성 할 수 있습니다.  
예를 들어, bean은 누락되었거나 유효하지 않은 특성으로 인해 예외를 던집니다.  
일부 구성 문제에 대한 가시성을 잠재적으로 지연시키는 이유는 `ApplicationContext` 구현이  
기본적으로 싱글 톤 `bean`을 미리 인스턴스화하기 때문 입니다.  
이러한 `bean`이 실제로 필요하기 전에 이들 `bean`을 생성하기 위한 약간의 초기 시간과 메모리를 사용해서,  
나중에 `ApplicationContext`가 생성 될 때 구성 문제를 발견하게 됩니다.  
싱글 톤 `Bean`이 미리 인스턴스화 되기 보다는 느리게 초기화 되도록 이 기본 작동을 무시할 수 있습니다.  

순환 종속성이없는 경우, 하나 이상의 협업 bean이 종속 bean에 주입 될 때,  
각 협업 bean은 종속 bean에 주입되기 전에 완전히 구성됩니다.  
즉, `bean` `A`가 `bean` `B`에 종속성이 있는 경우 `Spring IoC` 컨테이너는  
`bean` `A`에서 `setter` 메소드를 호출하기 전에 `bean` `B`를 완전히 구성합니다.  
즉, Bean이 인스턴스화되고 (사전 인스턴스화 된 싱글 톤이 아닌 경우)  
종속성이 설정되고 관련 라이프 사이클 메소드 (구성된 init 메소드 또는 InitializingBean 콜백 메소드 등)가 호출됩니다.  

#### 의존성 삽입의 예
다음 예제는 setter 기반 DI에 XML 기반 구성 메타 데이터를 사용합니다.  
Spring XML 설정 파일의 작은 부분은 다음과 같이 몇 가지 bean 정의를 지정합니다.  
~~~xml
<bean id="exampleBean" class="examples.ExampleBean">
    <!-- 중첩 된 ref 요소를 사용하는 setter 주입 -->
    <property name="beanOne">
        <ref bean="anotherExampleBean"/>
    </property>

    <!-- neater? ref 속성을 사용한 setter 주입 -->
    <property name="beanTwo" ref="yetAnotherBean"/>
    <property name="integerProperty" value="1"/>
</bean>

<bean id="anotherExampleBean" class="examples.AnotherBean"/>
<bean id="yetAnotherBean" class="examples.YetAnotherBean"/>
~~~

다음 예제는 해당 ExampleBean 클래스를 보여줍니다.  

~~~java
public class ExampleBean {

    private AnotherBean beanOne;

    private YetAnotherBean beanTwo;

    private int i;

    public void setBeanOne(AnotherBean beanOne) {
        this.beanOne = beanOne;
    }

    public void setBeanTwo(YetAnotherBean beanTwo) {
        this.beanTwo = beanTwo;
    }

    public void setIntegerProperty(int i) {
        this.i = i;
    }
}
~~~
앞의 예제에서 `setter`는 `XML` 파일에 지정된 **속성과 일치하도록 선언**됩니다.  
다음 예제에서는 **생성자 기반 `DI`를 사용**합니다.  
~~~xml
<bean id="exampleBean" class="examples.ExampleBean">
    <!-- 중첩 된 ref 요소를 사용한 생성자 삽입 -->
    <constructor-arg>
        <ref bean="anotherExampleBean"/>
    </constructor-arg>

    <!-- neater ref 속성을 사용하는 생성자 삽입 -->
    <constructor-arg ref="yetAnotherBean"/>

    <constructor-arg type="int" value="1"/>
</bean>

<bean id="anotherExampleBean" class="examples.AnotherBean"/>
<bean id="yetAnotherBean" class="examples.YetAnotherBean"/>
~~~
다음 예제는 해당 `ExampleBean` 클래스를 보여줍니다.
~~~java
public class ExampleBean {

    private AnotherBean beanOne;

    private YetAnotherBean beanTwo;

    private int i;

    public ExampleBean(
        AnotherBean anotherBean, YetAnotherBean yetAnotherBean, int i) {
        this.beanOne = anotherBean;
        this.beanTwo = yetAnotherBean;
        this.i = i;
    }
}
~~~
`Bean` 정의에 지정된 생성자 인수는 `ExampleBean`의 생성자에 대한 인수로 사용됩니다.  

이제 생성자를 사용하는 대신 `Spring`이 **정적 팩토리 메소드를 호출**하여  
**객체의 인스턴스를 반환**하도록 지시 한 이 예제의 변형을 고려해보십시오.  
~~~xml
<bean id="exampleBean" class="examples.ExampleBean" factory-method="createInstance">
    <constructor-arg ref="anotherExampleBean"/>
    <constructor-arg ref="yetAnotherBean"/>
    <constructor-arg value="1"/>
</bean>

<bean id="anotherExampleBean" class="examples.AnotherBean"/>
<bean id="yetAnotherBean" class="examples.YetAnotherBean"/>
~~~
다음 예제는 해당 `ExampleBean` 클래스를 보여줍니다.  
~~~java
public class ExampleBean {

    // a private constructor
    private ExampleBean(...) {
        ...
    }

    // static 팩토리 메소드.
    // 이 메소드에 대한 인수는 실제로 사용되는 인수에 관계없이
    // 리턴되는 bean의 종속성으로 간주 될 수 있습니다.
    public static ExampleBean createInstance (
        AnotherBean anotherBean, YetAnotherBean yetAnotherBean, int i) {

        ExampleBean eb = new ExampleBean (...);
        // 일부 다른 작업들...
        return eb;
    }
}
~~~

**정적 팩토리 메소드에 대한 인수**는 `<constructor-arg/>` 요소에 의해 제공됩니다.  
실제로 생성자가 사용 된 것과 정확히 같습니다.  
`Spring`의 **변환 서비스**는 이러한 값을 `String`에서 실제 속성 또는 인수 유형으로 변환하는 데 사용됩니다.  
`factory` 메소드가 리턴하는 클래스의 유형은 정적 팩토리 메소드를 포함하는 클래스와 동일한 유형 일 필요는 없습니다 (이 예에서는 그렇다).  
인스턴스 (비 정적)팩토리 메소드는 본질적으로 동일한 방식으로 사용할 수 있습니다(class 속성 대신 factory-bean 속성을 사용하는 것과는 별도).  
