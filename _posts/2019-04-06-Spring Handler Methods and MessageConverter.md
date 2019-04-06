---
layout: spring-framework-post
title: "Spring Handler Methods and MessageConverter"
date: 2019-04-06
tags: [springframework,encoding]
excerpt: "RequestBody, ResponseEntity, ResponseBody."
spring-framework: true
comments: true
---



`@ResponseBody` 와 `@RequestBody` 가 **요청본문과 응답본문**에 접근하는 건 `HttpMessageConverter` 를 통해 이루어 집니다.  

### @ResponseBody

`@ResponseBody` 는 **메서드 실행결과**를 **응답 본문**(`response body`)으로 취급하겠다고 스프링 MVC에 밝힙니다.  


### @RequestBody

`@ResponseBody`는 클래스 수준에서도 지원되며,이 경우 모든 컨트롤러 메서드에 상속됩니다.  

`@RequestBody` 는 HTTP **요청 본문**(`request body`)에 액세스합니다.  

### ResponseEntity

`ResponseEntity`는 `@ResponseBody`와 비슷하지만 **상태**와 **헤더**가 있습니다.  

~~~java
@GetMapping("/something")
public ResponseEntity<String> handle() {
    String body = ... ;
    String etag = ... ;
    return ResponseEntity.ok().eTag(etag).build(body);
}
~~~


### HttpMessageConverter

`InputStream`과 `OutputStream`을 통해 `HTTP` **요청**과 **응답본문**을
**읽고 쓸 수** 있습니다.  

서버측은 `RequestMethodHandlerAdapter` 에 등록됩니다.  

모든 변환기(converter)의 경우 기본 `media type`이 사용되지만 `supportedMediaTypes` `bean` 등록 정보를 설정하여 이를 대체 할 수 있습니다.  

아래는 HttpMessageConverter 구현체  
- StringHttpMessageConverter  
  - media type: text/*
  - Content-Type: text/plain
- FormHttpMessageConverter  
  - media type: application/x-www-form-urlencoded  
  - form data: MultiValueMap<String, String>  
- ByteArrayHttpMessageConverter  
  - media type: \*/*
  - Content-Type: application/octet-stream
- MarshallingHttpMessageConverter  
  - text/xml, application/xml
- MappingJackson2HttpMessageConverter  
  - application/json
- MappingJackson2XmlHttpMessageConverter  
  - - application/xml
- SourceHttpMessageConverter  
- BufferedImageHttpMessageConverter  
