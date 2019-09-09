---
layout: post
title: "private 메서드 Test Case 작성"
subtitle: private 메서드 test를 위한 두 가지 방법
date: 2019-09-09 23:15:00 +0900
categories: Test
tags: Test private
---

테스트 코드를 작성하고 있으나 **private 메서드**에 대한 테스트는 접근할 수 없다는 이유로 작성하지 않고 있었다.
다른 public 메서드에 종속되어 사용되더라도 공통적으로 사용될 수 있기에 테스트가 필요하다.

이에 두 가지 방법을 소개한다.

- `Reflection`을 활용한 유틸 클래스 테스트 코드 작성
    - 유틸 클래스의 경우, 일반적으로 **객체 생성과 상속이 불가**하다.

```java
public final class MiroticUtil {

  private MiroticUtil() {}

  public static int publicSum(int x, int y) {
    return privateSum(x, y);
  }

  private static int privateSum(int x, int y) {
    return x + y;
  }
}

public class MiroticUtilTest {

  @Test
  public void publicSum() {
    int result = MiroticUtil.publicSum(1, 2);
    assertEquals(result, 3);
  }

  @Test
  public void privateSum() throws Exception {
    Method method = MiroticUtil.class.getDeclaredMethod("privateSum", int.class, int.class);
    method.setAccessible(true); // 접근 허용

    int result = (int) method.invoke(method, 1, 2);
    assertEquals(result, 3);
  }
}

```

- Spring에서 지원하는 `ReflectionTestUtils`를 활용한 테스트 코드 작성 

```java
@Component
public class Mirotic {

  public int publicSum(int x, int y) {
    return privateSum(x, y);
  }

  private int privateSum(int x, int y) {
    return x + y;
  }
}

@RunWith(SpringRunner.class)
@SpringBootTest(classes = Mirotic.class)
public class MiroticTest {

  @Autowired
  private Mirotic mirotic;

  @Test
  public void publicSum() {
    int result = mirotic.publicSum(1, 2);
    assertEquals(result, 3);
  }

  @Test
  public void privateSum() throws Exception {
    int result = ReflectionTestUtils.invokeMethod(mirotic, "privateSum", 1, 2);
    assertEquals(result, 3);
  }
}

```

### 정리
- `private` 접근자 메서드 테스트의 경우, 외부 클래스에서 접근할 수 없으므로 `Reflection`을 사용해 접근이 가능하도록 접근을 허용해줘야 한다.
- 메서드명을 type-safe하게 작성할 수 없다는 단점이 있다.

<br>
##### Reference
- *[Blog](https://hyunto.github.io/2019/03/13/springframework-testing-private-method)*