---
layout: spring-test-post
title: "Hamcrest Tutorial"
date: 2019-04-17
tags: [test,hamcrest]
excerpt: "단위 테스트를 위해 Hamcrest를 사용하는 방법"
spring-test: true
comments: true
---


# Hamcrest

- 유연한 표현을 만들기 위해 결합 할 수있는 Matchers  

Java에서 태어난 Hamcrest는 현재 여러 언어로 구현되어 있습니다.  

- Java  
- Python  
- Ruby  
- Objective-C  
- PHP  
- Erlang  
- Swift  


## Getting Started

### 소개

Hamcrest은 일치 규칙을 선언적으로 정의 할 수 있도록 정규 일치 개체를 작성하기위한 프레임 워크입니다.  
UI 유효성 검사 또는 데이터 필터링과 같이 matchers가 사용할 수없는 상황이 많이 있지만 matcher는 가장 일반적으로 사용되는 **유연한 테스트를 작성** 하는 영역에 있습니다.  
이 튜토리얼에서는 **단위 테스트를 위해 Hamcrest를 사용하는 방법** 을 보여줍니다.  

#### first Hamcrest test

`JUnit`의 `assertEquals` 메소드를 사용하는 대신, 우리는 **정적으로 가져 오는 Hamcrest** 의 **`assertThat` 구문** 과 **표준 `matcher` 세트** 를 사용합니다.  


~~~java
import org.junit.jupiter.api.Test;
import static org.hamcrest.MatcherAssert.assertThat;
import static org.hamcrest.Matchers.*;

public class BiscuitTest {
  @Test
  public void testEquals() {
    Biscuit theBiscuit = new Biscuit("Ginger");
    Biscuit myBiscuit = new Biscuit("Ginger");
    assertThat(theBiscuit, equalTo(myBiscuit));
  }
}
~~~


`assertThat` 메서드는 테스트 `assertion`을 만들기 위한 양식화 된 문장입니다.  
이 예에서 `assertion`의 주체는 첫 번째 메서드 파라미터인 Biscuit 객체 입니다.  
2번째의 메소드 파라미터는, Biscuit 객체의 매처입니다.  

이 테스트는 Biscuit 클래스가 equals 메서드를 정의하기 때문에 성공합니다.  

테스트에서 하나 이상의 `assertion`이 있는 경우 테스트 된 값에 대한 식별자를 `assertion` 포함 할 수 있습니다.  
~~~java
assertThat("chocolate chips", theBiscuit.getChocolateChipCount(), equalTo(10));

assertThat("hazelnuts", theBiscuit.getHazelnutCount(), equalTo(3));
~~~

#### 기타 테스트 프레임 워크

Hamcrest는 처음부터 다른 단위 테스트 프레임 워크와 통합되도록 설계되었습니다.  
예를 들어, Hamcrest는 JUnit (모든 버전) 및 TestNG와 함께 사용할 수 있습니다.  
(자세한 내용은 전체 Hamcrest 배포와 함께 제공되는 예제를 참조하십시오.)  
다른 테스트 스타일이 Hamcrest와 공존 할 수 있기 때문에 기존 테스트 스위트에서 Hamcrest 스타일의 어설 션을 사용하는 것으로 마이그레이션하는 것은 쉽습니다.  

#### 일반적인 matchers 둘러보기

- Core  
  - anything : 항상 일치. 테스트중인 객체가 무엇인지 신경 쓰지 않으면 유용  
  - describedAs : 사용자 지정 실패 오류 추가  
  - is - 가독성을 높이기위한 데코레이터  
- Logical  
  - allOf : 모두 일치하면(예 : Java &&)    
  - anyOf : 하나라도 일치하면    
  - not : 일치하지 않으면  
- Object  
  - equalTo : Object.equals를 사용해 객체의 동등성을 테스트  
  - hasToString : Object.toString를 테스트  
  - instanceOf, isCompatibleType : 테스트 유형  
  - notNullValue, nullValue : null 테스트  
  - sameInstance : 객체 식별 테스트  
- Beans  
  - hasProperty : JavaBeans 속성 테스트  
- Collections  
  - array - 배열 요소르르 테스트  
  - hasEntry, hasKey, hasValue - map 을 테스트  
  - hasItem, hasItems - 컬렉션에 요소가 포함되어 있는지 테스트  
  - hasItemInArray - 배열에 요소가 들어 있는지 테스트  
- Number  
  - closeTo : 생략  
  - greaterThan, greaterThanOrEqualTo, lessThan, lessThanOrEqualTo : 생략  
- Text
  - equalToIgnoringCase : 대소 문자를 무시한 문자열 동등성 테스트  
  - equalToIgnoringWhiteSpace : 공백의 실행 차이를 무시한 문자열 동등성 테스트
  - containsString, endsWith, startsWith : 문자열 일치 테스트  

- 유쾌한  
`Hamcrest`는 **가능한 한 읽을 수있는 테스트를하기 위해 노력** 합니다.  
다음은 모두 동일합니다.  

~~~java
assertThat(theBiscuit, equalTo(myBiscuit));
assertThat(theBiscuit, is(equalTo(myBiscuit)));
assertThat(theBiscuit, is(myBiscuit));
~~~

마지막 형식은 `(T value)`가 `(equalTo (value))`를 반환하도록 오버로드 되어 있기 때문에 허용됩니다.  

#### 커스텀 matchers 작성하기

Hamcrest는 많은 유용한 matchers와 함께 번들로 제공되지만, 테스트 요구 사항에 맞추기 위해 사용자마다 자신 만의 템플릿을 만들어야 할 필요가 있을 것입니다.  
이것은 일반적으로 동일한 속성 집합을 여러 번 테스트 (그리고 다른 테스트에서)하는 코드 단편을 발견하고 단편을 단일 어설 션으로 묶으려는 경우에 주로 발생합니다.   
독자적인 정규 표현식을 작성함으로써 코드 중복을 제거하고 테스트를 보다 쉽게 ​​읽을 수 있습니다!  

`double` 값의 값이 `NaN`(숫자가 아님) 인 경우 테스트를 위해 자체 matcher를 작성해 보겠습니다. 이것은 우리가 작성하고자하는 테스트입니다.  

~~~java
@Test
public void testSquareRootOfMinusOneIsNotANumber() {
  assertThat(Math.sqrt(-1), is(notANumber()));
}
~~~

그리고 구현은 다음과 같습니다.  

~~~java
package org.hamcrest.examples.tutorial;

import org.hamcrest.Description;
import org.hamcrest.Matcher;
import org.hamcrest.TypeSafeMatcher;

public class IsNotANumber extends TypeSafeMatcher {

  @Override
  public boolean matchesSafely(Double number) {
    return number.isNaN();
  }

  public void describeTo(Description description) {
    description.appendText("not a number");
  }

  public static Matcher notANumber() {
    return new IsNotANumber();
  }

}
~~~

`assertThat` 메소드는, `assertion`의 주제의 형태에 의해 파라미터화 된 `Matcher`를 취하는 범용 메소드입니다.
우리는 `Double` 값에 대해 주장하고 있으므로 `Matcher<Double>`이 필요하다는 것을 알고 있습니다.
`Matcher` 구현의 경우 `TypeSafeMatcher`를 서브 클래싱하는 것이 가장 편리합니다. `TypeSafeMatcher`는 `Double`을 캐스팅합니다.  
- matchesSafely 메소드 만 구현하면됩니다.  
- Double이 NaN인지 단순히 확인합니다.  
- describeTo 메소드 : 테스트가 실패 할 때 실패 메시지를 생성하는 데 사용됩니다.  

다음은 실패 메시지가 표시되는 방법의 예입니다.  

~~~java
assertThat(1.0, is(notANumber()));

// fails with the message

java.lang.AssertionError: Expected: is not a number got : <1.0>
~~~

우리의 `matcher`에서 세 번째 메소드는 편리한 factory 메소드입니다.  
테스트에서 정규식을 사용하기 위해 이 메소드를 정적으로 가져옵니다.  

~~~java
import org.junit.jupiter.api.Test;
import static org.hamcrest.MatcherAssert.assertThat;
import static org.hamcrest.Matchers.*;
import static org.hamcrest.examples.tutorial.IsNotANumber.notANumber;

public class NumberTest {
  @Test
  public void testSquareRootOfMinusOneIsNotANumber() {
    assertThat(Math.sqrt(-1), is(notANumber()));
  }
}
~~~

비록 notANumber 메서드가 호출 될 때마다 새로운 매처를 생성하지만,
이것이 매처의 유일한 사용 패턴이라고 가정해서는 안됩니다.  
따라서 매처의 상태를 확인해야 하므로 매처간에 단일 인스턴스를 다시 사용할 수 있습니다.  
