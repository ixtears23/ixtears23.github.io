---
layout: spring-framework-post
title: "ApplicationContextAware"
date: 2018-01-08
excerpt: ""
tags: [springframework]
spring-framework: true
comments: true
---


# ApplicationContextAware

 > 스프링의 **ApplicationContextAware** 인터페이스를 구현하면 빈이 실행되는 환경인 ApplicationContext 인스턴스에 접근할 수 있다.
 > ApplicationContextAware 인터페이스는 **setApplicationContext** 메서드하나를 정의한다.

### JAVA 코드

~~~java
package com.user.web;

import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;

public class ApplicationCTXAware implements ApplicationContextAware  {

	private ApplicationContext context;

	@Override
	public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
		this.context = applicationContext;

	}

}

~~~

##### 용도
bean에서 ApplicationContext에 직접 접근 시 사용

##### 사용방법

ApplicationContextAware Interface 구현하고 setApplicationContext(ApplicationContext appCtx) override하여 매개변수를 통해 접근

 > bean이 ApplicationContextAware Interface를 구현하면 bean생성 및 property의존성 주입 완료 후
 > init 메소드 실행전에 ApplicationContextAware.setApplicationContext()를 호출하여준다.
 > 이를 통해 bean은 자신의 인스턴스를 생성관리하는 ApplicationContext가 어떤 인스턴스인지 확인하고 접근할 수 있다.
 > 쉽게 말해 bean을 관리하는 ApplicationContext 인스턴스에 직접 접근이 필요한 경우 사용하는 Interface이다.
 > 많이 사용되지는 않고 ApplicationContext세부 설정을 XML파일 로딩시점에 특정 bean에 위임하는 경우에 사용

출처 : http://javaslave.tistory.com/50

## 테스트
 > 테스트한 해당 프로젝트에   
 > ContextLoader로 설정된 ApplicationContext 와  
 > DispatcherServletContext 가 설정되어 있었음.  

 > Tomcat 구동시  
 > 해당 setApplicationContext() 메소드를 두번 호출함.  
