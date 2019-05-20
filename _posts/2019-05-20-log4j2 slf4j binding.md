---
layout: java-logging-post
title: "log4j2 slf4j binding"
date: 2019-05-20
excerpt: ""
tags: [log,log4j2,slf4j]
java-logging: true
comments: true
---

log4j2 slf4j binding 을 사용하면 slf4j 로 코딩된 애플리케이션을 log4j2로 사용할 수 있습니다.  
**slf4j를 log4j2로 바인딩하는 방법** 은  
단지 `log4j-slf4j-impl-2.11.2.jar` 파일만 추가하면 됩니다.  

**주의!!!**  
slf4j 를 log4j2 로 바인딩 시 `slf4j-log4j12-1.8.0-beta4.jar` 로 착각하면 안됩니다.  
`slf4j-log4j12-1.8.0-beta4.jar` 는 `log4j2` 가 아닌 `log4j` 와의 바인딩 입니다.  

#### pom.xml

##### 첫번째 방법
~~~xml
<dependency>
  <groupId>org.apache.logging.log4j</groupId>
  <artifactId>log4j-slf4j-impl</artifactId>
  <version>2.11.2</version>
</dependency>
~~~

`log4j2` 를 사용하기 위해서는 `log4j-api` 와 `log4j-core` 를 의존성으로 갖고 있어야 합니다.  
하지만 위처럼 `log4j-api` 와 `log4j-core` 가 없어도 `log4j2` 가 동작하는 이유는  
이미 `log4j-slf4j-impl` 이 `log4j-api` 와 `log4j-core`를 갖고 있기 때문 입니다.  


##### 두번째 방법
~~~xml
<dependency>
  <groupId>org.apache.logging.log4j</groupId>
  <artifactId>log4j-slf4j-impl</artifactId>
  <version>2.11.2</version>
</dependency>
<dependency>
  <groupId>org.apache.logging.log4j</groupId>
  <artifactId>log4j-api</artifactId>
  <version>2.11.0</version>
</dependency>
<dependency>
  <groupId>org.apache.logging.log4j</groupId>
  <artifactId>log4j-core</artifactId>
  <version>2.11.0</version>
</dependency>
~~~


이번 방법은 maven과 관련이 있습니다.  

`log4j-slf4j-impl` 이 `log4j-api` 와 `log4j-core` 를 가지고 있지만 위 처럼
같은 레벨에 의존성을 추가하게 되면 `log4j-slf4j-impl` 이 갖고 있는 `log4j-api` 와 `log4j-core` 가 아닌
같은 레벨에 있는 `log4j-api` 와 `log4j-core` 를 의존성으로 갖게 됩니다.  
예를 들기위해서 `log4j-slf4j-impl` 와 같은 레벨에 있는 `log4j-api` 와 `log4j-core` 의 `version` 을 `2.11.0` 다르게 명시 했습니다.  

**첫번째 방법의 의존 라이브러리**  
 - `log4j-slf4j-impl-2.11.2`  
 - `log4j-api-2.11.2`  
 - `log4j-core-2.11.2`  

**두번쨰 방법의 의존 라이브러리**  
 - `log4j-slf4j-impl-2.11.2`  
 - `log4j-api-2.11.0`  
 - `log4j-core-2.11.0`  



##### 세번째 방법

~~~xml
<dependency>
  <groupId>org.apache.logging.log4j</groupId>
  <artifactId>log4j-slf4j-impl</artifactId>
  <version>2.11.2</version>
  <exclusions>
    <exclusion>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-api</artifactId>
    </exclusion>
    <exclusion>
      <groupId>org.apache.logging.log4j</groupId>
      <artifactId>log4j-core</artifactId>
    </exclusion>
  </exclusions>
</dependency>
<dependency>
  <groupId>org.apache.logging.log4j</groupId>
  <artifactId>log4j-api</artifactId>
  <version>2.11.0</version>
</dependency>
<dependency>
  <groupId>org.apache.logging.log4j</groupId>
  <artifactId>log4j-core</artifactId>
  <version>2.11.0</version>
</dependency>
~~~


세번째 방법은 `log4j-slf4j-impl` 에서 `log4j-api` 와 `log4j-core` 를 먼저 제외 시키고  
`log4j-api` 와 `log4j-core` 의존성을 추가한 방법 입니다.  

실제로 추가되는 라이브러리는 두번째 방법과 동일합니다.  



#### 사용방법

~~~java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloWorld {
  public static void main(String[] args) {
    Logger logger = LoggerFactory.getLogger(HelloWorld.class);
    logger.info("Hello World");
  }
}
~~~
