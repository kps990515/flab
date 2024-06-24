# ObjectMApper

 - Jackson 라이브러리의 ObjectMapper를 설정
```java
@Configuration
public class ObjectMapperConfig {
    
    // ObjectMapper를 빈으로 등록하는 메서드
    @Bean
    public ObjectMapper objectMapper(){
        var objectMapper = new ObjectMapper();

        // registerModule : ObjectMapper에 모듈을 등록하여 적용할 범위 설정
        objectMapper.registerModule(new Jdk8Module()); // Java 8 버전 이후 클래스 지원
        objectMapper.registerModule(new JavaTimeModule()); // LocalDate, LocalDateTime 등 Java Time API 클래스 지원

        // 모르는 JSON 필드를 무시하도록 설정
        objectMapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false); 

        // 빈 객체에 대한 직렬화 실패를 무시하도록 설정
        objectMapper.configure(SerializationFeature.FAIL_ON_EMPTY_BEANS, false); 

        // 날짜를 타임스탬프로 직렬화하는 기능 비활성화
        objectMapper.disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS); 

        // 프로퍼티 네이밍 전략을 스네이크 케이스로 설정
        objectMapper.setPropertyNamingStrategy(new PropertyNamingStrategies.SnakeCaseStrategy()); 

        return objectMapper;
    }
}
```

```java
public class ObjectConvertUtil {

    // 싱글톤 인스턴스를 생성
    private static final ObjectConvertUtil INSTANCE = new ObjectConvertUtil();
    
    // Jackson ObjectMapper 인스턴스
    private final ObjectMapper objectMapper;

    // private 생성자: 외부에서 인스턴스 생성 불가
    private ObjectConvertUtil() {
        objectMapper = new ObjectMapper();
        
        // Java 8 기능 (Optional 등) 지원 모듈 등록
        objectMapper.registerModule(new Jdk8Module());
        
        // Java Time API (LocalDate 등) 지원 모듈 등록
        objectMapper.registerModule(new JavaTimeModule());
        
        // 알 수 없는 JSON 필드를 무시하도록 설정
        objectMapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);
        
        // 빈 객체의 직렬화 시 실패를 무시하도록 설정
        objectMapper.configure(SerializationFeature.FAIL_ON_EMPTY_BEANS, false);
        
        // 날짜를 타임스탬프로 직렬화하는 기능 비활성화
        objectMapper.disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS);
        
        // 프로퍼티 네이밍 전략을 스네이크 케이스로 설정
        objectMapper.setPropertyNamingStrategy(new PropertyNamingStrategies.SnakeCaseStrategy());
    }

    // 싱글톤 인스턴스를 반환하는 메서드
    public static ObjectConvertUtil getInstance() {
        return INSTANCE;
    }

    // 객체를 다른 타입의 객체로 변환하는 메서드
    public <T> T copyVO(Object fromValue, Class<T> toValueType) {
        return objectMapper.convertValue(fromValue, toValueType);
    }
}
```

