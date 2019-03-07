---
layout: post
title: "Multipart Resolver"
date: 2017-12-25
excerpt: "spring file upload resolver"
tags: [springframework, Multipart, Resolver]
spring-framework: true
comments: true
---

## Multipart Resolver

`org.springframework.web.multipart` 패키지의 `MultipartResolver` 는  
**파일 업로드**를 포함하여 **다중 요청을 구문 분석**하기위한 전략입니다.  
`Commons FileUpload`를 기반으로하는 구현과  
Servlet 3.0 다중 요청 구문 분석을 기반으로하는 구현이 있습니다.  


멀티 파트 처리를 가능하게하려면,  
`multipartResolver`라는 이름으로 `DispatcherServlet` `Spring` 설정에서  
`MultipartResolver` 빈을 선언해야 한다.  
`DispatcherServlet`은 이를 **감지**하여 들어오는 요청에 적용합니다.  
`content-type`이 `multipart/form-data` 인 `POST`가 수신되면  
`resolver`가 콘텐츠를 구문 분석하고 현재 `HttpServletRequest`를 `MultipartHttpServletRequest`로 래핑하여  
요청 된 매개 변수로 노출되는 것 외에도 해결된(resolved) 부분에 대한 액세스(access)를 제공합니다.  


### Apache Commons FileUpload

`Apache Commons FileUpload`를 사용하려면  
`CommonsMultipartResolver` 유형의 `bean`을 `multipartResolver` 이름으로 구성 할 수 있습니다.  
또한 `classpath`에 대한 종속성으로 `commons-fileupload`가 있어야합니다.  

### Servlet 3.0

Servlet 3.0 다중분할 구문분석(multipart parsing)은 Servlet 컨테이너 구성을 통해 활성화해야합니다.  
 - `Java`에서는 서블릿 등록시 `MultipartConfigElement`를 설정하십시오.  
 - `web.xml`에서 `<multipart-config>`섹션을 서블릿 선언에 추가하십시오.  

다음 예제는 `Servlet` 등록에 `MultipartConfigElement`를 설정하는 방법을 보여줍니다.  

~~~java
public class AppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

    // ...

    @Override
    protected void customizeRegistration(ServletRegistration.Dynamic registration) {

        // 선택적으로 maxFileSize, maxRequestSize, fileSizeThreshold도 설정하십시오.
        registration.setMultipartConfig(new MultipartConfigElement("/tmp"));
    }

}
~~~
`Servlet 3.0` 구성이 완료되면 `multipartResolver`라는 이름의  
`StandardServletMultipartResolver` type의 bean을 추가 할 수 있습니다.  
