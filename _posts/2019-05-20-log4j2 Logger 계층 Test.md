---
layout: java-logging-post
title: "log4j2 Logger 계층 Test"
date: 2019-05-20
excerpt: ""
tags: [log,log4j2]
java-logging: true
comments: true
---


#### log4j2.xml
~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
  <Appenders>
    <Console name="Console" target="SYSTEM_OUT">
      <PatternLayout pattern="Console %d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
    </Console>
    <Console name="ConsoleSubOne" target="SYSTEM_OUT">
      <PatternLayout pattern="ConsoleSubOne %d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
    </Console>
    <Console name="ConsoleSubTwo" target="SYSTEM_OUT">
      <PatternLayout pattern="ConsoleSubTwo %d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
    </Console>
    <Console name="RootConsole" target="SYSTEM_OUT">
      <PatternLayout pattern="RootConsole %d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
    </Console>
  </Appenders>
  <Loggers>
  	<Logger name="com.junseok.blog" level="debug" additivity="false">
  		<AppenderRef ref="Console"/>
  	</Logger>
  	<Logger name="com.junseok.blog.MyAppSubOne" level="info" additivity="false">
  		<AppenderRef ref="ConsoleSubOne"/>
  	</Logger>
  	<Logger name="com.junseok.blog.MyAppSubTwo" level="warn" additivity="false">
  		<AppenderRef ref="ConsoleSubTwo"/>
  	</Logger>
    <Root level="error">
      <AppenderRef ref="RootConsole"/>
    </Root>
  </Loggers>
</Configuration>
~~~

#### java 파일

~~~java
package com.junseok.blog;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

import com.junseok.logging.OtherLogging;

public class MyApp {

	private static final Logger logger = LogManager.getLogger(MyApp.class);

	private static MyAppSubOne subOne = new MyAppSubOne();
	private static MyAppSubTwo subTwo = new MyAppSubTwo();
	private static OtherLogging otherLogging = new OtherLogging();


	public static void main(String[] args) {

        logger.trace("MyApp TRACE log");
        logger.debug("MyApp DEBUG log");
        logger.info("MyApp INFO log");
        logger.warn("MyApp WARN log");
        logger.error("MyApp ERROR log");
        logger.fatal("MyApp FATAL log");

        subOne.doLogging();
        subTwo.doLogging();
        otherLogging.doLogging();
	}
}

//----------------------------------------------------------------------

package com.junseok.blog;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

public class MyAppSubOne {

	private static final Logger logger = LogManager.getLogger(MyAppSubOne.class);

	public void doLogging() {

        logger.trace("MyAppSubOne TRACE log");
        logger.debug("MyAppSubOne DEBUG log");
        logger.info("MyAppSubOne INFO log");
        logger.warn("MyAppSubOne WARN log");
        logger.error("MyAppSubOne ERROR log");
        logger.fatal("MyAppSubOne FATAL log");
	}
}

//----------------------------------------------------------------------

package com.junseok.blog;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

public class MyAppSubTwo {

	static final Logger logger = LogManager.getLogger(MyAppSubTwo.class.getName());

	public void doLogging() {

        logger.trace("MyAppSubTwo TRACE log");
        logger.debug("MyAppSubTwo DEBUG log");
        logger.info("MyAppSubTwo INFO log");
        logger.warn("MyAppSubTwo WARN log");
        logger.error("MyAppSubTwo ERROR log");
        logger.fatal("MyAppSubTwo FATAL log");
	}
}

//----------------------------------------------------------------------

package com.junseok.logging;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

public class OtherLogging {

	private static final Logger logger = LogManager.getLogger(OtherLogging.class);

	public void doLogging() {

        logger.trace("OtherLogging TRACE log");
        logger.debug("OtherLogging DEBUG log");
        logger.info("OtherLogging INFO log");
        logger.warn("OtherLogging WARN log");
        logger.error("OtherLogging ERROR log");
        logger.fatal("OtherLogging FATAL log");
	}
}

~~~

#### output

~~~java
Console 21:48:41.872 [main] DEBUG com.junseok.blog.MyApp - MyApp DEBUG log
Console 21:48:41.877 [main] INFO  com.junseok.blog.MyApp - MyApp INFO log
Console 21:48:41.878 [main] WARN  com.junseok.blog.MyApp - MyApp WARN log
Console 21:48:41.878 [main] ERROR com.junseok.blog.MyApp - MyApp ERROR log
Console 21:48:41.878 [main] FATAL com.junseok.blog.MyApp - MyApp FATAL log
ConsoleSubOne 21:48:41.878 [main] INFO  com.junseok.blog.MyAppSubOne - MyAppSubOne INFO log
ConsoleSubOne 21:48:41.878 [main] WARN  com.junseok.blog.MyAppSubOne - MyAppSubOne WARN log
ConsoleSubOne 21:48:41.878 [main] ERROR com.junseok.blog.MyAppSubOne - MyAppSubOne ERROR log
ConsoleSubOne 21:48:41.878 [main] FATAL com.junseok.blog.MyAppSubOne - MyAppSubOne FATAL log
ConsoleSubTwo 21:48:41.878 [main] WARN  com.junseok.blog.MyAppSubTwo - MyAppSubTwo WARN log
ConsoleSubTwo 21:48:41.878 [main] ERROR com.junseok.blog.MyAppSubTwo - MyAppSubTwo ERROR log
ConsoleSubTwo 21:48:41.878 [main] FATAL com.junseok.blog.MyAppSubTwo - MyAppSubTwo FATAL log
RootConsole 21:48:41.878 [main] ERROR com.junseok.logging.OtherLogging - OtherLogging ERROR log
RootConsole 21:48:41.878 [main] FATAL com.junseok.logging.OtherLogging - OtherLogging FATAL log
~~~

`log4j2.xml` 에 `Logger name`에 속하지 않은 클래스의 경우는 `Root Logger` 가 찍힙니다.  
`com.junseok.blog` 로거와 `com.junseok.blog.MyAppSubOne` 이 있는 경우 더 상세한 로거를 찾고 해당 로거에 속하지 않으면 부모 로거를 찾습니다.  
자식: `com.junseok.blog.MyAppSubOne` 부모: `com.junseok.blog`  
한마디로 **자식 로거** 에 속하지 않으면 **부모 로거** 를 찾고 부모 로거 또한 없으면 **Root Logger** 를 사용합니다.  


#### getLogger(...) 로거를 가져오는 방법.

 - **파라미터를 넘기지 않는 경우**  
**호출 측의 클래스 명을 가지는 Logger** 를 돌려줍니다.  
 - **Class 파라미터** 를 넘기는 경우  
**클래스의 완전 수식 명을 Logger 명의로서 사용** 해, Logger를 돌려줍니다.  
 - **String 파라미터** 를 넘기는 경우  
**지정된 이름을 가지는 Logger** 를 돌려줍니다.  

#### Root Logger 를 얻는 방법
~~~java
private static final Logger logger = LogManager.getLogger(MyApp.class);
// or
private static final Logger logger = LogManager.getRootLogger();
~~~
