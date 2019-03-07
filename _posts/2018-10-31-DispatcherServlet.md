---
layout: post
title: "DispatcherServlet"
date: 2017-12-25
excerpt: "스프링 중앙 서블릿"
tags: [springframework, Multipart, Resolver]
spring-framework: true
comments: true
---

# DispatcherServlet.md
[Spring WebFlux와 동일](https://docs.spring.io/spring/docs/5.0.4.BUILD-SNAPSHOT/spring-framework-reference/web-reactive.html#webflux-dispatcher-handler)

많은 다른 웹 프레임 워크와 마찬가지로 Spring MVC는 프런트 컨트롤러 패턴을 중심으로 설계되었으며,
DispatcherServlet 인 중앙 서블릿은 요청 처리를 위한 공유 알고리즘을 제공하고 실제 작업은 구성 가능한 위임 구성 요소로 수행합니다.  
이 모델은 유연하고 다양한 워크 플로를 지원합니다.  


DispatcherServlet은 모든 서블릿과 마찬가지로 Java 구성 또는 web.xml을 사용하여 서블릿 사양에 따라 선언하고 매핑해야합니다.  


DispatcherServlet은 Spring 구성을 사용하여
[요청 매핑(request mapping), 뷰 분석(view resolution), 예외 처리(exception handling) 등](https://docs.spring.io/spring/docs/5.0.4.BUILD-SNAPSHOT/spring-framework-reference/web.html#mvc-servlet-special-bean-types) 에 필요한 위임 구성 요소를 검색합니다.  


다음은 DispatcherServlet을 등록하고 초기화하는 Java 구성의 예입니다.  
이 클래스는 Servlet 컨테이너에 의해 자동 감지됩니다 ([Servlet Config 참조](https://docs.spring.io/spring/docs/5.0.4.BUILD-SNAPSHOT/spring-framework-reference/web.html#mvc-container-config)).  

~~~java
public class MyWebApplicationInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext servletCxt) {

        // Load Spring web application configuration
        AnnotationConfigWebApplicationContext ac = new AnnotationConfigWebApplicationContext();
        ac.register(AppConfig.class);
        ac.refresh();

        // Create and register the DispatcherServlet
        DispatcherServlet servlet = new DispatcherServlet(ac);
        ServletRegistration.Dynamic registration = servletCxt.addServlet("app", servlet);
        registration.setLoadOnStartup(1);
        registration.addMapping("/app/*");
    }
}
~~~
---
> ServletContext API를 직접 사용하는 것 외에도
AbstractAnnotationConfigDispatcherServletInitializer를 확장하고 특정 메서드를 재정의 할 수 있습니다.
 ([컨텍스트 계층 구조](https://docs.spring.io/spring/docs/5.0.4.BUILD-SNAPSHOT/spring-framework-reference/web.html#mvc-servlet-context-hierarchy)의 예제 참조).  

---

다음은 DispatcherServlet을 등록하고 초기화하는 web.xml 구성 예제입니다.  
~~~xml
<web-app>

    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/app-context.xml</param-value>
    </context-param>

    <servlet>
        <servlet-name>app</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value></param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>app</servlet-name>
        <url-pattern>/app/*</url-pattern>
    </servlet-mapping>

</web-app>
~~~

---
> 스프링 부트는 다른 초기화 순서를 따릅니다.  
> SpringBoot는 Servlet 컨테이너의 라이프 사이클에 연결하는 대신  Spring 구성을 사용하여 자체 및 내장 된 Servlet 컨테이너를 부트 스트랩합니다.  
> 필터 및 서블릿 선언은 Spring 구성에서 감지되어 서블릿 컨테이너에 등록됩니다.  
> 자세한 내용은 [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-embedded-container) 문서를 확인하십시오.  

---

## 1.2.1. Context Hierarchy
DispatcherServlet은 자신의 구성을 위해 일반 ApplicationContext의 확장 인 WebApplicationContext를 기대합니다.  
WebApplicationContext는 연관되어있는 ServletContext와 Servlet에 대한 링크를 가지고 있습니다.  
또한 ServletContext에 바인딩되어 있으므로 응용 프로그램에서 RequestContextUtils의 정적 메서드를 사용하여
WebApplicationContext에 액세스해야하는 경우이를 조회 할 수 있습니다.  

* 단일 WebApplicationContext를 갖는 많은 응용 프로그램은 간단하고 충분(sufficient)합니다.  
* 하나의 루트 WebApplicationContext가 여러 DispatcherServlet (또는 다른 Servlet) 인스턴스에서 공유되고
각각 자체 Child WebApplicationContext 구성을 갖는 컨텍스트 계층 구조를 가질 수도 있습니다.  
컨텍스트 계층 구조 기능에 대한 자세한 내용은 [ApplicationContext의 추가 기능](https://docs.spring.io/spring/docs/5.0.4.BUILD-SNAPSHOT/spring-framework-reference/core.html#context-introduction)을 참조하십시오.  

루트 WebApplicationContext는  
일반적으로 여러 개의 Servlet 인스턴스에서 공유해야 하는 데이터 저장소 및 비즈니스 서비스와 같은 인프라 bean을 포함합니다.  
이러한 bean은 효과적으로 상속되며 서블릿 특정 클래스에서 재정의 (즉, 다시 선언) 될 수 있습니다.  
자식 WebApplicationContext는 일반적으로 주어진 지역의 빈을 포함합니다.  
{% capture images %}
 https://github.com/ixtears23/docs/blob/master/mvc-context-hierarchy.png?raw=true
{% endcapture %}

ㅅㄷㄴㅅ

<figure>
	<a href="https://github.com/ixtears23/docs/blob/master/mvc-context-hierarchy.png"><img src="https://github.com/ixtears23/docs/blob/master/mvc-context-hierarchy.png"></a>
</figure>

다음은 WebApplicationContext 계층을 사용한 구성 예제입니다.
~~~java
public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class<?[] { RootConfig.class };
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class<?[] { App1Config.class };
    }

    @Override
    protected String[] getServletMappings() {
        return new String[] { "/app1/*" };
    }
}
~~~

---

> 응용 프로그램 컨텍스트 계층 구조가 필요하지 않은 경우 응용 프로그램은 getRervConfigClasses ()를 통해 모든 구성을 반환하고
getServletConfigClasses ()에서 null을 반환 할 수 있습니다.  

---

위의 java config와 동일기능을 하는 web.xml
~~~xml
<web-app>

    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/root-context.xml</param-value>
    </context-param>

    <servlet>
        <servlet-name>app1</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>/WEB-INF/app1-context.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>app1</servlet-name>
        <url-pattern>/app1/`*</url-pattern>
    </servlet-mapping>

</web-app>
~~~

## 1.2.2. Special Bean Types
[Same in Spring WebFlux](https://docs.spring.io/spring/docs/5.0.4.BUILD-SNAPSHOT/spring-framework-reference/web-reactive.html#webflux-special-bean-types)  
DispatcherServlet은 요청을 처리하고 적절한 응답을 렌더링하기 위해 특수 빈에 위임합니다.  
"special-bean"은 Spring이 관리하는 WebFlux 프레임 워크 계약을 구현하는 객체 인스턴스를 의미합니다.  
그것들은 일반적으로 내장 된 계약이 있지만 그 속성을 사용자 정의하거나 확장 한 다음 대체 할 수 있습니다.  

**1. HandlerMapping**  
사전 처리 및 사후 처리를위한 [인터셉터](https://docs.spring.io/spring/docs/5.0.4.BUILD-SNAPSHOT/spring-framework-reference/web.html#mvc-handlermapping-interceptor) 목록과 함께 요청을 처리기에 매핑합니다.  
매핑은 HandlerMapping 구현에 따라 세부 사항이 달라지는 일부 기준을 기반으로합니다.  
두 가지 주요 HandlerMapping 구현은 @RequestMapping 주석이있는 메소드를 지원하는 RequestMappingHandlerMapping과
URI 경로 패턴의 핸들러를 명시 적으로 등록한 SimpleUrlHandlerMapping입니다.  

**2. HandlerAdapter**  
DispatcherServlet이 핸들러가 실제로 호출되는 방법에 관계없이 요청에 매핑 된 핸들러를 호출 할 수있게 도와줍니다.  
예를 들어, anotation이 달린 컨트롤러를 호출하려면 anotation을 해석해야합니다.  
HandlerAdapter의 주요 목적은 DispatcherServlet을 그러한 세부 사항으로부터 보호하는 것입니다.  

**[3. HandlerExceptionResolver](https://docs.spring.io/spring/docs/5.0.4.BUILD-SNAPSHOT/spring-framework-reference/web.html#mvc-exceptionhandlers)**  
핸들러 또는 HTML 오류 뷰 또는 기타로 맵핑 할 수있는 예외를 해결하기위한 전략.  
[예외 해결](https://docs.spring.io/spring/docs/5.0.4.BUILD-SNAPSHOT/spring-framework-reference/web.html#mvc-exceptionhandlers)을 참조하십시오.  

**4. ViewResolver**  
Handler에서 반환 된 논리적 String 기반 뷰 이름을 실제 View로 해석하여 응답에 렌더링합니다.  
[View Resolution](https://docs.spring.io/spring/docs/5.0.4.BUILD-SNAPSHOT/spring-framework-reference/web.html#mvc-viewresolver), [View Technologies](https://docs.spring.io/spring/docs/5.0.4.BUILD-SNAPSHOT/spring-framework-reference/web.html#mvc-view) 를 참고 하십시오.  

**[5. LocaleResolver](https://docs.spring.io/spring/docs/5.0.4.BUILD-SNAPSHOT/spring-framework-reference/web.html#mvc-localeresolver), [LocaleContextResolver](https://docs.spring.io/spring/docs/5.0.4.BUILD-SNAPSHOT/spring-framework-reference/web.html#mvc-timezone)**  
국제화 된 뷰를 제공 할 수 있도록 클라이언트가 사용하는 로케일 가능한 시간대를 해결합니다.  
See [Locale](https://docs.spring.io/spring/docs/5.0.4.BUILD-SNAPSHOT/spring-framework-reference/web.html#mvc-localeresolver).  

**[6. ThemeResolver](https://docs.spring.io/spring/docs/5.0.4.BUILD-SNAPSHOT/spring-framework-reference/web.html#mvc-themeresolver)**  
예를 들어, 개인화 된 레이아웃을 제공하기 위해 Web 응용 프로그램에서 사용할 수있는 테마를 해결합니다.  
일부 멀티 파트 구문 분석 라이브러리의 도움으로 여러 부분 요청 (예 : 브라우저 폼 파일 업로드)을 구문 분석하기위한 추상화입니다.  

**[7. FlashMapManager](https://docs.spring.io/spring/docs/5.0.4.BUILD-SNAPSHOT/spring-framework-reference/web.html#mvc-flash-attributes)**  
일반적으로 리디렉션을 통해 한 요청에서 다른 요청으로 속성을 전달하는 데 사용할 수있는 "입력"및 "출력" FlashMap을 저장하고 검색합니다.  

## 1.2.3. Web MVC Config
응용 프로그램은 요청을 처리하는 데 필요한 [mvc-special-bean-types]에 나열된 인프라 bean을 선언 할 수 있습니다.  
DispatcherServlet은 각 특수 bean에 대한 WebApplicationContext를 확인합니다.  
일치하는 bean 유형이 없으면 [DispatcherServlet.properties](https://github.com/spring-projects/spring-framework/blob/master/spring-webmvc/src/main/resources/org/springframework/web/servlet/DispatcherServlet.properties)에 나열된 기본 유형으로 돌아갑니다.  

대부분의 경우 [MVC 구성](https://docs.spring.io/spring/docs/5.0.4.BUILD-SNAPSHOT/spring-framework-reference/web.html#mvc-config)이 가장 좋은 출발점입니다.  
필요한 bean을 Java 또는 XML로 선언하고 더 높은 레벨의 구성 콜백 API를 제공하여이를 사용자 정의합니다.  

---

> 스프링 부트는 MVC Java 구성을 사용하여 스프링 MVC를 구성하고 많은 추가 옵션을 제공합니다.  

---

## 1.2.4. Servlet Config
Servlet 3.0+ 환경에서는 Servlet 컨테이너를 프로그래밍 방식으로 구성할 수 있고 web.xml 파일과 함께 구성 할 수도 있습니다.  
다음은 DispatcherServlet을 등록하는 예제입니다.  
~~~java
import org.springframework.web.WebApplicationInitializer;

public class MyWebApplicationInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext container) {
        XmlWebApplicationContext appContext = new XmlWebApplicationContext();
        appContext.setConfigLocation("/WEB-INF/spring/dispatcher-config.xml");

        ServletRegistration.Dynamic registration = container.addServlet("dispatcher", new DispatcherServlet(appContext));
        registration.setLoadOnStartup(1);
        registration.addMapping("/");
    }
}
~~~
**WebApplicationInitializer**는 Spring MVC가 제공하는 인터페이스로 **자동으로 감지**되고 **Servlet 3 컨테이너를 초기화**하는 데 사용됩니다.  

**AbstractDispatcherServletInitializer**라는 WebApplicationInitializer의 추상 기본 클래스 구현은
**서블릿 매핑**과 **DispatcherServlet 구성의 위치를 지정하는 메소드를 간단히 재정의** 하여
**DispatcherServlet을 등록하는 것을 더욱 쉽게 만듭니다.**  

Java 기반 Spring 구성을 사용하는 응용 프로그램에 권장됩니다. 아래 소스참고
~~~java
public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        return null;
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class<?>[] { MyWebConfig.class };
    }

    @Override
    protected String[] getServletMappings() {
        return new String[] { "/" };
    }
}
~~~

XML 기반 Spring 구성을 사용하는 경우 AbstractDispatcherServletInitializer에서 직접 확장해야합니다.  
~~~java
public class MyWebAppInitializer extends AbstractDispatcherServletInitializer {

    @Override
    protected WebApplicationContext createRootApplicationContext() {
        return null;
    }

    @Override
    protected WebApplicationContext createServletApplicationContext() {
        XmlWebApplicationContext cxt = new XmlWebApplicationContext();
        cxt.setConfigLocation("/WEB-INF/spring/dispatcher-config.xml");
        return cxt;
    }

    @Override
    protected String[] getServletMappings() {
        return new String[] { "/" };
    }
}
~~~
**AbstractDispatcherServletInitializer**는 **Filter 인스턴스를 추가**하고
**DispatcherServlet에 자동 매핑**되도록하는 편리한 방법을 제공합니다.  
~~~java
public class MyWebAppInitializer extends AbstractDispatcherServletInitializer {

    // ...

    @Override
    protected Filter[] getServletFilters() {
        return new Filter[] {
            new HiddenHttpMethodFilter(), new CharacterEncodingFilter() };
    }
}
~~~

각 필터는 구체적인 유형을 기반으로 기본 이름으로 추가되고 자동으로 DispatcherServlet에 매핑됩니다.  
isAsyncSupported protected 메소드는 DispatcherServlet 및 이 클래스에 매핑 된
모든 필터에서 비동기 지원을 활성화하는 단일 위치를 제공합니다.  
기본적으로이 플래그는 true로 설정됩니다.  

마지막으로, DispatcherServlet 자체를 추가로 사용자 정의해야하는 경우
createDispatcherServlet 메소드를 대체 할 수 있습니다.  

## 1.2.5. Processing
**DispatcherServlet은 다음과 같이 요청을 처리합니다.**  
* WebApplicationContext는 프로세스에서 Controller 및 기타 요소가 사용할 수있는 속성으로 요청에서 검색되고 바인드됩니다.  
`DispatcherServlet.WEB_APPLICATION_CONTEXT_ATTRIBUTE` 키 아래에 기본적으로 바인딩됩니다.  
* locale resolver는 요청을 처리 할 때 사용할 로켈을 프로세스의 요소가 해결할 수 있도록하는 요청에 바인딩됩니다.  
(뷰 렌더링, 데이터 준비 등).로케일 해석이 필요하지 않으면 필요하지 않습니다.  
* 테마 해석자는 views와 같은 요소가 사용할 테마를 결정하도록 요청에 바인딩됩니다.  
테마를 사용하지 않으면 무시할 수 있습니다.  
* multipart file resolver를 지정하면 멀티 파트에 대한 요청이 검사됩니다.  
multiparts가 발견되면 요청은 프로세스의 다른 요소에 의한 추가 처리를 위해 MultipartHttpServletRequest에 래핑됩니다.  
다중 부분 처리에 대한 자세한 정보는 [Multipart resolver](https://docs.spring.io/spring/docs/5.0.4.BUILD-SNAPSHOT/spring-framework-reference/web.html#mvc-multipart)를 참조하십시오.  
* 적절한 핸들러가 검색됩니다. 핸들러가 발견되면 모델 또는 렌더링을 준비하기 위해
핸들러 (전 처리기, 포스트 프로세서 및 컨트롤러)와 연관된 실행 체인이 실행됩니다.  
또는 주석이 달린 컨트롤러의 경우 뷰를 반환하는 대신 응답을 렌더링(HandlerAdapter 내에서) 할 수 있습니다.  
* model이 반환되면 뷰가 렌더링됩니다. model이 반환되지 않으면
(아마도 보안상의 이유 때문에 요청을 가로 채는 전처리 기나 포스트 프로세서 때문일 수 있습니다.)  
요청이 이미 충족 되었기 때문에 뷰가 렌더링되지 않습니다.  

WebApplicationContext에서 선언 된 **HandlerExceptionResolver Bean**은 **요청 처리 중에 발생하는 예외를 해결**하는 데 사용됩니다.  
이러한 exception resolvers를 사용하면 로직을 정의하여 예외를 처리 할 수 있습니다.  
자세한 내용은 예외 해결([Exception Resolution](https://docs.spring.io/spring/docs/5.0.4.BUILD-SNAPSHOT/spring-framework-reference/web.html#mvc-exceptionhandlers))을 참조하십시오.  

Spring DispatcherServlet은 또한 Servlet API에 의해 지정된 `last-modification-date`의 리턴을 지원합니다.  
특정 요청에 대한 마지막 수정 날짜를 결정하는 과정은 간단합니다.  
DispatcherServlet은 적절한 핸들러 매핑을 검색해서 핸들러가 LastModified 인터페이스를 구현하는지 테스트합니다.  
그렇다면, LastModified 인터페이스의 long getLastModified (request) 메소드의 값이 클라이언트에 리턴됩니다.  

`web.xml` 파일의 Servlet 선언에 Servlet **초기화 매개 변수 (init-param 요소)를 추가**하여
개별 DispatcherServlet 인스턴스를 **사용자 정의** 할 수 있습니다.  
지원되는 매개 변수 목록은 다음 표를 참조하십시오.  

**DispatcherServlet initialization parameters(초기화 매개변수)**  

|Parameter|Explanation|
|:-----|:-----|
|contextClass|이 Servlet이 사용하는 컨텍스트를 인스턴스화하는 WebApplicationContext를 구현하는 클래스.기본적으로 XmlWebApplicationContext가 사용됩니다.|
|contextConfigLocation|문맥이 어디에 있는지를 나타내는 컨텍스트 인스턴스(contextClass로 지정된)에 전달되는 문자열.문자열은 다중 컨텍스트를 지원하기 위해 잠재적으로 여러 문자열 (쉼표를 구분 기호로 사용)로 구성됩니다.bean이 두 번 정의 된 다중 컨텍스트 위치의 경우 최신 위치가 우선합니다.|
|namespace|WebApplicationContext의 네임 스페이스입니다. 기본값은 [servlet-name] -servlet입니다.|

## 1.2.6. Interception
모든 HandlerMapping 구현은 특정 기능을 적용하려는 경우에 유용한 핸들러 인터셉터를 지원합니다.  
인터셉터는 모든 종류의 전처리 및 후 처리를 수행 할 수고 충분한 유연성을 제공하는 세 가지 메소드로
`org.springframework.web.servlet` 패키지의 `HandlerInterceptor`를 구현해야합니다.  
* preHandle(..) - 실제 핸들러가 실행되기 전에
* postHandle(..) - 핸들러가 실행 된 후
* afterCompletion(..) - 요청이 완료되면
preHandle (..) 메서드는 boolean 값을 반환합니다.  
이 방법을 사용하여 실행 체인의 처리를 중단하거나 계속할 수 있습니다.  
이 메서드가 true를 반환하면 handler 실행 체인이 계속됩니다.  
false를 반환하면 DispatcherServlet은 인터셉터 자체가 요청을 처리하고
(예를 들어 적절한 뷰를 렌더링 한) 실행 체인에서 다른 인터셉터와 실제 처리기를 계속 실행하지 않는다고 가정합니다.  
인터셉터를 구성하는 방법에 대한 예제는 MVC 구성 섹션의 [인터셉터](https://docs.spring.io/spring/docs/5.0.4.BUILD-SNAPSHOT/spring-framework-reference/web.html#mvc-config-interceptors)를 참조하십시오.  
개별 HandlerMapping 구현에 대한 설정자를 통해 직접 등록 할 수도 있습니다.  

postHandle은 @ResponseBody 및 ResponseEntity 메소드에 대해 유용하지 않으며,
응답은 HandlerAdapter 내에서와 postHandle 이전에 작성되고 커밋됩니다.  
즉, 추가 헤더를 추가하는 것과 같이 응답을 변경하기에는 너무 늦었습니다.  
이러한 시나리오의 경우 ResponseBodyAdvice를 구현하고
[Controller Advice bean](https://docs.spring.io/spring/docs/5.0.4.BUILD-SNAPSHOT/spring-framework-reference/web.html#mvc-ann-controller-advice)으로 선언하거나 RequestMappingHandlerAdapter에서 직접 구성 할 수 있습니다.  
