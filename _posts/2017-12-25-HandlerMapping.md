---
layout: spring-framework-post
title: "HandlerMapping"
date: 2017-12-25
excerpt: "RequestMappingHandlerMapping"
tags: [springframework,handlermapping]
spring-framework: true
comments: true
---


**Default is `BeanNameUrlHandlerMapping` and `RequestMappingHandlerMapping`.**  

# RequestMappingHandlerMapping
> 경로 `org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping`

`dispatcherServlet.xml`
~~~xml
<context:component-scan base-package="com.spring.java"/>

<bean id="requestMappingHandler" class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping">
	<property name="Interceptors">
		<list>
			<ref bean="handlerInterceptor"/>
			<ref bean="webReqInterceptor"/>
			<ref bean="handleAdaptor"/>
		</list>
	</property>
</bean>
<bean id="handlerInterceptor" class="com.spring.config.interceptor.HandlerInterCeptorImpl"/>
<bean id="webReqInterceptor" class="com.spring.config.interceptor.WebRequestInterceptorImpl"/>
<bean id="handleAdaptor" class="com.spring.config.interceptor.HandleAdaptor"></bean>
~~~



### Interceptor interface
* HandlerInterceptor
* WebRequestInterceptor

##### HandlerInterceptor
> 경로 `org.springframework.web.servlet.HandlerInterceptor`



##### WebRequestInterceptor
> 경로 `org.springframework.web.context.request.WebRequestInterceptor`



##### Interface대신 Adapter 사용
> 구현체가 아닌 **인터페이스를 프로그래밍**하는 것은 **좋은 습관**이다.   
> Spring Framework는 이러한 인터페이스를 상당히 많이 제공한다.   
> **HandlerInterceptor**는 그 중 하나이다.  
> **하지만** 이러한 인터페이스 중 일부는 다른 인터페이스보다 **풍부하다.**  
> 따라서 클라이언트로서 사용자 정의 구현을 제공하고   
> 메소드 중 일부만을 관리하려는 경우 **실제 구현하는 부분**과   
> override 했지만 실제 **구현되지 않은 메소드**들이 존재하게 된다.  

> 이 **문제를 해결**하기 위해 Spring Framework는 일반적으로   
> **HandlerInterceptorAdaptor** for HandlerInterceptor 인터페이스나  
> **WebMvcConfigurerAdapter** for WebMvcConfigurer와 같은 인터페이스에 해당하는 **추상 어댑터를 제공**한다.  

> 이것은 Spring Framework에서 **반복되는 주제**이며, 일반적인 예는 아래와 같다.  
* **WebMvcConfigurer and WebMvcConfigurerAdapter**
* **CachingConfigurer and CachingConfigurerSupport**


### postHandle
> HandlerMapping이 호출 된 후 적절한 핸들러 객체가 결정되었지만  
> HandlerAdapter가 핸들러를 호출하기 전에 호출됩니다.  

> 이 메소드는 핸들러 메소드에 전달되기 전에 요청을 가로 채기 위해 사용됩니다.   
> 이 메소드는 'true'를 반환해야 Spring이 다른 Spring 인터셉터를 통해 요청을 처리하도록 알리거나   
> 더 이상의 Spring 인터셉터가없는 경우 핸들러 메소드로 요청을 보내도록해야합니다.  
> 이 메소드가 'false'를 반환하면 Spring 프레임 워크는 요청이 스프링 인터셉터 자체에 의해  
> 처리되었다고 가정하고 더 이상 처리 할 필요가 없다고 가정합니다.   
> 이 경우 응답 객체를 사용하여 클라이언트 요청에 대한 응답을 보내야합니다.  

### postHandle
> HandlerAdapter가 실제로 핸들러를 호출 한 후에 호출되었지만  
> DispatcherServlet이 뷰를 렌더링하기 전에 호출됩니다.  
> 지정된 ModelAndView를 통해 추가 모델 객체를 뷰에 노출 할 수 있습니다.  

> 이 HandlerInterceptor 인터셉터 메서드는 HandlerAdapter가 핸들러를 호출했지만    
> DispatcherServlet이 아직 뷰를 렌더링하지 않을 때 호출됩니다.   
> 이 메소드는 뷰 페이지에서 사용될 ModelAndView 객체에 추가 속성을 추가하는 데 사용할 수 있습니다.   
> 이 스프링 인터셉터 메서드를 사용하여 처리기 메서드가 클라이언트 요청을 처리하는 데 걸리는 시간을 결정할 수 있습니다.  

### afterCompletion
> 요청 처리 완료 후, 즉 뷰를 렌더링 한 후 콜백합니다.  
> 핸들러 실행 결과에 대해 호출되므로 적절한 리소스 정리가 가능합니다.  
> 참고 :이 인터셉터의 preHandle 메서드가 성공적으로 완료되고 true를 반환 한 경우에만 호출됩니다!  

> 이것은 핸들러가 실행되고 뷰가 렌더링되면 호출되는 HandlerInterceptor 콜백 메소드입니다.  
