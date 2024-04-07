## 04/07

###[uuid, Autoincrement](https://www.percona.com/blog/store-uuid-optimized-way/)
 - uuid의 문제점
  1. 36글자로 용량이 큼
  2. 정렬이 안됨
  3. 데이터가 무작위로 분산됨

 - Ordered UUID
  - UUID를 생성할 때 시간 정보를 포함하여 정렬 가능하게 만든 것
  - 정렬 가능
  - 데이터 순차적 저장

### [HTTP 상태 코드](https://developer.mozilla.org/ko/docs/Web/HTTP/Status)
1. 정보 응답 (100-199)
 - 100 Continue: 클라이언트가 나머지 요청을 계속해도 좋음
 - 101 Switching Protocols: 서버가 클라이언트가 요청한 프로토콜 전환을 승인

2. 성공 응답 (200-299)
 - 200 OK: 요청이 성공적으로 처리
 - 201 Created: 요청이 성공적으로 이루어져 새로운 리소스가 생성
 - 204 No Content: 요청은 성공적이지만, 보낼 컨텐츠가 없음

3. 리다이렉션 메시지 (300-399)
 - 301 Moved Permanently: 요청된 리소스가 영구적으로 새 위치로 이동
 - 302 Found: 요청된 리소스가 일시적으로 다른 URI로 이동했
 - 304 Not Modified: 클라이언트가 조건부 GET 요청을 했고, 문서가 변경되지 않았다면 서버는 이 코드를 반환

4. 클라이언트 에러 응답 (400-499)
 - 400 Bad Request: 서버가 요청을 이해할 수 없음
 - 401 Unauthorized: 미승인 이지만 비인증을 의미
 - 403 Forbidden: 컨텐츠 접근 권한 없음
 - 404 Not Found: 서버가 요청한 페이지를 찾을 수 없음

5. 서버 에러 응답 (500-599)
 - 500 Internal Server Error: 서버가 요청을 처리할 수 없어 발생한 오류
 - 501 Not Implemented: 서버가 요청 메소드를 지원하지 않음
 - 503 Service Unavailable: 서버가 일시적으로 요청을 처리할 준비가 되어 있지 않음

### [HTTP 메소드](https://inpa.tistory.com/entry/WEB-%F0%9F%8C%90-HTTP-%EB%A9%94%EC%84%9C%EB%93%9C-%EC%A2%85%EB%A5%98-%ED%86%B5%EC%8B%A0-%EA%B3%BC%EC%A0%95-%F0%9F%92%AF-%EC%B4%9D%EC%A0%95%EB%A6%AC)

![img](https://core-cdn-fe.toss.im/image/optimize/?src=https://static.toss-internal.com/ipd-tcs/toss_core/live/59474144-e457-4281-80ee-a14e2f06f5a9?&w=1920&q=75)
#### 멱등성
  - 첫 번째 수행을 한 뒤 여러 차례 적용해도 결과를 변경시키지 않는 작업 또는 기능

1. GET
  - 리소스 조회 메서드 (Read)
  - 캐싱 사용가능
  - 200 OK return

2. POST
  - 전달한 데이터 처리/생성 요청 메서드 (Create)
  - 메시지 바디(body)를 통해 서버로 요청 데이터 전달
  - 200, 201 return

3. PUT
  - 리소스를 대체(수정)하는 메서드 (Update)
  - 리소스가 있으면 덮어쓰고 없으면 생성

4. PATCH
  - 리소스를 일부만 수정하는 메소드 (Update)

5. Delete
  - 리소스 제거하는 메소드 (Delete)
  - return 200, 204

6. HEAD
  - GET과 동일하지만 HTTP헤더정보만을 가져옴

7. Trace
  - 서버에 도달 했을 때의 최종 패킷의 요청 패킷 내용을 응답(검사용)
  - 200 return

8. Option
  - 웹 서버에서 지원하는 메소드의 종류를 조회
  - CORS(Cross-Origin Resource Sharing)에서 사전 요청(preflight request)으로 사용

9. Connect
  - 원하는 목적지와 TCP연결을 HTTP 프록시 서버에 요청
  - SSL(HTTPS)를 사용하는 웹 사이트 접속하는데 사용

