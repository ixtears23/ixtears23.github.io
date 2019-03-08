---
layout: spring-framework-post
title: "Bean Scanning"
date: 2018-01-08
excerpt: ""
tags: [springframework,bean]
spring-framework: true
comments: true
---


# Bean Scanning
 > 일반적으로 XML bean 설정 파일에 모든 bean이나 컴포넌트를 선언하여   
 > Spring 컨테이너가 bean이나 컴포넌트를 감지하고 등록 할 수 있도록한다.   
 > 사실, Spring은 사전 정의 된 프로젝트 패키지에서 빈을 자동 스캔, 감지 및 인스턴스화 할 수 있으며,    
 > XML 파일에서 지루한 빈 선언을 수행 할 필요가 없다.   

 ##### Spring에서 수동으로 구성 요소 선언과 자동 구성 요소 검색 사이의 다른 점


 ### 구성 요소를 수동으로 선언
 > Spring에서 bean을 선언하는 일반적인 방법

 * Normal bean.
~~~java
public class CustomerDAO
{
 @Override
 public String toString() {
  return "Hello , This is CustomerDAO";
 }
}
~~~
 * DAO layer.
~~~java
import com.mkyong.customer.dao.CustomerDAO;
public class CustomerService
{
 CustomerDAO customerDAO;
 public void setCustomerDAO(CustomerDAO customerDAO) {
  this.customerDAO = customerDAO;
 }
 @Override
 public String toString() {
  return "CustomerService [customerDAO=" + customerDAO + "]";
 }
}
~~~
 * 빈 환경 설정 파일 (Spring-Customer.xml)은 Spring의 일반적인 환경 설정이다.
~~~xml
<beans xmlns="http://www.springframework.org/schema/beans"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xsi:schemaLocation="http://www.springframework.org/schema/beans
 http://www.springframework.org/schema/beans/spring-beans-2.5.xsd">
 <bean id="customerService" class="com.mkyong.customer.services.CustomerService">
  <property name="customerDAO" ref="customerDAO" />
 </bean>
 <bean id="customerDAO" class="com.mkyong.customer.dao.CustomerDAO" />
</beans>
~~~
 * Run it
~~~java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import com.mkyong.customer.services.CustomerService;
public class App
{
    public static void main( String[] args )
    {
     ApplicationContext context =
       new ClassPathXmlApplicationContext(new String[] {"Spring-Customer.xml"});
     CustomerService cust = (CustomerService)context.getBean("customerService");
     System.out.println(cust);
    }
}
~~~
 * output
~~~java
CustomerService [customerDAO=Hello , This is CustomerDAO]
~~~
 ### Auto Components Scanning
 * `@Component` annotation을 달아 클래스가 자동 스캔 구성 요소임을 나타낸다.
~~~java
import org.springframework.stereotype.Component;
@Component
public class CustomerDAO
{
 @Override
 public String toString() {
  return "Hello , This is CustomerDAO";
 }
}
~~~
 * DAO 계층에 @Component를 추가하여 자동 스캔 구성 요소임을 나타낸다.
~~~java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import com.mkyong.customer.dao.CustomerDAO;
@Component
public class CustomerService
{
 @Autowired
 CustomerDAO customerDAO;
 @Override
 public String toString() {
  return "CustomerService [customerDAO=" + customerDAO + "]";
 }
}
~~~
 > "context : component"를 빈 환경 설정 파일에 넣으면 Spring에서 자동 스캐닝 기능이 활성화된다.  
 > base-package는 구성 요소가 어디에 저장되어 있는지 알려주고, Spring은이 폴더를 검색하여  
 > (`@Component로` annotation 처리 된) 빈을 찾고 Spring 컨테이너에 등록한다.  
~~~xml
<beans xmlns="http://www.springframework.org/schema/beans"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xmlns:context="http://www.springframework.org/schema/context"
 xsi:schemaLocation="http://www.springframework.org/schema/beans
 http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
 http://www.springframework.org/schema/context
 http://www.springframework.org/schema/context/spring-context-2.5.xsd">
 <context:component-scan base-package="com.mkyong.customer" />
</beans>
~~~
 * Run it
~~~java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import com.mkyong.customer.services.CustomerService;
public class App
{
    public static void main( String[] args )
    {
     ApplicationContext context =
        new ClassPathXmlApplicationContext(new String[] {"Spring-AutoScan.xml"});
     CustomerService cust = (CustomerService)context.getBean("customerService");
     System.out.println(cust);
    }
}
~~~
 * output
~~~
CustomerService [customerDAO=Hello , This is CustomerDAO]
~~~
 > 이것은 Auto Components Scanning이 Spring에서 작동하는 방법이다.  

----

 ### Custom auto scan component name (사용자 정의 component명 auto scan)
 > 기본적으로 Spring은 컴포넌트의 첫 문자를 'CustomerService'에서 'customerService'로 줄인다.   
 > 그리고 'customerService'라는 이름으로이 구성 요소를 검색 할 수 있다.  


 > 구성 요소의 사용자 정의 이름을 작성하려면 다음과 같이 사용자 정의 이름을 입력.

~~~
@Service("AAA")
public class CustomerService
...
~~~
 > 이제 'AAA'라는 이름으로 검색 할 수 있다.
~~~
CustomerService cust = (CustomerService)context.getBean("AAA");
~~~
 * Auto Components Scan Annotation Types

 > Spring 2.5에는 4 가지 유형의 Auto Components Scan Annotation이 있다.
 * `@Component` - 자동 스캔 구성 요소를 나타낸다.
 * `@Repository` - 지속성 계층의 DAO 구성 요소를 나타낸다.
 * `@Service` - 비즈니스 계층의 서비스 구성 요소를 나타낸다.
 * `@Controller` - 프리젠 테이션 레이어의 컨트롤러 구성 요소를 나타낸다.

 > 그래서, 어느 것이 사용할 것인가? 그건 중요하지 않다.
 > `@Repository`, `@Service` 또는 `@Controller`의 소스 코드를 보자.

 * `@Repository`
~~~java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Repository {
 String value() default "";
}
~~~
 * `@Service`
~~~java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Service {
 /**
  * The value may indicate a suggestion for a logical component name,
  * to be turned into a Spring bean in case of an autodetected component.
  * @return the suggested component name, if any
  */
 String value() default "";
}
~~~

 * `@Controller`
~~~java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Controller {
 /**
  * The value may indicate a suggestion for a logical component name,
  * to be turned into a Spring bean in case of an autodetected component.
  * @return the suggested component name, if any
  */
 String value() default "";
}
~~~

 > `@ Repository`, `@ Service` 또는 `@Controller`가 모두 `@Component`로 annotation 처리되었음을 알게 될 것이다.
 > 자동 스캔을 위해 모든 구성 요소에 `@Component` 만 사용할 수 있나?
 > 예, 가능합니다. 그리고 Spring은 `@Component`가 annotation 된 모든 구성 요소를 자동으로 스캔한다.
 > 잘 작동하지만 좋은 방법은 아니다.
 > 가독성을 위해 다음과 같이 코드를 더 읽기 쉽게 만들기 위해
 > 지정된 레이어에 대해 `@ Repository`, `@ Service` 또는 `@Controller`를 항상 선언해야 한다.

 * DAO layer
~~~java
import org.springframework.stereotype.Repository;
@Repository
public class CustomerDAO
{
 @Override
 public String toString() {
  return "Hello , This is CustomerDAO";
 }
}
~~~
 * Service layer
~~~java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import com.mkyong.customer.dao.CustomerDAO;
@Service
public class CustomerService
{
 @Autowired
 CustomerDAO customerDAO;
 @Override
 public String toString() {
  return "CustomerService [customerDAO=" + customerDAO + "]";
 }
}
~~~
