## Project Setting

### 멀티모듈
```java
// service 모듈 하위에 api, db 모듈 생성 및 연결
rootProject.name = 'service'
include 'api'
include 'db'
```

### 모듈에 다른 모듈 dependency 추가
```java
// api build.gradle에 db모듈을 의존성 추가
dependencies {
    implementation project(':db') 
    implementation 'org.springframework.boot:spring-boot-starter-validation'
    implementation 'org.springframework.boot:spring-boot-starter-web'
}
```

### jar옵션 설정
```java
// service build.gradle
bootJar{ //spring boot Jar(실행용) 옵션 끄기
	enabled = false
}

jar { // java Jar 파일(외부참고용) 생성 옵션 끄기
	enabled = false
}

// api build.gradle
bootJar{ // api를 실행시킬거기 때문에 켜기
	enabled = true
}

jar { // java Jar 파일 생성 옵션 끄기
	enabled = false
}

// db build.gradle
bootJar{ // 실행안함으로 끄기
	enabled = false
}

jar { // api에서 사용할거기 때문에 켜기
	enabled = true
}
```

### Service Jpa Config
```java
@Configuration
@EntityScan(basePackages = "org.delivery.db")
@EnableJpaRepositories(basePackages = "org.delivery.db")
public class JpaConfig {
    // 스프링에서 bean은 같은 패키지만 등록하기때문에
    // db 패키지에 있는 Entity와 repository도 bean으로 등록하라고 세팅
}
```

### ObjectMapper Config
```java
@Configuration
public class ObjectMapperConfig {
    @Bean
    public ObjectMapper objectMapper(){
        var objectMapper = new ObjectMapper();

        // registerModule : objectMapper에 적용할 범위 세팅
        objectMapper.registerModule(new Jdk8Module()); // java8버전 이후 클래스
        objectMapper.registerModule(new JavaTimeModule()); // local date


        objectMapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false); // 모르는 json field는 무시
        objectMapper.configure(SerializationFeature.FAIL_ON_EMPTY_BEANS, false); // 모르는 객체 컬럼은 무시
        objectMapper.disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS); // 날짜 관련 직렬화
        objectMapper.setPropertyNamingStrategy(new PropertyNamingStrategies.SnakeCaseStrategy()); //스네이크 케이스

        return objectMapper;
    }
}
```

### Swagger Config
```java
@Configuration
public class SwaggerConfig {
    @Bean
    public ModelResolver modelResolver(ObjectMapper objectMapper){
        // swagger형태를 내가 만든 objectMapper형태로 변환
        return new ModelResolver(objectMapper);
    }
}
```