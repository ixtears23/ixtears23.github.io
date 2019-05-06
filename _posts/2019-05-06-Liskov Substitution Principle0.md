---
layout: java-solid-post
title: "리스코프 치환 원칙(Liskov Substitution Principle)[1]"
date: 2019-05-06
excerpt: ""
tags: [java,solid,lsp]
java-solid: true
comments: true
---

컴퓨터 프로그램에서 S가 T의 서브타입이면 T 유형의 객체는 S 유형의 객체로 대체 될 수 있습니다.
(즉, S 유형의 객체는 T 유형의 객체를 대체 할 수 있음))


간단히 말해서, 객체 지향 프로그램의 어떤 클래스의 객체는 자식 클래스의 객체로 대체 될 수 있습니다.  


### 상속(Inheritance), 다형성(Polymorphism), 서브타이핑(Subtyping)  


- 상속 Inheritance  

상속은 상당히 이해하기 쉬운 개념입니다.  

객체 또는 클래스가 다른 객체 또는 클래스를 기반으로하는 경우입니다.  
클래스가 다른 클래스에서 "상속"되면 상속 된 클래스 (하위 클래스 또는 하위 클래스라고도 함)에
수퍼 클래스 (상위 클래스)의 모든 특성이 포함되지만 새 속성이 추가될 수 있음을 의미합니다.  

일반적인 예를 들어 설명해 보겠습니다.  
클래스 Watch가있는 경우 해당 클래스를 상속받아 PocketWatch 클래스를 가져올 수 있습니다.  
PocketWatch는 여전히 Watch이며, 몇 가지 추가 기능만 있습니다.  


- 다형성 Polymorphism  

객체는 특정 상황에서는 한 방향으로, 다른 상황에서는 다른 방향으로 동작 할 수 있습니다.  
객체 지향 프로그래밍에서이를 상황에 따른 동작이라고합니다.  
어머니는 자녀와 함께 산책을하거나 학교 학부모 모임에 참석할 때 어머니로 행동합니다.  
그러나 그녀가 친구와 함께있을 때, 직장에서 또는 단순히 심부름을 할 때, 그녀는 여자로서 행동 할 것입니다.
(알다시피,이 차이는 그다지 엄격하지 않습니다.)  

아래는 리스코프 치환 원칙 예제입니다.

~~~java

class TrasportationDevice
{
   String name;
   String getName() { ... }
   void setName(String n) { ... }

   double speed;
   double getSpeed() { ... }
   void setSpeed(double d) { ... }

   Engine engine;
   Engine getEngine() { ... }
   void setEngine(Engine e) { ... }

   void startEngine() { ... }
}
~~~

~~~java
class Car extends TransportationDevice
{
   @Override
   void startEngine() { ... }
}
~~~

여기에는 문제가 없습니다.  
자동차는 분명히 수송 장치이며, 여기서는 수퍼 클래스의 `startEngine()` 메서드를 재정의한다는 것을 알 수 있습니다.  


다른 운송 수단을 추가합시다.  

~~~java
class Bicycle extends TransportationDevice
{
   @Override
   void startEngine() /*problem!*/
}
~~~

모든 것이 계획대로 진행되고 있지 않습니다!  
자전거는 운송 수단이지만 엔진이 없으므로 `startEngine()` 메소드를 구현할 수 없습니다.  

이것들은 **Liskov Substitution Principle** 의 위반이 초래하는 종류의 문제 들이며,
대개 아무것도 하지 않거나 실행될 수 없는 방법으로 가장 일반적으로 인식 될 수 있습니다.  

이러한 문제에 대한 **해결책** 은 **올바른 상속 계층 구조** 이며,
우리의 경우 엔진이 있거나 없는 운송 장치 클래스를 차별화하여 문제를 해결할 것입니다.  
자전거는 운송 수단이지만 엔진이 없습니다.  
이 예에서는 운송 수단에 대한 정의가 잘못되었습니다. 엔진이 없어야 합니다.

다음과 같이 `TransportationDevice` 클래스를 리팩터링 할 수 있습니다.  

~~~java
class TrasportationDevice
{
   String name;
   String getName() { ... }
   void setName(String n) { ... }

   double speed;
   double getSpeed() { ... }
   void setSpeed(double d) { ... }
}
~~~

이제 우리는 비 동력 장치 용 `TransportationDevice` 를 확장 할 수 있습니다.  

~~~java
class DevicesWithoutEngines extends TransportationDevice
{  
   void startMoving() { ... }
}
~~~

그리고 동력 장치 용 `TransportationDevice` 를 확장하십시오.  
여기에 `Engine` 객체를 추가하는 것이 더 적절합니다.  


~~~java
class DevicesWithEngines extends TransportationDevice
{  
   Engine engine;
   Engine getEngine() { ... }
   void setEngine(Engine e) { ... }

   void startEngine() { ... }
}
~~~

따라서 우리의 `Car` 클래스는 **Liskov Substitution Principle** 을 고수하면서 더욱 전문화 되었습니다.  

~~~java
class Car extends DevicesWithEngines
{
   @Override
   void startEngine() { ... }
}
~~~

또한 `Bicycle` 클래스는 **Liskov Substitution Principle**을 준수합니다.  

~~~java
class Bicycle extends DevicesWithoutEngines
{
   @Override
   void startMoving() { ... }
}
~~~

### 결론

Java와 같은 객체 지향 언어는 매우 강력하며 개발자로서 엄청난 유연성을 제공합니다.  
클래스를 확장하지만 'Is-A'테스트에 실패한 객체를 작성하는 경우 **Liskov Substitution Principle**을 **위반**할 가능성이 높습니다.
