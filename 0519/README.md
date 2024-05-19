## 05/19

### [CORS](https://escapefromcoding.tistory.com/724)
- Cross Origin Resource Sharing
- 출처가 다른 자원들을 공유
- 출처 : Protocol + Host + port 
  - https:// : 프로토콜
  - github.com : 호스트
  - 8801 : 포트

- 사용이유 : 한 출처에서 다른 출처 접근을 제어하기 위해서
- CORS 정책 3가지
  1. 단순요청
    - GET, HEAD, POST 요청만 가능
    - CORS 안전 리스트 헤더 혹은 User-Agent 헤더만 가능
    - Contet-Type 헤더는 application/x-www-form-urlencoded, multipart/form-data and text/plain만 가능

  2. 프리플라이트
    - OPTIONS 메서드로 HTTP 요청을 미리 보내 안전한지 확인
    - 요청헤더
      - origin : 어디서 요청을 했는지 서버에 알려주는 주소
      - access-control-request-method : 실제 요청이 보낼 HTTP 메서드
      - access-control-request-headers : 실제 요청에 포함된 header
    - 응답헤더
      - access-control-allow-origin : 서버가 허용하는 출처
      - access-control-allow-methods : 서버가 허용하는 HTTP 메서드 리스트
      - access-control-allow-headers : 서버가 허용하는 header 리스트
      - access-control-max-age : 프리 플라이트 요청의 응답을 캐시에 저장하는 시간    
  
  3. 신용요청
    - 쿠키, 인증 헤더, TLS 클라이언트 인증서 등의 신용정보와 함께 요청

### [HashMap](https://lordofkangs.tistory.com/78)
- 자료구조로 배열(array)을 사용
- 해싱(Hashing)을 통해 Map 데이터가 저장 될 위치의 인덱스를 구한다.
- value 인덱스 = hash(key)
- Map 데이터를 Node객체(hash, key, value, next)로 만들어서 관리
- Node배열을 Table이라는 이름으로 관리

- 제약조건
  1. hasing의 결과는 정수
  2. hasing의 결과는 배열의 크기를 넘어서면 안 된다.

- 시간복잡도 
  - key 인덱스 : O(1)
  - LinkedList : O(n)
  - Red-Black Tree : O(log n)

- 키의 충돌 해결
  1. threshold : 테이블의 크기를 늘릴 임계점 설정
  2. Red-Black Tree : LinkedList 방식으로 충돌된 객체들을 관리하다가, 임계점을 넘으면 Red-Black Tree 방식으로 객체들을 저장

### [Persist VS Merge](https://umanking.github.io/2019/04/12/jpa-persist-merge/)
- persist : 새로운 엔티티를 영속성 컨텍스트(Persistence Context)에 추가하고 데이터베이스에 저장
- merge : 준영속 상태(detached state)나 새로운 엔티티를 영속성 컨텍스트에 병합하여 데이터베이스에 저장하거나 업데이트
  - persist -> detach -> persist상태

### [JPA Save시 select발생](https://chatgpt.com/c/2a75afd0-1a4f-4cbb-8a91-7f7b34a74fb8)
- ID가 AutoIncrement가 아닌경우 해당 ID가 중복인지 확인하기 위해 select발생
- save 함수
  - 새로운 객체 isNew(entity) = true 이면 persist
  - 아니면 merge
- 해결방법
  - isNew이 true가 되는 기준을 id 존재 여부가 아닌 아래조건으로 변경
    - 영속 상태가 되기 전
    - 영속성 컨텍스트에서 조회 안되었을떄
- 결론 : DB에서 ID중복을 확인하지 않고(select안하고) 바로 insert가능

```java
@Entity
@Table(name = "reserved_seat")
public class ReservedSeat implements Persistable<String> { // Persistable 인터페이스를 구현
	@Id
	@Column(name = "id")
	private String id;

	...

	@Transient
	private boolean isNew = true;

	protected ReservedSeat() {
	}

	@Override // // Persistable 인터페이스를 오버라이딩
	public boolean isNew() {
		return isNew;
	}

	@PrePersist // 영속상태가 되기 직전 false로 바꿔줌
	@PostLoad // 영속성 컨텍스트에서 조회가 되면 false로 바꿔줌
	void markNotNew() {
		this.isNew = false;
	}

	@Override
	public String getId() {
		return this.id;
	}
    
    ...
}
```