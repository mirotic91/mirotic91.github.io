---
layout: post
title: "Ch06. 객체와 자료구조"
subtitle: 객체와 자료구조를 적절히 사용하자
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

#### 디미터 법칙
모듈은 자신이 조작하는 객체의 속사정을 몰라야 한다는 법칙이다.

##### 기차 충돌
- 일반적으로 조잡하다 여겨지는 방식이므로 피하는 편이 좋다.
  - ex) final String outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();
- 객체라면 내부 구조를 숨겨야 하므로 확실히 디미터 법칙을 위반한다. 반면,  
자료 구조라면 당연히 내부 구조를 노출하므로 디미터 법칙이 적용되지 않는다.

##### 잡종 구조
- 때때로 절반은 객체, 절반은 자료 구조인 잡종 구조가 나온다.  
- 잡종 구조는 중요한 기능을 수행하는 함수도 있고, 공개 변수나 공개 조회/설정 함수도 있다.  
- 새로운 함수는 물론이고 새로운 자료 구조도 추가하기 어렵다. 그러므로 되도록 피하는 편이 좋다.

##### 구조체 감추기
- 객체라면 내부 구조를 감춰야 하므로 메서드 체이닝 방식은 안 된다.

```java
# before
String outFile = outputDir + "/" + className.replace('.', '/') + ".class";
FileOutputStream fout = new FileOutputStream(outFile);
BuffredOutputStream bos = new BufferedOutputStream(fout);

# after
// ctxt 객체에 임시 파일을 생성하라고 시킨다.
BufferedOutputStream bos = ctxt.createScratchFileStream(classFileName);
```

#### 자료 전달 객체 `DTO`
- 자료 구조체의 전형적인 형태는 공개 변수만 있고 함수가 없는 클래스다.
- DB에 저장된 가공되지 않은 정보를 애플리케이션 코드에서 사용할 객체로 변환하는 일련의 단계에서 가장 처음으로 사용하는 구조체다.

##### 활성 레코드
- DTO의 특수한 형태로 데이터베이스 테이블이나 다른 소스에서 자료를 직접 변환한 결과다.
- 활성 레코드는 자료 구조로 취급한다. 비즈니스 규칙을 담으면서 내부 자료를 숨기는 객체는 따로 생성한다.

#### 결론
새로운 `자료 타입`이 필요한 경우에는 클래스와 객체 지향 기법이 가장 적합하다.  
새로운 `함수`가 필요한 경우에는 절차적인 코드와 자료 구조가 좀 더 적합하다.  
우수한 개발자는 편견없이 이 사실을 이해해 직면한 문제에 **최적인 해결책**을 선택한다.

<br>
##### Reference
- *CleanCode 애자일 소프트웨어 장인 정신*
