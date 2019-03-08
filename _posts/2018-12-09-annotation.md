---
layout: post-java-basic
title: "Java Annotation"
date: 2018-12-09
excerpt: "Java 주석"
tags: [java,annotation]
java-basic: true
comments: true
---


# 애너테이션(annotation)

소스코드에 대한 문서를 따로 만들기보다 소스코드와 문서를 하나의 파일로 관리하는 것이 좋다.  
소스코드의 주석 `/**~*/`에 소스코드에 대한 정보를 저장하고,  
소스코드의 주석으로부터 HTML 문서를 생성해내는 프로그램(javadoc.exe)을 만들어서 사용.
{: .notice}

## 표준 애너테이션

자바에서 기본적으로 제공하는 애너테이션  
{: .notice}

 - `@Override`
 - `@Deprecated`
 - `@SuppressWarnings`
 - `@SafeVarargs`
 - `@FunctionalInterface`
 - `@Native`

### 표준 애너테이션 중 메타애너테이션

애너테이션을 정의하는데 사용되는 애너테이션
{: .notice}

 - `@Target`
 - `@Documented`
 - `@Retention`
 - `@Inherited`
 - `@Repeatable`

#### `@Target`
애너테이션이 적용가능한 대상을 지정하는데 사용.
{: .notice}

적용대상의 종류는 `java.lang.annotation.ElementType` enum 클래스로 지정할 수 있다.  
 - TYPE: class, interface, enum  
 - FIELD: 멤버변수, enum 상수  
 - METHOD: 메서드  
 - PARAMETER: 매개변수  
 - CONSTRUCTOR: 생성자  
 - LOCAL_VARIABLE: 지역변수  
 - ANNOTATION_TYPE: 애너테이션  
 - PACKAGE: 패키지  
 - TYPE_PARAMETER: 타입 매개변수 (since 1.8)  
 - TYPE_USE: 타입이 사용되는 모든 곳 (since 1.8)  


#### `@Retention`

애너테이션이 유지되는 기간을 지정하는데 사용
{: .notice}

`java.lang.annotation.RetentionPolicy` enum 클래스

 - SOURCE  
   - 소스파일에만 존재. 클래스 파일에는 존재하지 않음.  
   - `@Override` 나 `@SuppressWarnings` 처럼 컴파일러가 사용하는 애너테이션은 `SOURCE`.  
 - CLASS  
   - 클래스 파일에 존재. 실행시에 사용불가. 디폴트 값.  
   - 클래스 파일에 저장할 수 있게는 하지만, 클래스 파일이 JVM에 로딩될 때는 애너테이션의 정보가 무시되어
   실행 시에 애너테이션에 대한 정보를 얻을 수 없다. 그래서 기본값임에도 잘 사용되지 않음.  
 - RUNTIME
   - 클래스 파일에 존재. 실행시에 사용 가능.  
   - 실행시에 '리플렉션(reflection)'을 통해 클래스 파일에 저장된 애너테이션의 정보를 읽어서 처리할 수 있다.  
   - 예: `@FunctionalInterface`  

※ **지역 변수에 붙은 애너테이션**은 **컴파일러만 인식**할 수 있으므로,
**유지정책이 'RUNTIME'** 인 애너테이션을 **지역변수에 붙여도 실행 시에는 인식되지 않는다.**  
{: .notice}

#### `@Documented`

애너테이션에 대한 정보가 javadoc으로 작성한 문서에 포함되도록 한다.  
{: .notice}

#### `@Inherited`

애너테이션이 자손 클래스에 상속되도록 한다.  
조상클래스에 이 애너테이션을 붙이면, 자손 클래스도 이 애너테이션이 붙은 것과 같이 인식된다.  
{: .notice}

#### `@Repeatable`

보통은 하나의 대상에 한 종류의 애너테이션을 붙이는데, `@Repeatable`이 붙은 애너테이션은 여러 번 붙일 수 있다.  
{: .notice}

##### 작성 방법
~~~java
package junseok.annotation;

import java.lang.annotation.Repeatable;

@Repeatable(ToDos.class)
public @interface ToDo {
	String value();
}
~~~

같은 이름의 애너티에션 여러 개가 하나의 대상에 적용될 수 있기 때문에,  
아래와 같이 애너테이션들을 하나로 묶을 수 있는 애너테이션도 추가로 만들어줘야 한다.  
{: .notice}

~~~java
package junseok.annotation;

public @interface ToDos {
	ToDo[] value();
}
~~~

##### 사용 예
~~~java
package junseok.annotation;

@ToDo("delete test codes")
@ToDo("override inherited methods")
public class MyClass {

}
~~~

#### `@Native`

네이티브 메서드에 의해 참조되는 상수 필드에 붙이는 애너테이션.  
아래는 `java.lang.Long` 클래스에 정의된 상수이다.  
{: .notice}

~~~java
package java.lang;

import java.lang.annotation.Native;
...

public final class Long extends Number implements Comparable<Long> {
   @Native public static final long MIN_VALUE = 0x8000000000000000L;
   ...
}
~~~

##### 네이티브 메서드
JVM 이 설치된 OS의 메서드를 말한다.  
보통 C언어로 작성되어 있는데, 자바에서는 메서드의 선언부만 정의하고 구현은 하지 않는다.  
그래서 추상 메서드처럼 선언부만 있고 몸통이 없다.  
{: .notice}

~~~java
package java.lang;

public class Object {
   private static native void registerNatives();
   ...
   public final native Class<?> getClass();
   ...
}
~~~

이처럼 모든 클래스의 조상인 Object클래스의 메서드들은 대부분 네이티브 메서드이다.  
네이티브 메서드는 자바로 정의되어 있기 때문에 호출하는 방법은 자바의 일반 메서드와 다르지 않지만 실제로 호출되는 것은 OS의 메서드이다.  
자바에 정의된 네이티브 메서드와 OS의 메서드를 연결해주는 작업이 추가로 필요한대..
이 역할은 JNI(Java Native Interface) 가 한다. 이 내용은 여기서는 설명하지 않는다.  
{: .notice}


## 애너테이션 정의

~~~java
@interface 애너테이션이름 {
   타입 요소이름();   // 애너테이션 요소 선언
   ...
}
~~~

### 애너테이션 요소

 - 반환값이 있고 매개변수는 없는 추상 메서드의 형태  
 - 애너테이션 적용 시 요소들의 값을 빠짐없이 지정해야 한다.  
 - 각 요소는 기본값을 가질 수 있다. 값을 지정하지 않으면 기본값이 사용 된다.  
 - 요소가 하나이고 이름이 value인 경우 요소의 이름을 생략하고 값만 적어도 된다.  
 - 배열인 경우, 괄호{}를 사용.  

모든 애너테이션의 조상은 Annotation이지만, 애너테이션은 상속이 허용되지 않는다.  
{: .notice}

~~~java
package java.lang.annotation;

public interface Annotation {
   boolean equals(Object obj);
   int hashCode();
   String toString();
   Class<? extends Annotation> annotationType();
}
~~~

### 마커 애너테이션 Marker Annotation
값을 지정할 필요가 없는 경우, 애너테이션의 요소를 하나도 정의하지 않을 수 있다.  
{: .notice}


## 애너테이션 정의하고 애너테이션 요소의 값 출력 예제

~~~java

import java.lang.annotation.*;

@Deprecated
@SuppressWarnings("1111")
@TestInfo(testedBy="aaa", testDate=@DateTime(yyyymmdd="160101", hhmmss="233959"))
public class AnnotationEx {

	public static void main(String[] args) {

		Class<AnnotationEx> cls = AnnotationEx.class;

		TestInfo anno = (TestInfo) cls.getAnnotation(TestInfo.class);

		System.out.println("anno.testedBy()=" + anno.testedBy());
		System.out.println("anno.testDate().yyyymmdd()=" + anno.testDate().yyyymmdd());
		System.out.println("anno.testDate().hhmmss()=" + anno.testDate().hhmmss());

		for(String str : anno.testTools()) {
			System.out.println("testTools=" + str);
		}

		System.out.println();

		Annotation[] annoArr = cls.getAnnotations();
		for(Annotation a : annoArr) {
			System.out.println(a);
		}

	}

}


@Retention(RetentionPolicy.RUNTIME)
@interface TestInfo {
	int 		count() 		default 1;
	String 		testedBy();
	String[] 	testTools()		default "JUnit";
	TestType	testType()		default TestType.FIRST;
	DateTime 	testDate();
}

@Retention(RetentionPolicy.RUNTIME)
@interface DateTime {
	String yyyymmdd();
	String hhmmss();
}

enum TestType { FIRST, FINAL }

~~~

모든 클래스 파일은 클래스로더(Classloader)에 의해 메모리에 올라 갈 때 클래스에 대한 정보가 담긴 객체를 생성하는데 이 객체를 클래스 객체라고 한다. 이 객체를 참조할 때는 '클래스이름.class' 의 형식을 사용 한다.  
{: .notice}



**※ JAVA의 정석 3rd Edition 을 참고해서 정리함.**
