---
layout: post
title: "Srping Bean"
date: 2018-11-07
tags: [springframework,annotation]
excerpt: "Spring IoC 컨테이너에서 관리되는 Bean"
spring-framework: true
comments: true
---

**Spring IoC 컨테이너**는 하나 이상의 `Bean`을 관리한다.  
이 `Bean`은 사용자가 컨테이너에 제공하는 **구성 메타 데이터** (예 : XML `<bean/>` 정의의 양식)로 <U>작성</U>됩니다.  


컨테이너 내부에서 이러한 `bean` 정의는 (다른 정보들 사이에서) 다음 메타 데이터를 포함하는 `BeanDefinition` 객체로 표현됩니다.  
- 패키지 수식 클래스 명 : 일반적으로, 정의되고있는 bean의 실제 구현 클래스.  
- `Bean`이 컨테이너에서 작동하는 방법 (범위, 수명주기 콜백 등)을 나타내는 `Bean` 동작 구성 요소.  
- `Bean`이 작업을 수행하는 데 필요한 다른 `Bean`에 대한 참조.  
이러한 참조는 협력자(collaborators) 또는 종속성(dependencies)이라고도 합니다.  
- 새로 생성 된 객체에 설정할 기타 구성 설정: 예를 들어 풀의 크기 제한 또는 연결 풀을 관리하는 `Bean`에서 사용할 연결 수를 지정합니다.  

##### bean 정의

|----
|Property|설명|
|:--------|:--------|
|Class|[Instantiating Beans](https://docs.spring.io/spring/docs/5.1.2.RELEASE/spring-framework-reference/core.html#beans-factory-class)|
|----
|Name|[Naming Beans](https://docs.spring.io/spring/docs/5.1.2.RELEASE/spring-framework-reference/core.html#beans-beanname)|
|----
|Scope|[Bean Scopes](https://docs.spring.io/spring/docs/5.1.2.RELEASE/spring-framework-reference/core.html#beans-factory-scopes)|
|----
|Constructor arguments|[Dependency Injection](https://docs.spring.io/spring/docs/5.1.2.RELEASE/spring-framework-reference/core.html#beans-factory-collaborators)|
|----
|Properties|[Dependency Injection](https://docs.spring.io/spring/docs/5.1.2.RELEASE/spring-framework-reference/core.html#beans-factory-collaborators)|
|----
|Autowiring mode|[Autowiring Collaborators](https://docs.spring.io/spring/docs/5.1.2.RELEASE/spring-framework-reference/core.html#beans-factory-autowire)|
|----
|Lazy initialization mode|[Lazy-initialized Beans](https://docs.spring.io/spring/docs/5.1.2.RELEASE/spring-framework-reference/core.html#beans-factory-lazy-init)|
|----
|Initialization method|[Initialization Callbacks](https://docs.spring.io/spring/docs/5.1.2.RELEASE/spring-framework-reference/core.html#beans-factory-lifecycle-initializingbean)|
|----
|Destruction method|[Destruction Callbacks](https://docs.spring.io/spring/docs/5.1.2.RELEASE/spring-framework-reference/core.html#beans-factory-lifecycle-disposablebean)|
|----
{: rules="groups"}

`ApplicationContext` 구현은 컨테이너 외부에서 작성된 기존 객체의 등록을 허용 합니다.
이것은 `BeanFactory` `DefaultListableBeanFactory` 구현을 리턴하는 `getBeanFactory()` 메소드를 통해  `ApplicationContext`의 `BeanFactory`에 액세스함으로써 수행된다.  
`DefaultListableBeanFactory`는 `registerSingleton(..)` 및 `registerBeanDefinition(..)` 메소드를 통해 이 등록을 지원합니다.  
그러나 일반적인 응용 프로그램은 `Bean` 정의 메타 데이터를 통해 정의 된 `Bean`에서만 작동합니다.  


### 중요
**`Bean` 메타 데이터와 수동으로 제공되는 싱글 톤 인스턴스**는
자동 와이어 링 및 기타 인트로 스펙션 단계에서 컨테이너가 적절히 추론 할 수 있도록 **가능한 빨리 등록**해야합니다.  
기존 메타 데이터와 기존 싱글 톤 인스턴스를 재정의하는 것은 어느 정도 지원되지만
**런타임에 새로운 빈의 등록 (팩토리에 대한 라이브 액세스와 동시에)은 공식적으로 지원되지 않으며**
**동시 액세스 예외**, **빈 컨테이너의 일관성없는 상태**가 발생합니다.  

## Beans 인스턴스화 하기

**`bean`정의**는 본질적으로 **하나 이상의 객체를 생성하는 방법**입니다.  

컨테이너는 명명 된 `bean`에 대한 레시피를 보고
`bean` 정의에 의해 캡슐화 된 **설정 메타 데이터를 사용**하여 **실제 객체를 생성**(또는 획득) 합니다.  

**`XML`기반 구성** 메타 데이터를 사용하는 경우,
`<bean/>` 요소의 **`class` 속성**에서 **인스턴스화 할 객체의 유형 (또는 클래스)을 지정**합니다.  
이 **클래스 속성** (내부적으로 `BeanDefinition` 인스턴스의 `Class` 속성)은 일반적으로 **필수**입니다.  
(예외에 대해서는 인스턴스 팩토리 메서드 및 `Bean` 정의 상속을 사용하여 인스턴스화를 참조하십시오.)  
다음 **두 가지 방법** 중 하나로 **클래스 속성을 사용할 수 있습니다.**  
 - **컨테이너 자체가 생성자를 직접 호출**하여 **`bean`을 직접 생성하는 경우**에 **생성 될 bean 클래스를 지정**합니다.  
 **`java` 코드의 `new` 연산자**와 ***약간 동등*** 합니다.  
 - ***드문 경우이지만*** 컨테이너가 빈을 작성하기 위해 **클래스의 정적 팩토리 메소드를 호출하는 경우**,
 객체를 생성하기 위해서 불려가는 **`static` 팩토리 메소드를 포함한 실제의 클래스를 지정**합니다.
`static` 팩토리 메소드의 호출로부터 돌려 주어진 오브젝트 형은, **같은 클래스 또는 다른 클래스 일 가능성이 있습니다.**  


> **내부 클래스 이름**
>
> **정적 중첩 클래스에 대한 bean 정의를 구성**하려면 **중첩 클래스의 binary(2진) 이름을 사용**해야합니다.
>
> 예를 들어, `com.example` 패키지에 `SomeThing`이라는 클래스가 있고이 `SomeThing` 클래스에 `OtherThing`이라는
> 정적 중첩 클래스가있는 경우 `bean` 정의의 **`class` 속성 값**은 **`com.example.SomeThing$OtherThing`** 이 됩니다.
>
> 이름에 **`$` 문자**를 사용하여 **중첩 클래스 이름을 외부 클래스 이름과 구분**합니다.  

### 생성자로 인스턴스화하기
**생성자 접근법으로 빈을 생성**하면 **모든 일반(normal) 클래스가 Spring에서 사용 가능하고 호환 가능합니다.**  
즉, **개발중인 클래스**는 **특정 인터페이스를 구현하거나 특정 방식으로 코딩 할 필요가 없습니다.**  
**간단히 bean 클래스를 지정하기만 하면 됩니다.**  
특정 bean에 어떤 유형의 IoC를 사용하는지에 따라, ***기본 (empty) 생성자가 필요할 수 있습니다.***  


`Spring IoC` 컨테이너는 관리 하고자 하는 클래스를 실제 관리 할 수 있습니다.  
진정한(true) `JavaBeans` 관리에 국한되지 않습니다.  
대부분의 `Spring` 사용자는 디폴트 (인수 없음) 생성자와 적절한 `setter` 및 `getter`를 컨테이너의 속성 다음에 모델링한
실제 `JavaBeans`를 선호합니다.  
컨테이너에 더 많은 `non-bean` 스타일 클래스를 가질 수도 있습니다.  
예를 들어, 절대적으로 `JavaBean` 스펙을 따르지 않는 레거시 연결 풀을 사용해야 하는 경우에도 `Spring`은 이를 관리 할 수 있습니다.  


**`XML` 기반 구성 메타 데이터**를 사용하면 다음과 같이 `Bean` 클래스를 지정할 수 있습니다.  
~~~xml
<bean id="exampleBean" class="examples.ExampleBean"/>

<bean name="anotherExample" class="examples.ExampleBeanTwo"/>
~~~

생성자에 인수를 제공하는 메커니즘 (필요한 경우) 및 객체가 생성 된 후에
객체 인스턴스 속성을 설정하는 방법에 대한 자세한 내용은
[Injection Dependencies](https://docs.spring.io/spring/docs/5.1.2.RELEASE/spring-framework-reference/core.html#beans-factory-collaborators)
를 참조하십시오.  

### static 팩토리 메서드로 인스턴스화
**정적 팩토리 메소드로 작성한 bean을 정의** 할 때,  
**`class` 속성을 사용**하여 **정적 팩토리 메소드를 포함하는 클래스를 지정**하고  
**`factory-method`라는 속성을 사용**하여 **팩토리 메소드 자체의 이름을 지정**하십시오.  
이 메소드(나중에 설명하는 선택적 인수 사용)를 호출 할 수 있어야 하며 라이브 객체를 반환 할 수 있습니다.  
이 객체는 이후 **생성자를 통해 만들어진 것처럼 취급**됩니다.  
그러한 `bean` 정의를 사용하는 한 가지는 **레거시 코드에서 정적 팩토리를 호출하는 것**입니다.  

다음 `bean` 정의는 `factory` 메소드를 호출하여 `bean`을 작성하도록 지정합니다.  
이 정의는 **반환 된 객체의 유형(클래스)을 지정하지 않으며 `factory` 메소드를 포함하는 클래스 만 지정**합니다.  
이 예제에서 `createInstance()` 메서드는 **정적 메서드** 여야 합니다.  
다음 예제에서는 팩토리 메서드를 지정하는 방법을 보여줍니다.  

~~~xml
<bean id="clientService"
    class="examples.ClientService"
    factory-method="createInstance"/>
~~~

다음 예제는 앞의 `bean` 정의로 동작 할 클래스를 보여 줍니다.  
~~~java
public class ClientService {
    private static ClientService clientService = new ClientService();
    private ClientService() {}

    public static ClientService createInstance() {
        return clientService;
    }
}
~~~

팩토리 메소드에 인수(선택 사항)를 제공하고 팩토리에서 오브젝트가 리턴 된 후  
오브젝트 인스턴스 등록 정보를 설정하는 메커니즘에 대한 자세한 내용은
[종속성 및 구성 상세 정보](https://docs.spring.io/spring/docs/5.1.2.RELEASE/spring-framework-reference/core.html#beans-factory-properties-detailed)
를 참조하십시오.  


### 인스턴스 팩토리 메서드를 사용하여 인스턴스화

**정적 팩토리 메소드를 통한 인스턴스화**와 **유사**하게,  
**인스턴스 팩토리 메소드를 사용한 인스턴스화**는 **컨테이너에서 기존 빈의 비 정적 메소드를 호출하여 새 빈을 작성**합니다.  
이 메커니즘을 사용하려면 `class` 속성을 **공백**으로 두고 **`factory-bean` 속성**에서  
**객체를 만들기 위해 호출 할 인스턴스 메소드를 포함**하는 현재(또는 상위 또는 조상) 컨테이너에서 **bean의 이름을 지정**하십시오.  
**팩토리 메소드 자체의 이름**을 **`factory-method` 속성으로 설정**하십시오.  
다음 예제는 그러한 bean을 구성하는 방법을 보여줍니다.
~~~xml
<!-- createInstance()라는 메소드가 포함 된 factory bean -->
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
    <!-- inject any dependencies required by this locator bean -->
</bean>

<!-- factory bean을 통해 생성 될 bean -->
<bean id="clientService"
    factory-bean="serviceLocator"
    factory-method="createClientServiceInstance"/>
~~~
다음 예제는 해당 `Java` 클래스를 보여줍니다.
~~~java
public class DefaultServiceLocator {

    private static ClientService clientService = new ClientServiceImpl();

    public ClientService createClientServiceInstance() {
        return clientService;
    }
}
~~~
다음의 예와 같이, **1개의 팩토리 클래스**는 **하나 이상의 팩토리 메소드**를 가질 수가 있습니다.  
~~~xml
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
    <!-- 해당 빈에 필요한 모든 의존성 삽입 -->
</bean>

<bean id="clientService"
    factory-bean="serviceLocator"
    factory-method="createClientServiceInstance"/>

<bean id="accountService"
    factory-bean="serviceLocator"
    factory-method="createAccountServiceInstance"/>
~~~
다음 예제는 해당 `Java` 클래스를 보여줍니다.
~~~java
public class DefaultServiceLocator {

    private static ClientService clientService = new ClientServiceImpl();

    private static AccountService accountService = new AccountServiceImpl();

    public ClientService createClientServiceInstance() {
        return clientService;
    }

    public AccountService createAccountServiceInstance() {
        return accountService;
    }
}
~~~

이 접근법은 **`factory bean` 자체**가 __의존성 주입(DI)__ 을 통해 **관리되고 구성** 될 수 있음을 보여줍니다.  

---
#### 중요!!
 Spring 문서에서 **factory bean**은 **Spring 컨테이너에 구성**되고  
 **인스턴스 또는 정적 팩토리 메소드를 통해 객체를 생성하는 bean**을 나타냅니다.  
 반대로, **FactoryBean**(대문자사용에주의)은 Spring 관련
[`FactoryBean`](https://docs.spring.io/spring/docs/5.1.2.RELEASE/spring-framework-reference/core.html#beans-factory-extension-factorybean)
을 말합니다.
