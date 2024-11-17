## ObjectMapper 직렬화 역직렬화
- 직렬화 : 객체를 바이트 스트림으로 변환하는 과정
- 역직렬화 : 바이트 스트림으로 변환된 데이터를 다시 객체로 복원하는 과정

| **장점**               | **설명**                                                                 |
|------------------------|-------------------------------------------------------------------------|
| **가독성**             | 간단하고 직관적인 구조로 사람이 쉽게 이해할 수 있음                     |
| **경량화**             | XML보다 데이터 크기가 작아 네트워크와 저장 공간 효율적 사용 가능         |
| **플랫폼 독립성**      | 텍스트 기반으로 언어와 플랫폼에 종속되지 않음                           |
| **데이터 교환 표준**    | RESTful API, 모바일 애플리케이션 등에서 데이터 교환에 최적화            |
| **구조화 지원**        | 객체와 배열을 통한 계층적 데이터 표현 가능                              |
| **처리 속도**          | 간단한 문법 덕분에 빠른 파싱 가능                                       |
| **광범위한 지원**       | 대부분의 언어와 라이브러리에서 기본 지원                                |


```java
class ChapterFirstApplicationTests {
	@Autowired
	private ObjectMapper objectMapper;
	/*
	Object Mapper(Jackson Library)
	스프링부트에서 Json -> 역직렬화 -> DTO / DTO -> 직렬화 -> Json
 	*/
	@Test
	void contextLoads() throws JsonProcessingException {
		var bookRequest = new BookRequest();
		bookRequest.setNumber("1");
		bookRequest.setName("adf");

		// 직렬화
		// bookRequest에 있는 get메소드(getName이면 name으로 변환)를 통해 json으로 직렬화함
		// 직렬화 원하지 않는 get메소드가 있다면 @JsonIgnore 붙이기
		var json = objectMapper.writeValueAsString(bookRequest);
		System.out.println(json);

		// 역직렬화
		// set, get메소드를 활용해서 역직렬화
		var dto = objectMapper.readValue(json, BookRequest.class);
		System.out.println(dto);
	}
}
```
