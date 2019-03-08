---
layout: spring-framework-post
title: "ContextLoaderListener"
date: 2018-01-08
excerpt: ""
tags: [springframework]
spring-framework: true
comments: true
---

# ContextLoaderListener
> org.springframework.web.context.ContextLoaderListener
> jar - spring-web-5.0.2.RELEASE.jar (최신버전)

> 루트 애플리케이션 컨텍스트의 라이프 사이클 관리
> 부트 스트랩 리스너는 Spring의 root{@link WebApplicationContext}를 시작하고 종료합니다.
> 단순히 {@link ContextLoader} 에 위임 할뿐 아니라 {@link ContextCleanupListener}에 위임하기만 하면됩니다.
> Spring 3.1 이후, ContextLoaderListener는 Servlet 3.0+ 환경에서 프로그래밍 방식으로 구성 할 수 있도록
> {@link #ContextLoaderListener (WebApplicationContext)} 생성자를 통해 루트 웹 응용 프로그램 컨텍스트를 주입하는 것을 지원합니다.

> 「contextClass」및 「contextConfigLocation」서블릿 context-params에 근거 해 Web 어플리케이션 문맥을 작성하는 새로운 ContextLoaderListener를 작성합니다.
> 각각의 기본값에 대한 자세한 내용은 {@link ContextLoader} 수퍼 클래스 설명서를 참조하십시오.

> 이 생성자는 일반적으로 인수가없는 생성자가 필요한 {@code web.xml} 내의 {@code <listener>}로 ContextLoaderListener를 선언 할 때 사용됩니다.
> 생성 된 애플리케이션 컨텍스트는 속성 이름 {@link WebApplicationContext # ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE} 아래에 ServletContext에 등록되며
> Spring 애플리케이션 컨텍스트는 이 listener에서 {@link #contextDestroyed} 라이프 사이클 메소드가 호출 될 때 닫힙니다.
~~~java
 public ContextLoaderListener() {
}
~~~

# WebApplicationContext
> 웹 응용 프로그램의 구성을 제공하는 인터페이스.
> 이것은 어플리케이션의 실행 중에는 읽기 전용입니다만, implemenetation이 이것을 지원하고있는 경우는 리로드 될 가능성이 있습니다.
> 이 인터페이스는 범용의 ApplicationContext 인터페이스에 getServletContext () 메소드를 추가해, 부트 스트랩 처리로 루트 문맥을 바인드 할 필요가있는, 잘 알려진 어플리케이션 속성 명을 정의합니다.

> 일반 응용 프로그램 컨텍스트와 마찬가지로 웹 응용 프로그램 컨텍스트는 계층 적입니다.
> 애플리케이션 당 하나의 루트 컨텍스트가 있고, 애플리케이션의 각 서블릿 (MVC 프레임 워크의 디스패처 서블릿 포함)에는 자체 하위 컨텍스트가 있습니다.
> 표준 애플 리케이션 컨텍스트 라이프 사이클 기능 외에도 WebApplicationContext 구현은 ServletContextAware} Bean을 감지하고 이에 따라 {@code setServletContext} 메소드를 호출해야합니다.


# ContextLoader
> 루트 응용 프로그램 컨텍스트에 대한 실제 초기화 작업을 수행합니다.
> ContextLoaderListener에 의해 불려갑니다.

> 문맥 클래스 형을 지정하기 위해서, {@code web.xml} context-param 레벨의 {@link #CONTEXT_CLASS_PARAM "contextClass"}파라미터를 검색해,
> {@link org.springframework.web.context.support.XmlWebApplicationContext} 찾을 수없는 경우.
> 디폴트의 ​​ContextLoader 구현에서는, 지정된 컨텍스트 클래스가 {@link ConfigurableWebApplicationContext} 인터페이스를 구현할 필요가 있습니다.

> #CONFIG_LOCATION_PARAM "contextConfigLocation"context-param를 처리해, 그 값을 문맥 인스턴스에 건네 주어,  
> 복수의 파일 패스에 구문 분석 해, 임의의 수의 쉼표 및 공백으로 나눌 수 있습니다.
> "WEB-INF / applicationContext1.xml, WEB-INF / applicationContext2.xml".

> Ant-style 경로 패턴도 지원됩니다
> "WEB-INF / * Context.xml, WEB-INF / spring * .xml"
> 또는 "WEB-INF / & # 42; & # 42; / * Context.xml".

> 참고 : 여러 설정 위치의 경우, 이후의 bean 정의는 적어도 이전에로드 된 파일에 정의 된 것보다 우선합니다.
> Spring의 기본 ApplicationContext 구현.

> 이것은 추가 XML 파일을 통해 의도적으로 특정 bean 정의를 오버라이드 (override)하기 위해 활용 될 수 있습니다.

> 루트 어플리케이션 컨텍스트를 로드하는 것 이상으로 이 클래스는 선택적으로 공유 부모 컨텍스트를로드하거나 가져 와서 루트 응용 프로그램 컨텍스트에 연결합니다.
> 자세한 것은, {@link #loadParentContext(ServletContext)}  메소드를 참조하십시오.

> Spring 3.1 이후로 ContextLoader는 Servlet 3.0+ 환경에서 프로그래밍 방식으로 구성 할 수 있도록
> {@link #ContextLoader (WebApplicationContext)} 생성자를 통해 루트 웹 응용 프로그램 컨텍스트를 주입하는 것을 지원합니다.

> 사용 예는 {@link org.springframework.web.WebApplicationInitializer}를 참조하십시오.

> 기본 BeanFactory의 직렬화 ID로 사용되는 루트 WebApplicationContext ID의 구성 매개 변수 : {@value}
~~~java
public static final String CONTEXT_ID_PARAM = "contextId";
~~~

> 루트 컨텍스트의 구성 위치를 지정할 수있는 서블릿 컨텍스트 매개 변수의 이름 (예 : {@value}). 그렇지 않은 경우 구현의 기본값으로 되돌아갑니다.
> @see org.springframework.web.context.support.XmlWebApplicationContext#DEFAULT_CONFIG_LOCATION
~~~java
public static final String CONFIG_LOCATION_PARAM = "contextConfigLocation";
~~~

> 사용할 루트 WebApplicationContext 구현 클래스의 구성 매개 변수 : {@value}
> @see #determineContextClass(ServletContext)
~~~java
public static final String CONTEXT_CLASS_PARAM = "contextClass";
~~~
> 루트 웹 어플리케이션 컨텍스트를 초기화하는데 사용하는 {@link ApplicationContextInitializer} 클래스의 구성 매개 변수 : {@value}
> @see #customizeContext(ServletContext, ConfigurableWebApplicationContext)
~~~java
public static final String CONTEXT_INITIALIZER_CLASSES_PARAM = "contextInitializerClasses";
~~~

.
.
.
참고 : https://github.com/spring-projects/spring-framework/blob/master/spring-web/src/main/java/org/springframework/web/context/ContextLoader.java
