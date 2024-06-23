## yaml파일
# `application.yaml` 설정 분석

## Spring JPA 설정
```yaml
spring:
  jpa:
    show-sql: true 
    properties:
      format_sql: true 
      dialect: org.hibernate.dialect.MySQLDialect
    hibernate:
      ddl-auto: validate
```
- show-sql: SQL 쿼리를 출력합니다.
- properties.format_sql: 출력된 SQL 쿼리를 포맷팅합니다.
- properties.dialect: Hibernate에서 사용할 MySQL 방언을 설정합니다.
- hibernate.ddl-auto: 데이터베이스 테이블의 스키마를 어떻게 처리할지 설정합니다. validate는 엔티티와 테이블의 일치 여부를 검증합니다.

```yaml
spring:
  datasource:
    primary:
      url: jdbc:mysql://localhost:3306/toy_project?useSSL=false&useUnicode=true&allowPublicKeyRetrieval=true
      driver-class-name: com.mysql.cj.jdbc.Driver
      username: root
      password: root1234!!
    secondary:
      url: jdbc:mysql://localhost:3306/toy_project?useSSL=false&useUnicode=true&allowPublicKeyRetrieval=true
      driver-class-name: com.mysql.cj.jdbc.Driver
      username: root
      password: root1234!!
```

```yaml
spring:
  lifecycle:
    timeout-per-shutdown-phase: 30s
```

```yaml
spring:
  cache:
    type: redis 
  redis:
    host: localhost
    port: 6379  
```
 - 캐시를 redis로 사용

```yaml
server:
  shutdown: graceful
  servlet:
    encoding:
      force-response: true
```
- server.shutdown: Graceful shutdown 설정.
- servlet.encoding.force-response: 응답 시 인코딩을 강제 설정.
- graceful Shutdown : Graceful shutdown이 진행되면 더이상 요청은 거부 / 처리 중인 요청이 있다면 마무리하고 server를 종료

```yaml
token:
  secretKey: "SpringBootJWTHelperTokenSecretKeyValue123!@#"
  accessTokenExpiryHours: 1
  refreshTokenExpiryHours: 12
```

```yaml
resilience4j:
  circuitbreaker:
    configs:
      default:
        registerHealthIndicator: true
        slidingWindowSize: 100
        minimumNumberOfCalls: 10
        permittedNumberOfCallsInHalfOpenState: 3
        waitDurationInOpenState: 10000
        failureRateThreshold: 50
        eventConsumerBufferSize: 10
    instances:
      reservationService:
        baseConfig: default
      redisService:
        baseConfig: default
```
- resilience4j.circuitbreaker.configs.default: 기본 회로 차단기 설정.
  - registerHealthIndicator: 헬스 인디케이터 등록 여부.
  - slidingWindowSize: 슬라이딩 윈도우 크기.
  - minimumNumberOfCalls: 최소 호출 수.
  - permittedNumberOfCallsInHalfOpenState: Half-open 상태에서 허용되는 호출 수.
  - waitDurationInOpenState: Open 상태에서 대기 시간 (밀리초).
  - failureRateThreshold: 실패율 임계값.
  - eventConsumerBufferSize: 이벤트 소비자 버퍼 크기.
- resilience4j.circuitbreaker.instances: 각 서비스의 회로 차단기 인스턴스 설정.
  - reservationService: 예약 서비스 설정.
  - redisService: Redis 서비스 설정.

