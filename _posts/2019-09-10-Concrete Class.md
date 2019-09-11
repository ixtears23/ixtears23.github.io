---
layout: java-basic-post
title: "Concrete Class"
date: 2019-09-10
excerpt: ""
tags: [Concrete]
java-basic: true
comments: true
---


## Java에서 이야기 하는 Concrete Class 란 무엇인가?

난 `Concrete class` 를 구현 클래스, 구상 클래스, 구체 클래스 등과 같은 말로 굳이 표현하고 싶지 않다.  

`Java`를 자바라고 하듯이 `Concrete Class` 도 컨크리트 클래스라 부르겠다.


`Concrete class`는 `new` `keyword`를 사용해서 인스턴스화 될 수 있어야 한다.  

`class`의 멤버인 모든 메서드가 구현되어 있어야 한다.  


아래 추상클래스는 `new` `keyword`로 인스턴스화 할 수 없다.  
또한 모든 메서드가 구현되지 않았다.  
**그러므로 `Concrete class`가 아니다.**  

~~~java
abstract class Car {
  public void run() {
		System.out.println("달린다.");
	}

  public abstract void openDoor();
}
~~~


아래는 `interface` 이다.
`interface`에는 구현부가 존재하지 않는다.
`java8` 이후 부터는 기본 구현부가 존재하게 되었지만 `interface` 또한 `new` `keyword`로 인스턴스화 할 수 없다.   
**그러므로 `Concrete class`가 아니다.**  

~~~java

interface Gear {
	public void drive();
	public void parking();
}

~~~

아래는 추상클래스를 상속받고 인터페이스를 구현한 클래스이다.  
모든 메서드가 구현되었고 `new` `keyword` 로 인스턴스화 할 수 있다.  
**그러므로 `Concrete class` 이다.**  

~~~java

class Tico extends Car implements Gear {

  @Override
  public void openDoor() {
		System.out.println("티코 문여는 중");
	}

  @Override
	public void drive() {
		System.out.println("드라이브 모드");
	}

  @Override
	public void parking() {
		System.out.println("주차 모드");
	}

}

~~~


아래 `Car` 클래스 추상클래스를 상속받지도 않았고 인터페이스를 구현하지도 않았지만  
메서드가 모두 구현되어 있다.
**그러므로 `Concrete Class` 이다.**  

~~~java
class Car {
	void run() {
		System.out.println("달린다.");
	}
}
~~~
