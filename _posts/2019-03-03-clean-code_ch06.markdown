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

<br>
##### Reference
- *CleanCode 애자일 소프트웨어 장인 정신*
