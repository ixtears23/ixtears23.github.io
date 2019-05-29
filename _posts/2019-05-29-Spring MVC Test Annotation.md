---
layout: spring-test-post
title: "Spring MVC Test Annotation"
date: 2019-05-29
tags: [test,spring-test]
excerpt: ""
spring-test: true
comments: true
---



## `@SpringJUnit4ClassRunner`

아래 두 클래스는 전혀 다르지 않습니다.  

~~~java
@RunWith(SpringRunner.class)
@RunWith(SpringJUnit4ClassRunner.class)
~~~

아래 `SpringRunner` class 코드를 보면 왜 다르지 않은지 알 수 있습니다.  
~~~java
public final class SpringRunner extends SpringJUnit4ClassRunner {

	/**
	 * Construct a new {@code SpringRunner} and initialize a
	 * {@link org.springframework.test.context.TestContextManager TestContextManager}
	 * to provide Spring testing functionality to standard JUnit 4 tests.
	 * @param clazz the test class to be run
	 * @see #createTestContextManager(Class)
	 */
	public SpringRunner(Class<?> clazz) throws InitializationError {
		super(clazz);
	}

}
~~~

---

java doc 내용

package: `org.springframework.test.context.junit4`  

`SpringJUnit4ClassRunner`는 `JUnit`의 `BlockJUnit4ClassRunner`의 커스텀 확장으로서 `Spring TestContext Framework`의 기능을 `TestContextManager` 및 관련 지원 `class` 및 `annotation`을 통해 표준 `JUnit` 테스트에 제공합니다.  

이 클래스를 사용하려면 `JUnit4` 기반 테스트 클래스에 `@RunWith(SpringJUnit4ClassRunner.class)` 또는 `@RunWith(SpringRunner.class)`로 `annotation`을 달기 만하면됩니다.  

다음 목록은 현재 `SpringJUnit4ClassRunner`가 직접 또는 간접적으로 지원하는 모든 `annotation`을 구성합니다.  

 - `@Test(expected=...)`
 - `@Test(timeout=...)`
 - `@Timed`
 - `@Repeat`
 - `@Ignore`
 - `@ProfileValueSourceConfiguration`
 - `@IfProfileValue`

`Spring TestContext Framework`를 이 `Runner`가 아닌 다른 `Runner`와 함께 사용하려면 `SpringClassRule`과 `SpringMethodRule`을 사용하십시오.  

`Spring Framework 4.3`부터 이 클래스는 `JUnit 4.12` 이상이 필요합니다.  

---


### `TestContextManager`
package: `org.springframework.test.context`  

`TestContextManager`는 `Spring TestContext Framework`에 대한 **주요 진입 점** 이다.  
특히 `TestContextManager`는 단일 `TestContext`를 관리하고 다음 테스트 실행 지점에 등록 된 모든 `TestExecutionListener`에 이벤트를 전달합니다.  

 - 테스트 클래스 실행 전 : 특정 테스트 프레임워크의 클래스 콜백 전에. (예 : `JUnit4`의 `@BeforeClass`) 이전

### `TestContext`
package: `org.springframework.test.context`  
`TestContext`는 사용 중인 실제 테스트 프레임워크에 관계 없이 테스트가 실행되는 컨텍스트를 캡슐화한다.  

봐도 모르겠음..



## `@WebAppConfiguration`

## `@ContextConfiguration`
