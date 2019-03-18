---
layout: java-solid-post
title: "단일 책임 원칙(Single Responsibility Principle)"
date: 2019-03-18
excerpt: ""
tags: [java,solid,srp]
java-solid: true
comments: true
---


## Class 가 변경되어야 할 이유는 단 하나여야만 합니다.  

모든 모듈 또는 클래스는 소프트웨어가 제공하는 기능의 단일 부분에 대해 책임을 져야하며
그 책임은 클래스에 의해 완전히 캡슐화되어야합니다.  
예를 들어 탐색 소프트웨어의 시나리오를 상상해보십시오.
위치가 바뀌어야하는 방향 (북쪽, 남쪽, 서쪽, 동쪽)에 따라 위치가 결정됩니다.  
Position 클래스에는 x 및 y 축 위치와 관련된 값이 들어 있습니다.  

~~~java
package com.solid.srp.sample01;
public class Position {
    private Integer xAxis;
    private Integer yAxis;
    public Position(Integer xAxis, Integer yAxis) {
        this.xAxis = xAxis;
        this.yAxis = yAxis;
    }
    public Integer getxAxis() {
        return xAxis;
    }
    public void setxAxis(Integer xAxis) {
        this.xAxis = xAxis;
    }
    public Integer getyAxis() {
        return yAxis;
    }
    public void setyAxis(Integer yAxis) {
        this.yAxis = yAxis;
    }
}
~~~

방향은 북쪽, 동쪽, 남쪽 및 서쪽 방향을 나타내는 enum입니다.  

~~~java

package com.solid.srp.sample01;
public enum Direction {
    N,W,S,E
}
~~~

그리고 마지막으로 방향과 위치 변경에 따라 네비게이션을 담당하는 Navigator 클래스가 있습니다.  
제대로 탐색하기 위해서는 탐색기가 방향에 따라 다음 위치를 확인해야합니다. 또한 네비게이터는 값이 0보다 작은 경우 위치를 고정해야합니다.

~~~java
package com.solid.srp.sample01;
public class Navigator {
    public Position navigate(Position position, Direction direction) {
        Position nextPosition = resolve(position,direction);
        Position fixedPosition =fix(nextPosition);
        return fixedPosition;
    }
    public Position resolve(Position position,Direction direction) {
        switch (direction) {
            case N:
                return new Position(position.getxAxis(),position.getyAxis()+1);
            case S:
                return new Position(position.getxAxis(),position.getyAxis()-1);
            case W:
                return new Position(position.getxAxis()-1,position.getyAxis());
            case E:
                return new Position(position.getxAxis()+1,position.getyAxis());
            default:
                throw new IllegalArgumentException();
        }
    }
    public Position fix(Position position) {
        return new Position(
                position.getxAxis()<0?0:position.getxAxis(),
                position.getyAxis()<0?0:position.getyAxis()
        );
    }
}
~~~

이 방법의 문제점은 위치 유효성 기준이 변경되면 Navigator 클래스를 변경해야한다는 것입니다.
위치 이동 메커니즘이 변경되는 경우에도 마찬가지입니다.
네비게이터는 탐색하는 대신 다음 위치를 해결하고 새로운 위치를 수정하는 역할을합니다.
단일 책임 원칙을 어기지 않는 접근법은 다음 위치를 해결할 클래스와 새 위치를 수정하는 클래스를 만드는 것입니다.
NextPositionResolver 클래스는 지정된 방향에 따라 다음 위치를 확인합니다.  

~~~java
package com.solid.srp.sample01;
public class NextPositionResolver {
    public Position resolve(Position position,Direction direction) {
        switch (direction) {
            case N:
                return new Position(position.getxAxis(),position.getyAxis()+1);
            case S:
                return new Position(position.getxAxis(),position.getyAxis()-1);
            case W:
                return new Position(position.getxAxis()-1,position.getyAxis());
            case E:
                return new Position(position.getxAxis()+1,position.getyAxis());
            default:
                throw new IllegalArgumentException();
        }
    }
}
~~~

PositionRepairer 클래스는 x 또는 y 값이 잘못된 경우 위치를 수정합니다.  

~~~java
package com.solid.srp.sample01;
public class PositionRepairer {
    public Position fix(Position position) {
        return new Position(
                position.getxAxis()<0?0:position.getxAxis(),
                position.getyAxis()<0?0:position.getyAxis()
        );
    }
}
~~~

Navigator 클래스는 탐색을 제대로 수행하기 위해 NextPositionResolver 및 PositionRepairer 클래스를 종속성으로 갖습니다.

~~~java
package com.solid.srp.sample01;
public class Navigator {
    private NextPositionResolver nextPositionResolver;
    private PositionRepairer positionRepairer;
    public Navigator(NextPositionResolver nextStepResolver,PositionRepairer positionRepairer) {
        this.nextPositionResolver = nextStepResolver;
        this.positionRepairer = positionRepairer;
    }
    public Position navigate(Position position, Direction direction) {
        Position nextPosition =  nextPositionResolver.resolve(position,direction);
        Position fixedPosition = positionRepairer.fix(nextPosition);
        return fixedPosition;
    }
}
~~~

[출처](https://dzone.com/articles/solid-principles-single-responsibility-principle)  
