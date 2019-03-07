---
layout: post
title: "BeanNameGenerator - Bean naming"
date: 2018-03-12
excerpt: "-"
tags: [springframework]
spring-framework: true
comments: true
---


구성 요소가 검색 프로세스의 일부로 **자동 감지**되면
`BeanName`은 해당 스캐너에 알려진 **BeanNameGenerator 전략**에 의해 **생성**됩니다.  
기본적으로 name value를 포함하고있는 모든 Spring 스테레오 타입 어노테이션
(`@Component`, `@Repository`, `@Service` 및 `@Controller`)은 해당 bean 정의(definition)에 해당 이름을 제공합니다.  

그러한 어노테이션에 ***name value가 포함되지 않은 경우***,
또는 ***검출 된 다른 컴퍼넌트 (커스텀 필터로 검출 된 것 등)의 경우***,
**디폴트 bean 네임 제네레이터**는, **캡슐화되지 않고 검증되어 있지 않은(non-qualified) 클래스 명**을 돌려줍니다.  
예를 들어, 다음 **두 구성 요소가 검색된 경우** 이름은 `myMovieLister` 및 `movieFinderImpl`입니다.  
~~~java
@Service("myMovieLister")
public class SimpleMovieLister {
    // ...
}
~~~
~~~java
@Repository
public class MovieFinderImpl implements MovieFinder {
    // ...
}
~~~

---
기본 **`bean-naming` 전략**에 ***의존하기를 원하지 않으면***,
**사용자 정의 `bean-naming` 전략**을 **제공** 할 수 있습니다.  
먼저, **`BeanNameGenerator` 인터페이스를 구현**하고,
**인수가없는 기본 생성자**를 포함시켜야합니다.  
그런 다음 *스캐너를 구성 할 때* **정규화 된 클래스 이름을 제공**하십시오.

---
~~~java
@Configuration
@ComponentScan(basePackages = "org.example", nameGenerator = MyNameGenerator.class)
public class AppConfig {
    ...
}
~~~
~~~xml
<beans>
    <context:component-scan base-package="org.example"
        name-generator="org.example.MyNameGenerator" />
</beans>
~~~

일반적으로 다른 구성 요소가 명시 적으로 참조를 만들 때마다
**어노테이션과 함께 이름을 지정**하는 것이 좋습니다.  
반면, **자동 생성 된 이름**은 **컨테이너가 wiring을 담당 할 때마다 적절**합니다.
