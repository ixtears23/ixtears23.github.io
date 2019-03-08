---
layout: spring-framework-post
title: "SpringBeanLifeCycle[3]"
date: 2018-01-08
excerpt: ""
tags: [springframework,bean]
spring-framework: true
comments: true
---



# 빈 생명주기

위치 : dispatcher-servlet.xml

`<context:component-scan base-package="spring.mvc" use-default-filters="true">`
> `use-default-filters=false` 하게되면  
> `@Component`, `@Repository`, `@Service` 또는 `@Controller`로 주석 첨부 된 클래스의 자동 검출 하지 못한다.  

> 기본적으로 Spring이 제공하는  
> `@Component`, `@Repository`, `@Service`, `@Controller`, `@RestController`, `@ControllerAdvice`,  
> @Configuration 스테레오 타입이 감지된다.  

> 이 태그는 자동 감지 된 구성 요소에 일반적으로 필요한 구성 요소 클래스에  
> `@Required`, `@Autowired`, `@PostConstruct`, `@PreDestroy`, `@Resource`, `@PersistenceContext` 및  
> `@PersistenceUnit` 주석을 활성화하는 'annotation-config'태그의 효과를 의미합니다. (외부 구성없이).  

 Spring2.5이하에서는 모든 빈을 xml에 등록해야 했다.  


`<context:annotation-config/>`
 > Spring의 `@Required` 및 `@Autowired`뿐만 아니라 JSR 250의 `@PostConstruct`, `@PreDestroy` 및 `@Resource` (사용 가능한 경우),  
 > JAX-WS의 `@WebServiceRef` (사용 가능한 경우) EJB 3의 `@EJB` (사용 가능한 경우) 및 JPA의 `@PersistenceContext` 및  
 > `@PersistenceUnit` (사용 가능한 경우) 또는 해당 주석에 대해 개별 BeanPostProcessors를 활성화하도록 선택할 수 있습니다.  

 > Spring의 `@Transactional` 또는 EJB 3의 `@TransactionAttribute` 주석 처리를 활성화하지 않습니다.  
 > 그 목적으로 `<tx:annotation-driven>` 태그를 사용하는 것을 고려하십시오.  

 > * 쉽게 말해서 `@Required`,`@Autowired`,`@PostConstruct`, `@PreDestroy`, `@Resource`, `@WebServiceRef`, `@EJB`,  
 > `@PersistenceContext`, `@PersistenceUnit` 어노테이션을 활성화 하기 위해서는  
 > xml에 `<context:annotation-config/>` 태그를 사용해야 한다.  
 > * `<context:annotation-config/>` 태그는 bean을 등록하는 태그가 아니다!

 > ##### 결론적으로 `<context:component-scan>`을 사용하면 `<context:annotation-config>` 기능이 암시 적으로 활성화됩니다.  
 > ##### `<context:component-scan>`을 사용할 때는 일반적으로 `<context:annotation-config>` 요소를 포함 할 필요가 없습니다.


### `@PostConstruct` and `@PreDestroy`
> `@PostConstruct` : bean 초기화시 수행  
> `@PreDestroy` : bean 소멸시 수행  
