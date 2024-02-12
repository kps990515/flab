## 02/11

### [OAuth](https://blog.naver.com/mds_datasecurity/222182943542)
- OAuth(openAuthorization) 등장배경
앱에 페이스북, 구글 정보를 가져오기위해 ID,PASSWORD를 받아오는것을 위험하기 때문

- OAuth란
다양한 플랫폼(구글, 페이스북 등)의 사용자 데이터 접근 권한을 위임 받는 프로토콜

- 주체
    1. 리소스 소유자 또는 사용자
        - 구글, 페이스북, 나
    2. 인증 서버
        - 리소스 소유자를 인증하고 Client에게 액세스 토큰 발급
    3. 리소스 서버 : 구글, 페이스북 서버
    4. Client(Application) : 우리의 서비스

 - 주요용어
    1. Authentication : 인증, 접근 자격 검증
    2. Authorization : 자원에 접근할 권한 부여(Access token부여)
    3. Access Token : 자원에 접근할때 사용하는 Token
    4. Refresh Token : Access Token만료 시 갱신하는 Token

 - 권한부여 방식
    1. Authorization Code Grant
     - 기본으로 쓰이는 방식(간편 로그인)
     - Client가 사용자 대신 특정 자원 접근 요청
     - Refresh Token 사용가능

    ![img](https://postfiles.pstatic.net/MjAyMzAyMDNfMTA5/MDAxNjc1Mzg2ODIyMTk2.p2TqHr3sSdI5wcVsE01Hn95R-LpblY6qk1-TJ8dmz2cg.opYLjtRDwQu1F4-9SwreEr_df0fc-b1bgCZo2iyZRTIg.JPEG.mds_datasecurity/000.jpg?type=w966)


