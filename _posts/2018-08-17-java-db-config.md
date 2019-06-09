---
layout: post
title: "DatabaseConfiguration 적용"
subtitle: Database 로 프로퍼티 관리하기
date: 2018-08-17 12:00:00 +0900
categories: Java
tags: DatabaseConfiguration
---

## PropertiesConfiguration
- 서비스에 사용되는 설정 값들을 실시간으로 변경할 수 있다.
- 서버가 적을 경우에는 각 서버마다 설정을 변경하는 것이 크게 번거롭지 않지만 서버가 많을 경우에는 모든 서버의 설정을 변경하는 것은 곤혹스럽다.
- `AWS Auto Scaling` 을 사용하고 있다면 서버 사용량에 따라 미리 만들어 놓은 이미지로 서버가 늘고 줄게된다.
해당 서버 이미지의 설정값은 변경되지 않은 기존 이미지의 설정값을 따르기 때문에 스케일링 서버들은 다르게 동작하게 된다.
그렇기 때문에 설정값을 각각 수정하더라도 이미지를 다시 만들어야하는 단점이 있다.

## DatabaseConfiguration
- 모든 서버의 설정값을 동일하게 가져간다면 DB로 편히 관리할 수 있다.
- 서버마다 설정을 다르게 가져가야 하는 경우는 예외다.
- `AWS Auto Scaling` 에 상관없이 DML 수행만으로 서버에 구애 받지 않고 실시간으로 설정값을 변경할 수 있다. 

---
## DatabaseConfiguration 적용
예제 코드를 통해 DatabaseConfiguration 적용해보자

### DatabaseConfiguration 빈 등록
```java
@Configuration
public class DatabaseConfig {

  @Bean
  public DataSource dataSource() {
    BasicDataSource dataSource = new BasicDataSource();
    dataSource.setDriverClassName("com.mysql.jdbc.Driver");
    dataSource.setUrl("jdbc:mysql://localhost/test");
    dataSource.setUsername("root");
    dataSource.setPassword("root");
    return dataSource;
  }

  @Bean
  public DatabaseConfiguration databaseConfig() {
    // table명, key/value명
    return new DatabaseConfiguration(dataSource(), "config", "key", "value");
  }

}

```

### config 테이블 정의 (JPA)
```java
@Entity
@Table(name = "config")
@Getter
@EqualsAndHashCode(callSuper = false, of = { "key" })
@NoArgsConstructor
public class Config {

  @Id
  @Column
  private String key;

  @Column
  private String value;

}
```

### 프로퍼티 값 설정
```java
@Component
public class ConfigUtil {

  @Autowired
  private DatabaseConfiguration dbConfig;

  // key, default value

  public int getRetryCount() {
    return dbConfig.getInteger("retry.count", 3);
  }

  public boolean isSendAsync() {
    return dbConfig.getBoolean("send.async", false);
  }

  public String getPaymentServerAddress() {
    return dbConfig.getString("payment.server.address", "www.pay.com");
  }

}

```

동작 방식 예시 *getRetryCount()* 
1. config 테이블에서 retry.count 라는 key가 존재하는지 확인한다.
2. 해당 key가 존재하면 retry.count 의 value를 반환한다.  
3. 해당 key가 존재하지 않으면 default 값으로 지정된 3을 반환한다.
4. 아래 쿼리를 수행한 후 다시 호출하면 해당 key에 변경된 값인 5를 반환한다.
```mysql
UPDATE config 
SET value = 5 
WHERE key = 'retry.count';
```

추가적으로 해당 설정값들의 변경될 가능성을 감안하여 캐시를 적용하면 DB를 조회하는 행위를 생략할 수 있다.