---
layout: spring-framework-post
title: "WebApplicationInitializer"
date: 2018-01-08
excerpt: ""
tags: [springframework]
spring-framework: true
comments: true
---



https://github.com/spring-projects/spring-framework/blob/master/spring-web/src/main/java/org/springframework/web/WebApplicationInitializer.java
#### org.springframework.web.WebApplicationInitializer.java
> 기존의 `web.xml` 기반 접근 방식과 반대로 (또는 병행 가능) 프로그래밍 방식으로  
> '{@ link ServletContext}'를 구성하기 위해 Servlet 3.0+ 환경에서 구현할 인터페이스

> 이 SPI의 구현은, SpringServletContainerInitializer에 의해 자동적으로 검출됩니다.
> 이 자체는, Servlet 3.0 컨테이너에 의해 자동적으로 부트 스트랩됩니다.

> 이 부트 스트래핑 메커니즘에 대한 자세한 내용은 {@linkplain SpringServletContainerInitializer의 Javadoc}을 참조하십시오.

## example
### 전통적인 XML 기반 접근 방식
> 웹 애플리케이션을 구축하는 대부분의 Spring 사용자는 Spring의 `DispatcherServlet`을 등록해야합니다.
> 참고로, WEB-INF / web.xml에서 이것은 일반적으로 다음과 같이 수행됩니다.

~~~xml
{@code
<servlet>
 <servlet-name>dispatcher</servlet-name>
 <servlet-class>
	 org.springframework.web.servlet.DispatcherServlet
 </servlet-class>
 <init-param>
	 <param-name>contextConfigLocation</param-name>
	 <param-value>/WEB-INF/spring/dispatcher-config.xml</param-value>
 </init-param>
 <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
 <servlet-name>dispatcher</servlet-name>
 <url-pattern>/</url-pattern>
</servlet-mapping>}
~~~
### WebApplicationInitializer를 사용하는 코드 기반 접근 방식
> 여기에 해당하는 {@code DispatcherServlet} 등록 로직이 있습니다.
~~~java
{@code WebApplicationInitializer}-style:
public class MyWebAppInitializer implements WebApplicationInitializer {
	&#064;Override
	public void onStartup(ServletContext container) {
		XmlWebApplicationContext appContext = new XmlWebApplicationContext();
		appContext.setConfigLocation("/WEB-INF/spring/dispatcher-config.xml");
		ServletRegistration.Dynamic dispatcher =
			container.addServlet("dispatcher", new DispatcherServlet(appContext));
		dispatcher.setLoadOnStartup(1);
		dispatcher.addMapping("/");
	}
}
~~~
> 상기의 것의 대안으로서, 다음으로부터 확장 할 수도 있습니다.
> org.springframework.web.servlet.support.AbstractDispatcherServletInitializer
> 여기서 볼 수 있듯이, Servlet 3.0의 새 ServletContext # addServlet 메소드 덕분에
> 우리는 DispatcherServlet의  **인스턴스**를 실제로 등록하고 있으며
> 이는 `DispatcherServlet`은 이제 다른 객체와 같이 취급됩니다.
> 이 경우 응용 프로그램 컨텍스트의 생성자 주입을 수신합니다.

> 이 스타일은 더 간단하고 간결합니다.
> init-params 등을 다루는 데는 일반적인 JavaBean 스타일의 프로퍼티와 생성자 인자 만 신경 쓸 필요가 없습니다.
> `DispatcherServlet`에 삽입하기 전에 필요에 따라 Spring 애플리케이션 컨텍스트를 만들고 사용할 수 있습니다.

> 대부분의 주요 스프링 웹 구성 요소가 이 스타일의 등록을 지원하도록 업데이트되었습니다.
`DispatcherServlet`,`FrameworkServlet`,`ContextLoaderListener` 및 `DelegatingFilterProxy` 모두 이제 생성자 인수를 지원합니다.
> 구성 요소 (예 : non-Spring, third party)가 WebApplicationInitializers 내에서 사용하도록 특별히 업데이트되지 않은 경우에도 여전히 사용할 수 있습니다.
> Servlet 3.0 `{@code ServletContext}` API를 사용하면 init-params, context-params 등을 프로그래밍 방식으로 설정할 수 있습니다.

##  구성에 대한 100 % 코드 기반 접근 방식

> 위의 예에서는 {@code WEB-INF / web.xml}이 {@code WebApplicationInitializer} 형식의 코드로 바뀌었지만
> 실제{@code dispatcher-config.xml} 스프링 구성은 XML 기반으로 유지되었습니다.
> {@code WebApplicationInitializer}는 Spring의 코드 기반으로 사용하기에 적합합니다.
> Javadoc에 대한 자세한 설명은 있지만, 다음 예제는 Spring의 리팩토링을 사용하는 방법을 보여줍니다.
> {@code XmlWebApplicationContext} 대신
> {@link org.springframework.web.context.support.AnnotationConfigWebApplicationContext AnnotationConfigWebApplicationContext} 을 사용하고
> 사용자 정의 {@code @Configuration} 클래스 {@code AppConfig} 및 Spring XML 파일 대신 DispatcherConfig를 사용하십시오.
> 이 예제는 위의 내용을 넘어서서 '루트'응용 프로그램의 일반적인 구성을 보여줍니다.
> 컨텍스트 및 ContextLoaderListener의 등록 :

~~~java
public class MyWebAppInitializer implements WebApplicationInitializer {
	@Override
	public void onStartup(ServletContext container) {
		// Create the 'root' Spring application context
		AnnotationConfigWebApplicationContext rootContext =
			new AnnotationConfigWebApplicationContext();
		rootContext.register(AppConfig.class);
		// Manage the lifecycle of the root application context
		container.addListener(new ContextLoaderListener(rootContext));
		// Create the dispatcher servlet's Spring application context
		AnnotationConfigWebApplicationContext dispatcherContext =
			new AnnotationConfigWebApplicationContext();
		dispatcherContext.register(DispatcherConfig.class);
		// Register and map the dispatcher servlet
		ServletRegistration.Dynamic dispatcher =
			container.addServlet("dispatcher", new DispatcherServlet(dispatcherContext));
		dispatcher.setLoadOnStartup(1);
		dispatcher.addMapping("/");
	}
}
~~~
> 상기의 것의 대안으로서, 다음으로부터 확장 할 수도 있습니다.
> {@link org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer}.
> {@code WebApplicationInitializer} 구현은 **자동으로 감지**되므로 응용 프로그램 내에서 자유롭게 구현할 수 있습니다.
> 아래를 보십시오.

## WebApplicationInitializer 실행 순서 지정
> {@code WebApplicationInitializer} 구현물은 선택적으로 클래스 수준에서 Spring의 @ {@ link org.springframework.core.annotation.Order Order} annotation을 달거나
> Spring의 {@link org.springframework.core.Ordered Ordered} 인터페이스를 구현할 수 있습니다.
> 그러면 이니셜 라이저는 호출 전에 주문됩니다.
> 이것은 사용자가 서블릿 컨테이너 초기화가 일어나는 순서를 보장 할 수있는 메커니즘을 제공합니다.
> 이 기능의 사용은 드물 것으로 예상됩니다.
> 일반적인 어플리케이션은 모든 컨테이너 초기화를 단일 WebApplicationInitializer 내에서 중앙 집중화 할 가능성이 높기 때문입니다.

## 경고

### web.xml 버전 관리
 > {@code WEB-INF / web.xml} 및 {@code WebApplicationInitializer} 사용은 상호 배타적이지 않습니다.
 > 예를 들어 web.xml은 하나의 서블릿을 등록 할 수 있고 WebApplicationInitializer는 다른 하나를 등록 할 수 있습니다.
 > 이니셜 라이저는 ServletContext # getServletRegistration (String)와 같은 메소드를 통해 web.xml에서 수행 된 등록을 수정할 수도 있습니다.
 > 그러나 WEB-INF / web.xml이 응용 프로그램에있는 경우, {@code version} 속성을 "3.0"이상으로 설정해야합니다.
 > 그렇지 않으면 {@code ServletContainerInitializer} 부트 스트랩이 서블릿 컨테이너에서 무시됩니다.

### Tomcat "/" 매핑
> Apache Tomcat은 내부의 {@code DefaultServlet}을 "/"로 매핑하고 Tomcat 버전 7.0.14 이하에서는
> 이 서블릿 매핑을 프로그래밍 방식으로 재정의 할 수 없습니다.
> 7.0.15가 문제를 해결했습니다. "/"서블릿 매핑을 오버라이드하여 테스트를 마쳤습니다.
> GlassFish 3.1에서 성공적으로 수행되었습니다.

~~~java
public interface WebApplicationInitializer {
> @param - {@code ServletContext}를 초기화하는 servletContext
> ServletException - 지정된 ServletContext에 대한 호출이 존재하지 않는 경우,
> ServletException을 throw합니다.
void onStartup(ServletContext servletContext) throws ServletException;
}
~~~



# BeanFactory.interface
> org.springframework.beans.factory.BeanFactory.java
> spring-beans-5.0.2.RELEASE.jar

> 지정된 bean의 인스턴스를 돌려줍니다.
> 이 메소드는 Spring BeanFactory가 Singleton 또는 Prototype 디자인 패턴을 대신하여 사용될 수있게한다.
> 호출자는 Singleton Bean의 경우 반환 된 객체에 대한 참조를 유지할 수 있습니다.
> 별명을 해당 정식 bean 이름으로 다시 변환합니다.
> 이 factory 인스턴스에서 bean을 찾을 수없는 경우 부모 팩토리에 요청합니다.
> @param name 검색하는 bean의 이름
> @return Bean의 인스턴스를 돌려줍니다
> @throws NoSuchBeanDefinitionException - 지정된 이름의 Bean 정의가 존재하지 않는 경우
> @throws BeansException - Bean를 취득 할 수 없었던 경우
~~~
Object getBean(String name) throws BeansException;
~~~
