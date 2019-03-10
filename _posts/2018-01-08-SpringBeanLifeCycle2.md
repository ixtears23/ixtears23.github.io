---
layout: spring-framework-post
title: "SpringBeanLifeCycle[2]"
date: 2018-01-08
excerpt: ""
tags: [springframework,bean]
spring-framework: true
comments: true
---



스프링 bean factory는 스프링 컨테이너를 통해 만들어진 빈의 라이프 사이클을 관리한다.
bean의 lifeCycle은 두 가지 그룹으로 크게 분류 할 수있는 콜백 메소드로 구성된다.

 * 초기화 이후 콜백 메소드
 * 파기 이전 콜백 메소드

 > Spring 프레임 워크는 bean의 생명주기 이벤트를 제어하기 위해 다음과 같은 4 가지 방법을 제공한다.

 1. InitializingBean 및 DisposableBean 콜백 인터페이스
 2. 특정 동작을위한 기타 Aware 인터페이스
 3. bean 설정 파일의 커스텀 init ()과 destroy () 메소드
 4. @PostConstruct 및 @PreDestroy 주석

 ## InitializingBean 및 DisposableBean 콜백 인터페이스
 ##### org.springframework.beans.factory.InitializingBean 인터페이스는  
 ##### 빈에 필요한 모든 프로퍼티가 컨테이너에 의해 설정된 후에  
 ##### 빈이 초기화 작업을 수행 할 수 있도록 해준다.  
 ##### InitializingBean 인터페이스는 단일 메서드를 지정한다.
    void afterPropertiesSet() throws Exception;
 ##### bean 클래스를 스프링 컨테이너에 단단히 연결하기 때문에 bean을 초기화하는 것은 더 좋은 방법이 아니다.  
 ##### 더 나은 접근법은 applicationContext.xml 파일의 bean 정의에서 "init-method"속성을 사용하는 것이다.
 ##### 비슷하게, org.springframework.beans.factory.DisposableBean 인터페이스를 구현하면  
 ##### 빈을 포함하는 컨테이너가 파기 될 때 빈이 콜백을 얻을 수있다.  
 ##### DisposableBean 인터페이스는 단일 메서드를 지정한다.
    void destroy() throws Exception;
 > 위의 인터페이스를 구현 한 샘플 빈은 다음과 같다.

~~~java
import org.springframework.beans.factory.DisposableBean;
import org.springframework.beans.factory.InitializingBean;

public class DemoBeanTypeOne implements InitializingBean, DisposableBean
{
    //Other bean attributes and methods

    @Override
    public void afterPropertiesSet() throws Exception
    {
        //Bean initialization code
    }

    @Override
    public void destroy() throws Exception
    {
        //Bean destruction code
    }
}
~~~
 ## 특정 동작을위한 기타 Aware 인터페이스
 ##### 스프링은 컨테이너가 특정 인프라 의존성을 필요로한다는 것을 컨테이너가 알 수있게 해주는 일련의 Aware 인터페이스를 제공한다.
 ##### 각 인터페이스는 bean에 의존성을 삽입하는 메소드를 구현하도록 요구할 것이다.
 * ApplicationContextAware
    void setApplicationContext(ApplicationContext applicationContext) throws BeansException;
 > 실행되는 ApplicationContext 객체를 받기를 원할때 구현하는 인터페이스.

 * ApplicationEventPublisherAware
    void setApplicationEventPublisher(ApplicationEventPublisher applicationEventPublisher);
 > 이 객체가 실행되는 ApplicationEventPublisher를 설정합니다.

 * BeanClassLoaderAware
    void setBeanClassLoader(ClassLoader classLoader);
 > Bean 클래스 로더를 Bean 인스턴스에 제공하는 콜백.

 * BeanFactoryAware
    void setBeanFactory(BeanFactory beanFactory) throws BeansException;
 > Bean 인스턴스에 소유하고있는 팩토리를 제공하는 콜백.

 * BeanNameAware
    void setBeanName(String name);
 > ApplicationContext가 org.springframework.beans.factory.BeanNameAware 인터페이스를 구현하는 클래스를 생성하면 클래스는 연관된 객체 정의에 정의 된 이름에 대한 참조를 제공 받는다.

 * LoadTimeWeaverAware
    void setLoadTimeWeaver(LoadTimeWeaver loadTimeWeaver);
 > 이 객체의 ApplicationContext를 포함하는 LoadTimeWeaver를 설정한다.

 * MessageSourceAware
    void setMessageSource(MessageSource messageSource);
 > 이 객체가 실행되는 MessageSource를 설정한다.

 * NotificationPublisherAware
    void setNotificationPublisher(NotificationPublisher notificationPublisher);
 > 현재 관리되는 리소스 인스턴스에 대한 NotificationPublisher 인스턴스를 설정한다.

 * ResourceLoaderAware
    void setResourceLoader(ResourceLoader resourceLoader);
 > 이 객체가 실행되는 ResourceLoader를 설정한다.

 * ServletConfigAware
    void setServletConfig(ServletConfig servletConfig);
 > 이 객체가 실행되는 ServletConfig를 설정한다.

 * ServletContextAware
    void setServletContext(ServletContext servletContext);
 > 이 객체가 실행되는 ServletContext를 설정한다.
https://howtodoinjava.com/spring/spring-core/spring-bean-life-cycle/

 ## bean 설정 파일의 커스텀 init ()과 destroy () 메소드
 ##### Bean 설정 파일의 기본 init 및 destroy 메소드는 다음 두 가지 방법으로 정의 할 수 있다.
 * 단일 빈에 적용 가능한 빈 로컬 정의
 * bean 컨텍스트에 정의 된 모든 bean에 적용되는 전역 정의
 ~~~xml
<beans>
    <bean id="demoBean" class="com.howtodoinjava.task.DemoBean" init-method="customInit" destroy-method="customDestroy"></bean>
</beans>
 ~~~
 > 여기서 전역 적 정의는 다음과 같이 주어진다.
 > 이 메소드는 <beans> 태그에서 주어진 모든 bean 정의에 대해 호출된다.
 > 모든 bean에 대해 init () 및 destroy ()와 같은 공통 메소드 이름을 일관 적으로 정의하는 패턴이있을 때 유용하다.
 > 이 기능은 모든 bean에 대한 init 및 destroy 메소드 이름을 독립적으로 언급하지 않도록 도와준다.
~~~xml
<beans default-init-method="customInit" default-destroy-method="customDestroy">      
        <bean id="demoBean" class="com.howtodoinjava.task.DemoBean"></bean>
</beans>
~~~
 > 이 유형의 라이프 사이클에 대한 샘플 구현은 다음과 같다.
~~~java
public class BemoBeanTypeThree
{
    public void customInit()
    {
        System.out.println("Method customInit() invoked...");
    }

    public void customDestroy()
    {
        System.out.println("Method customDestroy() invoked...");
    }
}
~~~
 ## @PostConstruct 및 @PreDestroy annotation
 ##### Spring 2.5 이상에서는 @PostConstruct 및 @PreDestroy annotation을 사용하여
 ##### lifeCycle 메소드를 지정하는데도 annotation을 사용할 수 있다.

 * @PostConstruct 어노테이션 된 메소드는 기본 생성자를 사용하여 Bean을 구성한 후 인스턴스를 요청한 오브젝트로 리턴하기 직전에 호출된다.  
 * @PreDestroy annotation이 달린 메소드는 bean 컨테이너 내부에서 bean이 파기되기 바로 전에 호출된다.
 > 샘플 구현은 다음과 같습니다.
~~~java
import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;

public class BemoBeanTypeFour
{
    @PostConstruct
    public void customInit()
    {
        System.out.println("Method customInit() invoked...");
    }

    @PreDestroy
    public void customDestroy()
    {
        System.out.println("Method customDestroy() invoked...");
    }
}
~~~
