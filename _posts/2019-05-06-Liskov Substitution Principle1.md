---
layout: java-solid-post
title: "리스코프 치환 원칙(Liskov Substitution Principle)[2]"
date: 2019-05-06
excerpt: ""
tags: [java,solid,lsp]
java-solid: true
comments: true
---


## 리스코프 치환 원칙 이란 무엇입니까?

Liskov Substitution Principle에 대한 자세한 내용을 제공하는 [이 보고서](https://drive.google.com/file/d/0BwhCYaYDn8EgNzAzZjA5ZmItNjU3NS00MzQ5LTkwYjMtMDJhNDU5ZTM0MTlh/view)를 살펴보십시오.  
LSP에 따라 기본 클래스에 대한 참조를 사용하는 함수는 파생 클래스의 객체를 알지 못해도 사용할 수 있어야합니다.  
**간단히 말해 파생 클래스는 기본 클래스로 대체 할 수 있어야합니다.**  
LSP를 설명하기 위해 직사각형과 정사각형의 예를 들어 봅시다.  
하나는 IS-A 관계를 확립하는 경향이 있으므로, 정사각형을 직사각형으로 말할 수 있습니다.  
그러나 다음 코드 샘플을 통해 입증해야 할 문제가 발생합니다 (LSP 위반).  
LSP를 자세히 이해하려면 아래의 코드 그림을 참조하십시오.  

## LSP를 설명하는 Java 코드 샘플


LSP는 사각형 및 직사각형 예제를 사용하여 널리 설명됩니다.  
정사각형과 직사각형 사이에 IS-A 관계를 설정 하려고 한다고 가정 해 봅시다.  
따라서 우리는 "정사각형은 직사각형"라고 부릅니다. 아래 코드는 관계를 나타냅니다.  

~~~java
/**
 * Rectangle
 * @author Ajitesh Shukla
 */
public class Rectangle {
    private int length;
    private int breadth;
    public int getLength() {
        return length;
    }
    public void setLength(int length) {
        this.length = length;
    }
    public int getBreadth() {
        return breadth;
    }
    public void setBreadth(int breadth) {
        this.breadth = breadth;
    }
    public int getArea() {
        return this.length * this.breadth;
    }
}
~~~

아래는 정사각형의 코드입니다. Square는 Rectangle을 확장합니다.  

~~~java
/**
 * Square class; Square는 Rectangle에서 상속받습니다.;
 * ISA 관계를 나타냅니다. - 정사각형은 직사각형입니다.
 * @author Ajitesh Shukla
 */
public class Square extends Rectangle {
    @Override
    public void setBreadth(int breadth) {
        super.setBreadth(breadth);
        super.setLength(breadth);
    }
    @Override
    public void setLength(int length) {
        super.setLength(length);
        super.setBreadth(length);
    }
}
~~~

아래 코드는 Rectangle(직사각형)을 인수로 갖는 함수를 나타냅니다.  
원칙에 따라 기본 클래스에 대한 참조를 사용하는 함수는 모르는 상태에서 파생 클래스의 객체를 사용할 수 있어야합니다.  
따라서 아래에 있는 예제에서 "Rectangle(직사각형)"참조를 사용하는 calculateArea 함수는
Square(정사각형)와 같은 파생 클래스의 객체를 사용할 수 있어야하고 Rectangle(직사각형) 정의에서 요구하는 요구 사항을 충족해야합니다.  

아래 코드에서 calculateArea 메서드를 살펴보십시오.  
Rectangle의 정의에 따라 다음 데이터를 고려할 때 항상 다음 조건이 충족 되어야 합니다.

 - 길이(Length)는 항상 메소드의 setLength에 대한 입력으로 전달된 길이와 동일하지 않으면 안됩니다.  
 - 폭(Breadth)은 항상 메소드 setBreadth에 대한 입력으로 전달된 너비와 동일해야 합니다.  
 - 면적은 항상 길이와 너비의 곱과 동일해야합니다.  

이 경우 Square(정사각형)의 인스턴스가 전달되면 다음 코드에서 "정사각형은 직사각형이다."를 호출하면
예기치 않게 작동하기 때문에 Square(정사각형)와 Rectangle(직사각형)간에 IS-A 관계를 설정하려고합니다.  
영역 검사 오류로 인해 어설 션 오류가 발생하면 프로그램이 종료되지만 "영역"을 확인하고 "폭"을 확인하는 경우 어설 션 오류가 발생합니다.  


~~~java
/**
 * The class demonstrates the Liskov Substitution Principle (LSP)
 *
 * 원칙에 따라 기본 클래스에 대한 참조를 사용하는 함수는 모르는 상태에서 파생 클래스의 객체를 사용할 수 있어야합니다.
 * 따라서 아래의 예에서 Rectangle의 참조를 사용하는 calculateArea 함수는 Square와 같은 파생 클래스의 객체를 사용할 수 있어야하며
 * Rectangle 정의에 의해 요구되는 요구 사항을 충족해야합니다.
 *  
 * @author Ajitesh Shukla
 */
public class LSPDemo {
    /**
    * Rectangle의 정의에 따르면, 다음 데이터가 주어지면 항상 다음을 수행해야합니다.
    * 1. 길이(Length)는 항상 메소드의 setLength에 대한 입력으로 전달된 길이와 동일하지 않으면 안됩니다.
    * 2. 폭(Breadth)은 항상 메소드 setBreadth에 대한 입력으로 전달된 너비와 동일해야 합니다.  
    * 3. 면적은 항상 길이와 너비의 곱과 동일해야합니다.  
    *
    * 스퀘어와 직사각형 사이에 ISA 관계를 설정하려고합니다.
    * 즉, "정사각형은가 직사각형입니다."라고 말하면, 스퀘어 인스턴스가 전달되면 코드가 예기치 않게 작동하기 시작합니다.
    * 영역을 검사하고 폭을 검사 할 때 어설 션 오류가 발생합니다. 영역 검사 실패로 인해 어설 션 오류가 발생하면 프로그램이 종료됩니다.    *   
    * @param r Instance of Rectangle
    */
    public void calculateArea(Rectangle r) {
        r.setBreadth(2);
        r.setLength(3);
        //
        // Assert Area
        //
        // 코드에서 예상되는 동작은 다음과 같습니다.
        // 직사각형의 면적은 6
        //
        assert r.getArea() == 6 : printError("area", r);
        //
        // Assert Length & Breadth
        //
        // 코드에서 예상되는 동작은 다음과 같습니다.
        // 길이는 항상 3과 같아야하고
        // 너비는 항상 2와 같아야합니다.
        //
        assert r.getLength() == 3 : printError("length", r);
        assert r.getBreadth() == 2 : printError("breadth", r);
    }
    private String printError(String errorIdentifer, Rectangle r) {
        return "Unexpected value of " + errorIdentifer + "  for instance of " + r.getClass().getName();
    }
    public static void main(String[] args) {
        LSPDemo lsp = new LSPDemo();
        //
        // Rectangle 인스턴스가 전달됩니다.
        //
        lsp.calculateArea(new Rectangle());
        //
        // Square 인스턴스가 전달됩니다.
        //
        lsp.calculateArea(new Square());
    }
}
~~~

위의 코드를 감안할 때 Square-Rectangle ISA 관계의 문제점은 무엇입니까?  
 - Square 클래스는 정사각형의 변이 동일하므로 setBreadth 또는 setLength와 같은 메소드를 필요로 하지 않습니다.  
   이것은 **낭비** 입니다. 수십만 개의 정사각형 물체를 상상해보십시오.  
 - LSPDemo 클래스는 throw 오류를 피하기 위해 Rectangle (Square와 같은) 파생 클래스의 세부 정보를 적절히 코딩해야합니다.  
   파생 클래스를 처리하기 위해 기존 코드가 변경되면 처음에는 open-closed principle이 적용되지 않습니다.  

### LSP는 어떤 코드 품질 특성을 나타 냅니까?  

다음은 Liskov 대체 원칙이 나타내는 코드 품질 특성 중 일부입니다.  
 - 파생 된 유형이 해당 기본 유형을 완전히 대체 할 수 있는 경우에만 해당 기본 유형을 사용하는 함수가 무분별하게 재사용 될 수 있고 파생 된 유형을 변경하지 않을 수 있습니다.  
 - LSP는 때로는 "Design by Contract"라고도 불립니다. 이 체계를 사용하여 클래스의 메소드는 사전 조건과 사후 조건을 선언합니다. 메소드가 실행 되려면 사전 조건이 true여야 합니다. 완료시, 메소드는 사후 조건이 true가 되도록 보장합니다.  
 - Design by Contract는 "throw" Exception의 선언과 런타임 Exception의 throw 및 일반적으로 try/catch에 영향을줍니다.  
