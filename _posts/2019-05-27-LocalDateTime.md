---
layout: post
title: "java.time 패키지의 LocalDateTime"
subtitle: 다양한 API를 제공하는 Java8의 time 패키지를 사용하자
date: 2019-05-27 23:20:00 +0900
categories: Java
tags: Java
---

## 다양한 API를 제공하는 Java8의 time 패키지를 사용하자
---

Java8 에서 이전의 *java.util* 패키지의 `Date`, `Calendar` 클래스의 단점을 해결하기 위해 날짜와 시간에 대한 새로운 API를 도입했다.
Java8 부터 지원하는 *java.time* 패키지의 `LocalDate` , `LocalTime`, `LocalDateTime`, `ZonedDateTime`, `Period`, `Duration` 주요 클래스는 유연한 메서드들을 제공한다.

#### 개선된 내용
* 불변이고 스레드 안정적이다.
* 날짜, 시간, 기간 등 광범위한 유틸 메서드를 지원한다.
* 표준시간대를 별도로 적용할 필요없는 *ZoneDateTime* 을 제공한다.

---

### LocalDate
시간 표현 없이 기본 ISO 포맷으로 **날짜**만 표현한다. ex) 2012-03-02

#### 1. 생성
생성자가 아닌 static 정적 메서드를 제공한다.
```java
// 2019-05-27
LocalDate.now(); // System current date
LocalDate.parse("2019-05-27");
LocalDate.of(2019, 5, 27);

```

#### 2. 조회
날짜에 대한 다양한 정보를 제공한다.
```java
LocalDate localDate = LocalDate.now(); // 2019-05-27
localDate.getYear(); // 2019
localDate.getMonth(); // MAY
localDate.getMonthValue(); // 5
localDate.getDayOfMonth(); // 27
localDate.getDayOfWeek(); // Monday
localDate.isLeapYear(); // false (윤년여부)

```

#### 3. 조작
날짜 변경에 대한 여러 메서드를 제공한다.
```java
// 이 달의 첫 날을 구하는 여러가지 방식
LocalDate localDate = LocalDate.now();
LocalDate firstDayOfMonth1 = localDate.with(ChronoField.DAY_OF_MONTH, 1); // TemporalField
LocalDate firstDayOfMonth2 = localDate.with(TemporalAdjusters.firstDayOfMonth()); // TemporalAdjusters
LocalDate firstDayOfMonth3 = localDate.withDayOfMonth(1); // int
```

날짜를 특정 단위로 더하고 빼는 메서드를 제공한다.
```java
LocalDate localDate = LocalDate.now();
LocalDate previousMonthSameDay = localDate.minus(1, ChronoUnit.MONTHS);
LocalDate tomorrowDay = localDate.plus(1, ChronoUnit.DAYS);

```

#### 4. 비교
날짜간의 순서를 비교하는 메서드를 제공한다. 
```java
boolean notBefore = LocalDate.parse("2016-06-12")
  .isBefore(LocalDate.parse("2016-06-11"));
 
boolean isAfter = LocalDate.parse("2016-06-12")
  .isAfter(LocalDate.parse("2016-06-11"));

boolean isEqual = LocalDate.parse("2016-06-12")
  .isEqual(LocalDate.parse("2016-06-12"));

```

---

### LocalTime
날짜 표현 없이 **시간**만 표현한다. ex) 13:30:31.100300

#### 1. 생성
생성자가 아닌 static 정적 메서드를 제공한다.
```java
LocalTime.now(); // System current time
LocalTime.of(22, 37, 45);
LocalTime.parse("22:37:45");

```

#### 2. 조회
시간에 대한 정보를 제공한다.

```java
LocalTime localTime = LocalTime.now(); // 22:37:45.990050
localTime.getHour(); // 22
localTime.getMinute(); // 37
localTime.getSecond(); // 45
localTime.getNano(); // 990050000

```

#### 3. 조작
시간 변경에 대한 여러 메서드를 제공한다.
```java
LocalTime localTime = LocalTime.now();
localTime.with(ChronoField.HOUR_OF_DAY, 13); // TemporalField
localTime.withHour(13); // int
```

시간을 특정 단위로 더하고 빼는 메서드를 제공한다.
```java
// 06:30:30
LocalTime localTime1 = LocalTime.parse("05:30:30").plus(1, ChronoUnit.HOURS);
LocalTime localTime2 = LocalTime.parse("06:40:30").minusMinutes(10);
LocalTime localTime3 = LocalTime.parse("06:30:10").plusSeconds(20);

```

#### 4. 비교
시간의 순서를 비교하는 메서드를 제공한다. 
```java
boolean notBefore = LocalTime.parse("22:22:22")
  .isBefore(LocalTime.parse("11:11:11"));
 
boolean isAfter = LocalTime.parse("22:22:22")
  .isAfter(LocalTime.parse("11:11:11"));

boolean isEqual = LocalTime.parse("22:22:22")
  .isEqual(LocalTime.parse("22:22:22"));

```

---

### LocalDateTime (LocalDate + LocalTime)
**날짜와 시간**을 함께 표현한다. ex) 2015-02-20T06:30:00

#### 1. 생성
생성자가 아닌 static 정적 메서드를 제공한다.
```java
LocalDateTime.now(); // System current date time
LocalDateTime.of(2015, Month.FEBRUARY, 20, 06, 30);
LocalDateTime.parse("2015-02-20T06:30:00");

```

#### 2. 조회
날짜와 시간에 대한 정보를 제공한다.

```java
LocalDateTime localDateTime = LocalDateTime.now(); // 2015-02-20T06:30:00
localDateTime.getYear(); // 2015
localDateTime.getMonthValue(); // 2
localDateTime.getDayOfMonth(); // 20
localDateTime.getDayOfWeek(); // FRIDAY
localDateTime.getHour(); // 6
localDateTime.getMinute(); // 30
localDateTime.getSecond(); // 0

```

#### 3. 조작
날짜와 시간 변경에 대한 여러 메서드를 제공한다.
```java
LocalDateTime localDateTime = LocalDateTime.now();
localDateTime.with(ChronoField.HOUR_OF_DAY, 12); // TemporalField
localDateTime.with(TemporalAdjusters.lastDayOfMonth()); // TemporalAdjusters
localDateTime.withYear(2019); // int
localDateTime.withHour(23); // int
```

날짜와 시간을 특정 단위로 더하고 빼는 메서드를 제공한다.
```java
// 2015-02-20T06:30:00
LocalDateTime localDateTime1 = LocalDateTime.parse("2015-02-20T05:30:00").plus(1, ChronoUnit.HOURS);
LocalDateTime localDateTime2 = LocalDateTime.parse("2015-02-10T06:30:00").plusDay(10);
LocalDateTime localDateTime3 = LocalDateTime.parse("2015-02-20T08:30:00").minusHours(2);

```

#### 4. 비교
날짜와 시간의 순서를 비교하는 메서드를 제공한다. 
```java
boolean notBefore = LocalDateTime.parse("2015-02-20T06:30:00")
  .isBefore(LocalDateTime.parse("2002-02-20T06:30:00"));
 
boolean isAfter = LocalDateTime.parse("2015-02-20T16:30:00")
  .isAfter(LocalDateTime.parse("2015-02-20T06:30:00"));

boolean isEqual = LocalDateTime.parse("2015-02-20T06:30:00")
  .isEqual(LocalDateTime.parse("2015-02-20T06:30:00"));

```
     
<br>
##### Reference
- *[baeldung](https://www.baeldung.com/java-8-date-time-intro)*