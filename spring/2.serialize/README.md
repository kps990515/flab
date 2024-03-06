## ObjectMapper 직렬화 역직렬화

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
