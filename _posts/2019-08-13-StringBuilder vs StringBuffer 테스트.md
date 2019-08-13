---
layout: java-basic-post
title: "StringBuilder vs StringBuffer 테스트"
date: 2019-08-13
excerpt: ""
tags: [String,StringBuilder,StringBuffer]
java-basic: true
comments: true
---

## TEST Java version 은 1.8

## 1억번 for문을 돌렸을 때

~~~java
package performence;

public class TestString {

	public static void main(String[] args) {

		StringBuilder str1 = new StringBuilder("s12");
		StringBuffer str2 = new StringBuffer("s12");
		String str3 = "s12";
		String result = "";

		long start1 = System.currentTimeMillis();
		for(int i = 0; i < 100_000_000; i++) {
			str1.append("str");
		}
		long end1 = System.currentTimeMillis();

		System.out.println((end1-start1) / 1000F + "초 Builder");

		long start2 = System.currentTimeMillis();
		for(int i = 0; i < 100_000_000; i++) {
			str2.append("str");
		}
		long end2 = System.currentTimeMillis();

		System.out.println((end2-start2) / 1000F + "초 Buffer");

		long start3 = System.currentTimeMillis();
		for(int i = 0; i < 100_000; i++) {
			result += str3;
		}
		long end3 = System.currentTimeMillis();

		System.out.println((end3-start3) / 1000F + "초 String");
	}
}
~~~

## output
~~~script
★첫번째 실행결과
1.735초 Builder
4.302초 Buffer
16.075초 String
★두번째 실행결과
1.751초 Builder
4.913초 Buffer
17.963초 String
★세번째 실행결과
1.716초 Builder
5.024초 Buffer
17.091초 String
~~~

## 성능
 - 1등 : Builder
 - 2등 : Buffer
 - 3등 : + 연산자

## debuging
~~~script
문자열을 연결해도 str1 의 id 값은 변하지 않는다.  
문자열을 연결해도 str2 의 id 값은 변하지 않는다.  
문자열을 연결할 때 마다 result 의 id 값이 계속 변함.  
~~~

> String은 불변(immutable)객체 이기 때문에 result 에 + 연산으로 문자열을 연결할 때 마다  
> 새로운 객체를 생성하므로 1만번 반복하면 1만개의 새로운 객체를 생성함.  



## jad로 decompile 한 모습 (심심풀이)

~~~java
package performence;


public class TestString
{
  public static void main(String[] args) {
    StringBuilder str1 = new StringBuilder("s12");
    StringBuffer str2 = new StringBuffer("s12");
    String str3 = "s12";
    String result = "";

    long start1 = System.currentTimeMillis();
    for (int i = 0; i < 100000000; i++) {
      str1.append("str");
    }
    long end1 = System.currentTimeMillis();

    System.out.println(String.valueOf((float)(end1 - start1) / 1000.0F) + "초 Builder");

    long start2 = System.currentTimeMillis();
    for (int i = 0; i < 100000000; i++) {
      str2.append("str");
    }
    long end2 = System.currentTimeMillis();

    System.out.println(String.valueOf((float)(end2 - start2) / 1000.0F) + "초 Buffer");

    long start3 = System.currentTimeMillis();
    for (int i = 0; i < 100000; i++) {
      result = String.valueOf(result) + str3;
    }
    long end3 = System.currentTimeMillis();

    System.out.println(String.valueOf((float)(end3 - start3) / 1000.0F) + "초 String");
  }
}
~~~

#### 변경된 점
- `100_000_000` 숫자가 `100000000` 으로 변경됨.  
- `(end1 - start1)` 앞에 `(float)`가 붙음.  
- `(end1 - start1) / 1000F` 앞에 `String.valueOf`가 붙음. 뒤에 `+` 연산으로 문자열을 연결해서 붙음.  
- `1000F` 에서 `1000.0F` 로 변경됨.  
- `result += str3` 이 `String.valueOf(result) + str3` 으로 변경됨.  


---


## 아래 코드를 실행하게 되면 에러 발생

> `Exception in thread "main" java.lang.OutOfMemoryError: Java heap space`  
> 100번 밖에 for문을 돌리지 않았지만 `OutOfMemoryError` 가 발생.  
> 문자열에 문자열을 더하면 OutOfMemoryError 가 발생한다.

~~~java
package performence;

public class TestString {

	public static void main(String[] args) {

		String str = "s12";

		long start = System.currentTimeMillis();
		for(int i = 0; i < 100; i++) {
			str += str;
		}
		long end = System.currentTimeMillis();

		System.out.println((end - start) / 1000F + "초 String");
	}
}
~~~



## 하지만 아래처럼 다른 변수에 담게 되면 에러가 발생하지 않는다.

~~~java
package performence;

public class TestString {

	public static void main(String[] args) {

		String str = "s12";
		String result = "";

		long start = System.currentTimeMillis();
		for(int i = 0; i < 100_000; i++) {
			result += str;
		}
		long end = System.currentTimeMillis();

		System.out.println((end - start) / 1000F + "초 String");
	}
}
~~~

#### output
~~~script
15.803초 String
~~~

너무 느리다...


`+` 연산자를 이용해서 문자열은 연결하게 되는 경우  
하나의 변수를 사용해서 연결하게 되면 `OutOfMemoryError`가 발생한다.  
하지만 다른 변수를 하나 만들어서 연결하게 되면  
속도는 상당히 느리지만 `OutOfMemoryError` 는 발생하지 않는다.  

이런 이슈가 있다는걸 알게 됐지만  
나중에 시간이 될 때 이유에 대해서도 찾아봐야 겠다.  
시간 관계상 여기까지..  


※참고

`HeapMemory` 등을 확인해 보고 싶어서
`visual VM`을 사용해서 찾아 봤다.  

여기에 대해서는 나중에 심도있게 찾아볼 시간이 생긴다면..  
