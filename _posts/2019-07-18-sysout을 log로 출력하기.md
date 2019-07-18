---
layout: java-logging-post
title: "sysout을 log로 출력하기"
date: 2019-07-18
excerpt: "-"
tags: [sysout,log]
java-logging: true
comments: true
---



# System.out.println 을 Log 로 출력 하기

### System print를 logger로 사용하기 위한 구현 코드
~~~java
package com.test.log;

import java.io.PrintStream;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class StdPrintToLogger {

  public static void setSystemOutAndErrToLog() {
    System.setOut(createLoggingProxy(System.out));
    System.setErr(createLoggingProxy(System.err));
  }

  public static PrintStream createLoggingProxy(final PrintStream realPrintStream) {
    return new PrintStream(realPrintStream) {
      public void print(final String msg) {
        realPrintStream.print(msg);
        StackTraceElement[] stacks = Thread.currentThread().getStackTrace();
        StackTraceElement stack = null;
        Logger logger;
        for (int i = 0; i < stacks.length; i++) {
          if (i == 3) {
            stack = stacks[i];
          }
        }
        if(stack != null) {
          String logMessage = String.format("\nmethod:%s\nlineNumber:%s\nsysout:%s", stack.getMethodName(),
          stack.getLineNumber(), msg);
          logger = LoggerFactory.getLogger(stack.getClassName());
          logger.info(logMessage);
        }
      }
    };
  }

}
~~~

## 아래는 java api 문서를 구글에서 번역한 내용입니다.

#### System.setOut(PrintStream out)
> "표준"출력 스트림을 재 할당합니다.  
우선, 시큐러티 매니저가 존재하는 경우, 그 checkPermission 메소드가 RuntimePermission ( "setIO") 액세스권을 사용해 불려가, 「표준」출력 스트림을 재할인할지 어떨지를 확인합니다.

#### System.setErr(PrintStream err)
> "표준"오류 출력 스트림을 재 할당합니다.  
우선, 시큐러티 매니저가 존재하는 경우, 그 checkPermission 메소드가 RuntimePermission ( "setIO") 액세스권을 사용해 불려가 「표준」에러 출력 스트림을 재할인할지 어떨지를 확인합니다.

#### Thread.currentThread()
 > 현재 실행중인 스레드 객체에 대한 참조를 반환합니다.

#### Thread.currentThread().getStackTrace()
 > 이 thread의 스택 덤프를 나타내는 스택 트레이스 요소의 배열을 돌려줍니다.

#### StackTraceElement
> Throwable.getStackTrace ()에 의해 반환되는 스택 트레이스의 요소입니다. 각 요소는 단일 스택 프레임을 나타냅니다. 스택 맨 위에있는 것을 제외한 모든 스택 프레임은 메소드 호출을 나타냅니다. 스택 맨 위의 프레임은 스택 추적이 생성 된 실행 지점을 나타냅니다. 통상, 스택 트레이스에 대응하는 Throw 가능 오브젝트가 생성 된 시점입니다.  

#### PrintStream
> PrintStream는 다른 출력 스트림에 기능을 추가합니다. 즉 다양한 데이터 값의 표현을 편리하게 인쇄 할 수있는 기능입니다. 두 가지 다른 기능도 제공됩니다. 다른 출력 스트림과는 달리, PrintStream은 결코 IOException을 던지지 않습니다. 예외적 인 상황은 checkError 메소드를 통해 테스트 할 수있는 내부 플래그를 설정하기 만합니다. 선택적으로 자동으로 플러시되도록 PrintStream을 만들 수 있습니다. 즉, 바이트 배열의 기입, println 메소드의 호출, 또는 개행 문자 또는 바이트 ( '\ n')의 기입 해에 의해, flush 메소드가 자동적으로 불려갑니다.  
PrintStream에 의해 출력 된 모든 문자는 플랫폼의 기본 문자 인코딩을 사용하여 바이트로 변환됩니다. PrintWriter 클래스는 바이트가 아닌 문자를 써야하는 상황에서 사용해야합니다.  




### Container 구동시 Listener를 이용해서 StdOutToLogger 클래스를 호출한다.
~~~java
package com.test.listener;

import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;
import com.test.log.StdPrintToLogger;

public class ServletInitializedListener implements ServletContextListener {


  @Override
  public void contextInitialized(ServletContextEvent sce) {
    StdPrintToLogger.setSystemOutAndErrToLog();
  }

  @Override
  public void contextDestroyed(ServletContextEvent sce) {
      // ...
  }

}
~~~

## 아래는 Oracle api 문서를 구글에서 번역한 내용입니다.
#### ServletContextListener
> ServletContext 라이프 사이클 변경에 관한 통지 이벤트를 받기위한 인터페이스.  
이러한 알림 이벤트를 수신하려면 구현 클래스를 웹 응용 프로그램의 배포 설명자에서 선언하거나 WebListener로 주석을 지정하거나 ServletContext에 정의 된 addListener 메서드 중 하나를 통해 등록해야합니다.

### web.xml에 Listener 등록
~~~xml
<listener>
  <listener-class>com.test.listener.ServletInitializedListener</listener-class>
</listener>
~~~

### 실행 예
~~~java
package com.test;
.
.
.
100    class LoggerTest {
101        public void print() {
102          System.out.println("테스트");
103        }
104    }

~~~

### output
~~~C#
[2019-07-18 10:30:20,752  INFO com.test.LoggerTest] 
method: print
lineNumber: 102
sysout: 테스트
~~~
