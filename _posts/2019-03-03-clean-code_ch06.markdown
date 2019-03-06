---
layout: post
title: "Ch06. 객체와 자료구조"
date: 2019-03-03 23:45:33 +0900
categories: CleanCode
tags: CleanCode
---

# 객체와 자료구조
---

#### 자료 추상화
- 추상 인터페이스를 제공해 사용자가 구현을 모른 채 자료의 핵심을 조작할 수 있어야 진정한 의미의 클래스다.
- 아무 생각 없이 조회/설정 함수를 추가하는 방법이 가장 나쁘다.

```java
# before
public class Point {
    public double x;
    public double y;
}

# after
public interface Point {
    double getX();
    double getY();
    void setCartesian(double x, double y);
}
```

#### 자료/객체 비대칭
- `객체`는 추상화 뒤로 자료를 숨긴 채 자료를 다루는 함수만 공개한다.
- `자료 구조`는 자료를 그대로 공개하며 별다른 함수는 제공하지 않는다.
- `절차적인 코드`는 기존 자료 구조를 변경하지 않으면서 새 함수를 추가하기 쉽다.  
그러나 새로운 자료 구조를 추가하기 어렵다. 그러려면 모든 함수를 고쳐야 한다.
- `객체지향 코드`는 기존 함수를 변경하지 않으면서 새 클래스를 추가하기 쉽다.  
그러나 새로운 함수를 추가하기 어렵다. 그러려면 모든 클래스를 고쳐야 한다.
> 새로운 **자료 타입**이 필요한 경우에는 클래스와 객체 지향 기법이 가장 적합하다.  
> 새로운 **함수**가 필요한 경우에는 절차적인 코드와 자료 구조가 좀 더 적합하다.

```java
/* 절차적인 도형 */

public class Square {
    
    public Point topLeft;
    
    public double side;
}

public class Rectangle {
    
    public Point topLeft;
    
    public double height;
    
    public double width;
}

public class Circle {
    
    public Point center;
    
    public double radius;
}

public class Geometry {

    public final double PI = 3.141592653589793;

    public double area(Object shape) throws NoSuchShapeException {
        if (shape instanceof Square) {
            Square s = (Square) shape;
            return s.side * s.side;
        } else if (shape instanceof Rectangle) {
            Rectangle r = (Rectangle) shape;
            return r.height * r.width;
        } else if (shape instanceof Circle) {
            Circle c = (Circle) shape;
            return PI * c.radius * c.radius;
        }
        throw new NoSuchShapeException();
    }
}

 /* 다형적인 도형 */

public class Square implements Shape {
    
    private Point topLeft;
    
    private double side;
    
    public double area() {
        return side * side;
    }
}

public class Rectangle implements Shape {
    
    private Point topLeft;
    
    private double height;
    
    private double width;
    
    public double area() {
        return height * width;
    }
}

public class Circle implements Shape {
    
    private Point center;
    
    private double radius;
    
    public final double PI = 3.141592653589793;
    
    public double area() {
        return PI * radius * radius;
    }
}
```

<br>
##### Reference
- *CleanCode 애자일 소프트웨어 장인 정신*
