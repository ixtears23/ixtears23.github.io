---
layout: spring-framework-post
title: "Spring Autowire"
date: 2018-01-08
excerpt: ""
tags: [springframework]
spring-framework: true
comments: true
---


 # Spring autowire

 > Spring 프레임 워크에서는 자동 배선 기능을 사용하여 빈을 자동으로 연결할 수 있다.  
 > 이를 가능하게하려면 <bean>에 "autowire"속성을 정의하면 된다.  

~~~xml
<bean id="customer" class="com.mkyong.common.Customer" autowire="byName" />
~~~

 > Spring에서는 5 개의 Auto-wiring mode가 지원된다.
 * no - default, Auto-wiring 없음, "ref"속성을 통해 수동으로 설정
 * byName - 속성 이름에 의한 Auto-wiring. Bean의 이름이 다른 Bean 속성의 이름과 같으면 자동으로 연결한다.
 * byType - 특성 데이터 유형별 Auto-wiring. Bean의 데이터 타입이 다른 Bean 속성의 데이터 타입과 호환되면 자동으로 연결한다.   
 * constructor - 생성자 인수의 byType Mode  
 * autodetect - 기본 생성자가 발견되면 "autowired by constructor"로 연결되고, 그렇지 않으면 "autowired by type"으로 연결된다.  

 1. 이것은 기본 모드이며, 'ref'속성을 통해 빈을 연결해야합니다.
~~~java
public class Customer
{
 private Person person;
 public Customer(Person person) {
  this.person = person;
 }
 public void setPerson(Person person) {
  this.person = person;
 }
 //...
}
~~~
~~~java
public class Person
{
 //...
}
~~~
~~~xml
<bean id="customer" class="com.mkyong.common.Customer">
                  <property name="person" ref="person" />
</bean>
<bean id="person" class="com.mkyong.common.Person" />
~~~

 2. Auto-Wiring ‘byName’
 > bean을 특성 이름으로 자동 연결하십시오.  
 > 이 경우 "person"bean의 이름이 "customer"bean의 속성 ( "person")의 이름과 동일하므로
 > Spring은 setter 메소드 인 "setPerson (Person person)"을 통해 자동으로 연결한다.

~~~xml
<bean id="customer" class="com.mkyong.common.Customer" autowire="byName" />
<bean id="person" class="com.mkyong.common.Person" />
~~~
 > byName으로 autowire를 사용하면 ref 태그를 더 이상 선언 할 필요가 없다.
 > "Address" bean이 "Customer" bean 필드의 setter 이름과 같은 한, Spring은 자동으로 연결한다.
 > xml에 선언한 bean id 와 Java class에 있는 setter 이름이 같아야 함.(예) setPay 라면 bean id가 Pay 일 때 자동 연결된다.
 > 만약 그럴일은 없겠지만 filed 명 private Pay pay 라고 쓰고 setPPay 라는 setter를 만들었다면 bean id가 PPay 일 때 자동 연결된다.

 * Beans
~~~java
public class Customer
{
 private Address address;
 //...
}
~~~
~~~java
public class Address
{
 private String fulladdress;
 //...
}
~~~
 * Spring Wiring

~~~xml
<bean id="customer" class="com.mkyong.common.Customer" >
 <property name="address" ref="address" />
</bean>
<bean id="address" class="com.mkyong.common.Address" >
 <property name="fulladdress" value="Block A 888, CA" />
</bean>
~~~
 * 위의 코드에서 아래 코드로 변경
~~~xml
<bean id="customer" class="com.mkyong.common.Customer" autowire="byName" />
<bean id="address" class="com.mkyong.common.Address" >
 <property name="fulladdress" value="Block A 888, CA" />
</bean>
~~~
 * output
~~~
Customer [address=Address [fulladdress=Block A 888, CA]]
~~~
 * 아래는 bean "addressABC"가 bean "customer"의 속성 이름과 일치하지 않으므로 wiring에 실패하게 된다.
~~~xml
<bean id="customer" class="com.mkyong.common.Customer" autowire="byName" />
<bean id="addressABC" class="com.mkyong.common.Address" >
 <property name="fulladdress" value="Block A 888, CA" />
</bean>
~~~
 * Output
~~~
Customer [address=null]
~~~


 # @Autowired
 > Spring에서는 지원하는 Auto-wiring mode(5개)를 모두 지원한다.  
 > 대부분의 경우 특정 bean에서만 autowired 속성이 필요할 수 있다.  
 > Spring에서는 @Autowired 어노테이션을 사용하여 setter 메소드, 생성자 또는 필드에서 bean을 자동으로 연결할 수있다.  
 > 또한 특정 bean에서 autowired 속성을 사용할 수 있습니다.  
 > @Autowired 어노테이션은 일치하는 데이터 유형으로 bean을 자동 연결한다.  




 * Marks a constructor, field, setter method or config method as to be autowired by Spring's dependency injection facilities
 * Spring의 의존성 주입 기능에 의해 자동 생성되도록 생성자, 필드, 설정 메소드 또는 구성 메소드를 표시합니다.

 * Only one constructor (at max) of any given bean class may carry this annotation, indicating the constructor to autowire when used as a Spring bean. Such a constructor does not have to be public.
 * 주어진 bean 클래스의 한 생성자 (max)만이 Spring bean으로 사용될 때 autowire에 대한 생성자를 나타내는이 annotation을 전달할 수 있습니다. 이러한 생성자는 공개 될 필요가 없습니다.

 * Fields are injected right after construction of a bean, before any config methods are invoked. Such a config field does not have to be public.
 * 모든 config 메소드가 호출되기 전에, 빈의 생성 직후에 필드가 주입됩니다. 이러한 설정 필드는 공개 될 필요가 없습니다.

 * Config methods may have an arbitrary name and any number of arguments; each of those arguments will be autowired with a matching bean in the Spring container. Bean property setter methods are effectively just a special case of such a general config method. Such config methods do not have to be public.
 * 구성 메소드에는 임의의 이름과 인수가있을 수 있습니다. 각각의 인수는 Spring 컨테이너에서 일치하는 bean으로 autowired 될 것이다. 빈 프로퍼티 설정 메소드는 실제로 이러한 일반적인 설정 메소드의 특수한 경우입니다. 이러한 설정 메소드는 public 일 필요는 없습니다.

 * In the case of multiple argument methods, the 'required' parameter is applicable for all arguments.
 * 다중 인수 메소드의 경우 '필수'매개 변수가 모든 인수에 적용 가능합니다.

 * In case of a Collection or Map dependency type, the container will autowire all beans matching the declared value type. In case of a Map, the keys must be declared as type String and will be resolved to the corresponding bean names.
 * Collection 또는 Map 종속성 유형의 경우 컨테이너는 선언 된 값 유형과 일치하는 모든 bean을 자동 연결합니다. Map의 경우, 키는 String 타입으로 선언되어야하며 해당 빈 이름으로 해석됩니다.

 * Note that actual injection is performed through a BeanPostProcessor which in turn means that you cannot use @Autowired to inject references into BeanPostProcessor or BeanFactoryPostProcessor types. Please consult the javadoc for the AutowiredAnnotationBeanPostProcessor class (which, by default, checks for the presence of this annotation).
 * 실제 주입은 BeanPostProcessor를 통해 수행되는데, 이는 다시 BeanPostProcessor 또는 BeanFactoryPostProcessor 유형에 참조를 주입하기 위해 @Autowired를 사용할 수 없음을 의미합니다. AutowiredAnnotationBeanPostProcessor 클래스 (기본적으로이 annotation이 있는지 검사)에 대한 javadoc을 참조하십시오.
