---
layout: spring-framework-post
title: "SpringBeanLifeCycle[1]"
date: 2018-01-08
excerpt: ""
tags: [springframework,bean]
spring-framework: true
comments: true
---



~~~java
public class SimpleClass implements InitializingBean, DisposableBean{

    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("BEAN 생성 및 초기화 : afterPropertiesSet() 호출됨");
    }

    @Override
    public void destroy() throws Exception {
        System.out.println("BEAN 생성 소멸 : destroy 호출됨");
    }

    // 중략...
}

~~~

 * InitializingBean Interface를 구현하면 Spring 에서 Bean생성과 properties의존성 주입 후 콜백 afterPropertiesSet()을 호출
 * DispoableBean Interface를 구현하면 Spring 에서 Bean소멸 전 콜백 destroy()를 호출

 #### init-method
 * init-method는 주로 의존성이 제대로 주입되었는지 검사(내부 property가 맞게 주입되었는지)하는 경우 사용된다.
 * 만약 init-method가 아니라 의존성 주입에 대한 검사로직을 생성자에서 하는 경우 문제가 발생한다.
 * 왜냐하면 생성하는 시점에는 property주입을 수행하지 않기 때문이다.
 * 대신 init-method는 spring에서 모든 의존성 주입을 완료한 이후에 호출하기 때문에 init-method에서 의존성 주입 유효성 검사를 체크하여야 한다.
 #### destroy
 * destroy는 주로 자원의 반납을 하는 경우 사용된다.
 * 만약 File 처리와 관련된 Class가 존재하고 필요 기능을 전부 사용한 후에 close를 수행하고자 할 때 가장 적절한 위치는 bean이 소멸되기 직전인 destory다.
 * 흔희 io관련된 자원을 close 하는 부분을 destroy에 작성하곤 한다.
 * 별도로 bean 소멸 시 수행할 작업이 있는 경우에 추가 코드를 작성하면 된다.

출처 : http://javaslave.tistory.com/48?category=534249


 ## 테스트
  > Tomcat 구동 시 afterPropertiesSet() 메소드를 호출
  > Tomcat Stop 시 destroy() 메소드 호출
