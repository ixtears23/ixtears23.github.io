---
layout: java-logging-post
title: "log4j2 구성 및 Additivity"
date: 2019-05-18
excerpt: ""
tags: [log,log4j2]
java-logging: true
comments: true
---




### log4j2 의존성 추가 (maven)

~~~xml
<dependency>
  <groupId>org.apache.logging.log4j</groupId>
  <artifactId>log4j-api</artifactId>
  <version>2.11.2</version>
</dependency>
<dependency>
  <groupId>org.apache.logging.log4j</groupId>
  <artifactId>log4j-core</artifactId>
  <version>2.11.2</version>
</dependency>
~~~

### log4j2 의 구성 방법 4가지

1. `XML`, `JSON`, `YAML` 또는 속성 형식으로 작성된 구성 파일  
2. 프로그래밍 방식으로 `ConfigurationFactory` 및 구성 구현을 작성  
3. 구성 인터페이스에 표시된 API를 호출하여 기본 구성에 구성 요소를 추가함으로써 프로그래밍 방식으로 구성  
4. 프로그래밍 방식으로 내부 Logger 클래스의 메서드를 호출  

### log4j2 가 자동으로 설정파일을 찾는 방법

Log4j는 초기화하는 동안 자동으로 구성 할 수 있습니다.  
Log4j가 시작되면 모든 ConfigurationFactory 플러그인을 찾아 가중치가 높은 순서대로 정렬합니다.  
전달 된대로 Log4j에는 `JSON`용, `YAML`용, `properties` 용 및 `XML` 용의 4가지 ConfigurationFactory 구현이 포함되어 있습니다.  

1. Log4j는 `"log4j.configurationFile"` system property를 검사하고 설정되어 있으면 파일 확장명과 일치하는 ConfigurationFactory를 사용하여 구성을 로드 하려고 시도합니다.  
2. 시스템 프로퍼티가 설정되어 있지 않은 경우, ConfigurationFactory 프로퍼티는 클래스 패스에 `log4j2-test.properties`를 검색합니다.  
3. 그러한 파일이 발견되지 않으면 YAML ConfigurationFactory는 classpath에 `log4j2-test.yaml` 또는 `log4j2-test.yml`을 찾는다.  
4. 그런 파일이 발견되지 않으면 JSON ConfigurationFactory는 classpath에 `log4j2-test.json` 또는 `log4j2-test.jsn`을 찾습니다.  
5. 그러한 파일이 발견되지 않으면 XML ConfigurationFactory는 클래스 경로에서 `log4j2-test.xml`을 찾습니다.  
6. 테스트 파일을 찾을 수없는 경우 ConfigurationFactory 속성은 클래스 경로에서 `log4j2.properties`를 찾습니다.  
7. properties 파일을 찾을 수없는 경우 YAML ConfigurationFactory는 classpath에 `log4j2.yaml` 또는 `log4j2.yml`을 찾습니다.  
8. YAML 파일을 찾을 수없는 경우 JSON ConfigurationFactory는 클래스 경로에서 `log4j2.json` 또는 `log4j2.jsn`을 찾습니다.  
9. JSON 파일을 찾을 수 없으면 XML ConfigurationFactory는 클래스 경로에 `log4j2.xml`을 찾습니다.  
10. 구성 파일을 찾을 수 없으면 DefaultConfiguration이 사용됩니다. 이로 인해 로깅 출력이 콘솔로 이동합니다.



~~~java
package com.junseok.blog;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

public class MyApp {

	private static final Logger logger = LogManager.getLogger(MyApp.class);

	public static void main(String[] args) {

        logger.trace("첫번째 트레이스 로그 출력");

        Bar bar = new Bar();
        if (!bar.doIt()) {
            logger.error("Didn't do it.");
        }
        logger.trace("두번째 트레이스 로그 출력");
	}

}

.
.
.

package com.junseok.blog;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

public class Bar {

	static final Logger logger = LogManager.getLogger(Bar.class.getName());

	public boolean doIt() {
		logger.entry();
		logger.error("Did it again!");
		return logger.exit(false);
	}
}
~~~

아무런 설정없이 maven에 의존성만 추가하고 위의 코드 실행 시 output

~~~java
ERROR StatusLogger No Log4j 2 configuration file found. Using default configuration (logging only errors to the console), or user programmatically provided configurations. Set system property 'log4j2.debug' to show Log4j 2 internal initialization logging. See https://logging.apache.org/log4j/2.x/manual/configuration.html for instructions on how to configure Log4j 2
11:00:15.539 [main] ERROR com.junseok.blog.Bar - Did it again!
11:00:15.543 [main] ERROR com.junseok.blog.MyApp - Didn't do it.
~~~

**에러로그 설명**
Log4j 2 구성 파일을 찾을 수 없습니다. 기본 구성 (콘솔에만 오류 로깅) 또는 사용자 프로그래밍 방식으로 제공되는 구성을 사용합니다.  
Log4j 2 내부 초기화 로깅을 표시하려면 시스템 등록 정보 'log4j2.debug'를 설정하십시오.....  

Log4j는 구성 파일을 찾을 수 없는 경우 기본 구성을 제공합니다. `DefaultConfiguration` 클래스에 제공된 기본 구성은 다음을 설정합니다.  
 - 루트 로거에 연결된 ConsoleAppender입니다.  
 - PatternLayout을 ConsoleAppender에 첨부 된 패턴 `%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n`으로 설정합니다.  
기본적으로 Log4j는 루트 로거를 `Level.ERROR`에 할당합니다.  


아래 xml은 아무런 설정을 하지 않았을 때의 기본 구성과 동일한 xml 설정 입니다.  

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
  <Appenders>
    <Console name="Console" target="SYSTEM_OUT">
      <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
    </Console>
  </Appenders>
  <Loggers>
    <Root level="error">
      <AppenderRef ref="Console"/>
    </Root>
  </Loggers>
</Configuration>
~~~

위의 xml 설정을 classpath에 log4j2.xml로 배치하고 다시 실행하면 아래 결과가 표시 됩니다.  
오류 메세지는 표시되지 않고 log 메세지만 표시 됩니다.  

~~~java
11:02:32.654 [main] ERROR com.junseok.blog.Bar - Did it again!
11:02:32.658 [main] ERROR com.junseok.blog.MyApp - Didn't do it.
~~~

테스트하기 위해서 로그 수준을 변경합니다.  
log4j2.xml 의 Root 태그의 level 속성 값을 error 에서 trace 로 변경합니다.  
그러면 아래처럼 출력 됩니다.  

~~~java
11:03:50.613 [main] TRACE com.junseok.blog.MyApp - 첫번째 트레이스 로그 출력
11:03:50.619 [main] TRACE com.junseok.blog.Bar - Enter
11:03:50.619 [main] ERROR com.junseok.blog.Bar - Did it again!
11:03:50.619 [main] TRACE com.junseok.blog.Bar - Exit with(false)
11:03:50.619 [main] ERROR com.junseok.blog.MyApp - Didn't do it.
11:03:50.622 [main] TRACE com.junseok.blog.MyApp - 두번째 트레이스 로그 출력
~~~

### Additivity
com.junseok.blog.Bar 를 제외한 모든 항목에서 모든 TRACE 출력을 제거해야 된다고 가정해 보겠습니다.  
단순히 로그 수준을 변경해도 원하는 결과를 얻을 수 없습니다. 대신 구성에 새 로거 정의를 추가하는 것이 해결책입니다.  
아래와 같은 형식 입니다.  

~~~xml
<Logger name="com.junseok.blog.Bar" level="TRACE"/>
<Root level="ERROR">
  <AppenderRef ref="STDOUT">
</Root>
~~~

이 구성을 사용하면 com.junseok.blog.Bar의 모든 로그 이벤트가 기록되며 다른 모든 구성 요소의 오류 이벤트 만 기록됩니다.  

~~~java
11:06:21.647 [main] TRACE com.junseok.blog.Bar - Enter
11:06:21.651 [main] ERROR com.junseok.blog.Bar - Did it again!
11:06:21.651 [main] TRACE com.junseok.blog.Bar - Exit with(false)
11:06:21.651 [main] ERROR com.junseok.blog.MyApp - Didn't do it.
~~~

앞의 예제에서 com.junseok.blog.Bar의 모든 이벤트는 여전히 콘솔에 기록되었습니다.  
이는 com.junseok.blog.Bar의 로거가 부모가 수행하는 동안 구성된 모든 애펜더를 갖고 있지 않기 때문입니다.  
다음 구성으로 변경해 보겠습니다.  

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
  <Appenders>
    <Console name="Console" target="SYSTEM_OUT">
      <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
    </Console>
  </Appenders>
  <Loggers>
  	<Logger name="com.junseok.blog.Bar" level="trace">
  		<AppenderRef ref="Console"/>
  	</Logger>
    <Root level="error">
      <AppenderRef ref="Console"/>
    </Root>
  </Loggers>
</Configuration>
~~~

결과는 다음과 같습니다.  

~~~java
11:14:51.237 [main] TRACE com.junseok.blog.Bar - Enter
11:14:51.237 [main] TRACE com.junseok.blog.Bar - Enter
11:14:51.242 [main] ERROR com.junseok.blog.Bar - Did it again!
11:14:51.242 [main] ERROR com.junseok.blog.Bar - Did it again!
11:14:51.243 [main] TRACE com.junseok.blog.Bar - Exit with(false)
11:14:51.243 [main] TRACE com.junseok.blog.Bar - Exit with(false)
11:14:51.243 [main] ERROR com.junseok.blog.MyApp - Didn't do it.
~~~

com.junseok.blog.Bar의 추적 메시지가 두 번 나타납니다.  
이는 로거 com.junseok.blog.Bar과 연결된 appender가 처음 사용되어 콘솔에 첫 번째 인스턴스를 기록하기 때문입니다.  
다음으로 루트 로거 인 com.junseok.blog.Bar의 부모가 참조됩니다.  
그 다음에 이벤트는 appender로 전달되며,이 콘솔도 콘솔에 기록되어 두 번째 인스턴스가 생성됩니다.  
이것을 가산 (additivity)이라고합니다.  
additivity는 (appender 참조를 구성 할 필요가없는 첫 번째 이전 예제와 같이) 매우 편리한 기능 일 수 있지만  
대부분의 경우 이 동작은 바람직하지 않은 것으로 간주되므로 로거의 additivity 특성을 다음과 같이 설정하여 비활성화 할 수 있습니다.  

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
  <Appenders>
    <Console name="Console" target="SYSTEM_OUT">
      <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
    </Console>
  </Appenders>
  <Loggers>
    <Logger name="com.foo.Bar" level="trace" additivity="false">
      <AppenderRef ref="Console"/>
    </Logger>
    <Root level="error">
      <AppenderRef ref="Console"/>
    </Root>
  </Loggers>
</Configuration>
~~~

결과는 다음과 같습니다.

~~~java
11:19:13.488 [main] TRACE com.junseok.blog.Bar - Enter
11:19:13.493 [main] ERROR com.junseok.blog.Bar - Did it again!
11:19:13.493 [main] TRACE com.junseok.blog.Bar - Exit with(false)
11:19:13.493 [main] ERROR com.junseok.blog.MyApp - Didn't do it.
~~~



### log4j2 를 slf4j 에 바인딩 하기

~~~xml
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j18-impl</artifactId>
    <version>2.11.2</version>
    <scope>test</scope>
</dependency>

<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>1.7.5</version>
</dependency>
~~~
