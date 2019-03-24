---
layout: post
title: "Ch08. 경계"
subtitle: 경계에 위치하는 코드를 깔끔하게 분리하자
date: 2019-03-21 23:53:33 +0900
categories: CleanCode
tags: CleanCode
---

# 경계
---

외부의 코드를 이용할 때 우리 코드에 깔끔하게 통합해야만 한다.

#### 외부 코드(Map) 사용하기
- Map 인스턴스를 여기저기로 넘긴다면, Map 인터페이스가 변할 경우, 수정할 코드가 상당히 많아진다.
- Map과 같은 경계 인터페이스를 이용할 때는 이를 이용하는 클래스나 클래스 계열 밖으로 노출되지 않도록 주의한다.  
Map 인스턴스를 공개 API의 인수로 넘기거나 반환값으로 사용하지 않는다.

#### 경계 살피고 익히기
- 외부 코드에 대한 테스트가 우리의 책임은 아니다. 다만 우리가 사용할 코드를 테스트하는 편이 바람직하다.
- `학습 테스트`란 간단한 테스트 케이스를 작성해 외부 코드를 익히는 방식이다.  
학습 테스트는 API의 사용 목적에 맞게 작성하여 이해를 돕는다.

#### 외부 코드(log4j) 익히기
> 로깅 기능을 직접 구현하는 대신 아파치의 log4j 패키지를 사용한다고 가정하자. -생략-  
대략적인 테스트 케이스를 작성한다. 테스트가 실패하면 레퍼런스를 보면서 테스트 케이스를 수정해 나간다.

#### 학습 테스트는 공짜 이상이다
- 필요한 지식만 확보하는 손쉬운 방법이다.
- 학습 테스트는 공짜 이상의 가치가 있다. 노력보다 얻는 성과가 더 크다.
- 외부 코드가 새 버전이 나온다면 학습 테스트를 돌려 차이가 있는지 확인한다.  
새 버전이 우리 코드와 호환되지 않으면 학습 테스트가 이 사실을 곧바로 밝혀낸다.
- 경계 테스트가 있다면 새 버전으로 이전하기 쉬워진다.

#### 아직 존재하지 않는 코드를 사용하기
- 경계와 관련해 또 다른 유형은 **아는 코드**와 **모르는 코드**를 분리하는 경계다.
- 우리가 바라는 인터페이스를 구현하면 *우리가 인터페이스를 전적으로 통제*한다는 장점이 생긴다. 
또한 코드 **가독성**도 높아지고 코드 **의도**도 분명해진다.

> 지정한 주파수를 이용해 이 스트림에서 들어오는 자료를 아날로그 신호로 전송하는 **송신기** 시스템을 예로 들자.

![Figure 8.2](/img/clean-code/figure-8.2.png)

```java
// 주파수와 스트림을 전송하는 인터페이스를 정의한다.
public interface Transimitter {
    void transmit(Integer frequency, String stream);
}

// 외부로부터 제공받을 실제 API
public class FutureTransimitter {
    void transmit(Integer frequency, String stream);
}

// 어댑터 패턴으로 API 사용 캡슐화. 수정 포인트 집결
public class TransmitterAdapter implements Transimitter {
    
    private FutureTransmitter futureTransmitter;
    
    public void transmit(Integer frequency, String stream) {
        futureTransmitter.transmit(frequency, stream);
    }
}

// 실제 API 제공 받기 전 테스트 용도로 생성
public class FakeTransmitter implements Transimitter {
    
    public void transmit(Integer frequency, String stream) {
        // ...
    }
}

public class CommunicationController {
    
    // 실제 API 제공받기 전 사용 사례
    public void transmit() {
        Transmitter transmitter = new FakeTransmitter();
        transmitter.transmit(someFrequency, someStream);
    }

    // 실제 API 제공받은 후 사용 사례
    public void transmit() {
        Transmitter transmitter = new TransmitterAdapter();
        transmitter.transmit(someFrequency, someStream);
    }
}
```

#### 깨끗한 경계
- **경계**에 위치하는 코드는 깔끔히 **분리**한다.
- 외부 코드에 의존하는 대신 통제가 가능한 우리 코드에 의존하는 편이 훨씬 좋다.
- 새로운 클래스로 경계를 감싸거나 아니면 `어댑터 패턴`을 사용해 우리가 원하는 인터페이스를 외부 코드가 제공하는 인터페이스로 변환하자.  
어느 방법이든 코드 **가독성**이 높아지며, 경계 인터페이스를 사용하는 일관성도 높아지며, 외부 코드가 변했을 때 변경할 코드도 줄어든다.

<br>
##### Reference
- *CleanCode 애자일 소프트웨어 장인 정신*
