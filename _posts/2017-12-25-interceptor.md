---
layout: post
title: "Hoisting"
date: 2019-03-06
excerpt: "hoisting은 모든 선언을 scope의 상단으로 끌어 올립니다."
tags: [javascript]
comments: true
---

# interceptor



## 순서
 1. **xml 설정**
 2. **InterCeptor interface 구현**
 

#### 1. xml 설정
 > HandlerMapping 클래스 Bean  참조  
 > HandlerMapping클래스 Bean의 property에 **interceptors** 이름으로 interceptor를 구현한 클래스를 참조 한다.  
 > InterCeptor Bean 객체의 class 속성값은 InterCeptor를 구현한 클래스의 경로부터 이름까지 적어준다.  
 
##### InterCeptor를 여러개 등록 `<list>`를 사용해서 등록
 ~~~xml
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping">
	<property name="interceptors">
		<list>
			<ref bean="handlerInterCeptor"/>
		</list>		
	</property>
</bean>
 
 
<bean id="webRequestInterCeptor" class="spring.learning.config.java.interCeptors.WebRequest"/>
<bean id="handlerInterCeptor" class="spring.learning.config.java.interCeptors.Handler"/>
~~~


##### InterCeptor를 하나만 등록
~~~xml
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping">
	<property name="interceptors" ref="webRequestInterCeptor"/>
</bean>
 
<bean id="webRequestInterCeptor" class="spring.learning.config.java.interCeptors.WebRequest"/>
~~~
  
#### 2. InterCeptor interface 구현
~~~java
public class WebRequest implements WebRequestInterceptor {

	@Override
	public void preHandle(org.springframework.web.context.request.WebRequest request) throws Exception {
		System.out.println("[WebRequestInterceptor]");
	}

	@Override
	public void postHandle(org.springframework.web.context.request.WebRequest request, ModelMap model)
			throws Exception {
		System.out.println("[WebRequestInterceptor]");
	}

	@Override
	public void afterCompletion(org.springframework.web.context.request.WebRequest request, Exception ex)
			throws Exception {
		System.out.println("[WebRequestInterceptor]");
	}

}
~~~

### Interface **HandlerInterceptor**
`preHandle`
> 핸들러의 실행을 차단하십시오.   
> HandlerAdapter가 실제로 핸들러를 호출 한 후에 호출되었지만  
> DispatcherServlet이 뷰를 렌더링하기 전에 호출됩니다.  
> 이 방법을 사용하면 각 인터셉터는 실행 체인을 중단하고  
> 일반적으로 HTTP 오류를 보내거나 사용자 지정 응답을 작성하도록 결정할 수 있습니다.  

`postHandle`
> 핸들러의 실행을 차단하십시오.  
> HandlerAdapter가 실제로 핸들러를 호출 한 후에 호출되었지만  
> DispatcherServlet이 뷰를 렌더링하기 전에 호출됩니다.  
> 지정된 ModelAndView를 통해 추가 모델 객체를 뷰에 노출 할 수 있습니다.  
> 이 방법을 사용하면 각 인터셉터는 실행 체인을 역순으로 적용하여 실행을 사후 처리 할 수 있습니다.  

`afterCompletion`
> 요청 처리 완료 후, 즉 뷰 렌더링 후 콜백.  
> 핸들러 실행 결과에 대해 호출되므로 적절한 리소스 정리가 가능합니다.  
> 참고 :이 인터셉터의 preHandle 메서드가 성공적으로 완료되고 true를 반환 한 경우에만 호출됩니다!  


> InterCeptor의 종류는 다양하다  
> 어떤 InterCeptor를 구현할지는 개발자의 몫인듯  
> WebRequestInterceptor, HandlerInterceptor 등등..많음..  
> 그리고 InterCeptor를 구현할 클래스의 위치는 개발자 마음임.  
> xml에 Bean객체를 등록할 때 class속성에 InterCeptor를 구현한 클래스의 경로와 클래스명만 명시해 주면됨.  


---
### 이하 JAVA API
### `AbstractHandlerMapping` 클래스
* `setInterceptors`
> 지원되는 인터셉터 유형은  
> `HandlerInterceptor`, `WebRequestInterceptor` 및 `MappedInterceptor`입니다.  
> 매핑 된 인터셉터는 경로 패턴과 일치하는 요청 URL에만 적용됩니다.  
> 매핑 된 인터셉터 빈은 초기화 중 유형(type)별로도 감지됩니다.  

- HandlerInterceptor  
- WebRequestInterceptor  
- MappedInterceptor  


