## API 명세서

### USER

#### 로그인
**URL:** `/users/signin`  
**Method:** POST

**Request**
| 이름          | 타입     | 설명                                                                                       | 필수 |
|---------------|----------|------------------------------------------------------------------------------------------|------|
| signinMethod  | String   | 로그인 방법 ('kakao', 'naver', 'google', 'email'). 사용자는 이 중 하나를 선택하여 제공해야 함. | O    |

**Response**
| 이름     | 타입     | 설명                           | 필수 |
|----------|----------|------------------------------|------|
| result   | String   | 작업 결과 ('success' 또는 'fail') | O    |

#### 이메일 로그인
**URL:** `/users/signin/email`  
**Method:** POST

**Request**
| 이름         | 타입     | 설명         | 필수 |
|--------------|----------|------------|------|
| id           | String   | 사용자 ID     | O    |
| encPassword  | String   | 암호화된 비밀번호 | O    |

**Response**
| 이름     | 타입     | 설명                           | 필수 |
|----------|----------|------------------------------|------|
| result   | String   | 작업 결과 ('success' 또는 'fail') | O    |

#### 로그아웃
**URL:** `/users/logout`  
**Method:** POST

**Request:** None

**Response**
| 이름     | 타입     | 설명                           | 필수 |
|----------|----------|------------------------------|------|
| result   | String   | 작업 결과 ('success' 또는 'fail') | O    |

#### 프로필 조회 및 수정
**URL:** `/users/profiles`  
**조회:** GET  
**수정:** POST

**Request for Viewing**
| 이름    | 타입    | 설명  | 필수 |
|---------|---------|-----------------------|------|
| method  | String  | 'S'는 조회, 'U'는 수정 | O    |

**Response for Viewing**  
| 이름    | 타입    | 설명  | 필수 |
|---------|---------|--------------|------|
|id       | String   | 사용자 ID          | O     |
|name       | String   | 사용자 이름       | O     |
|email       | String   | 사용자 이메일     | O     |
|snsConnection | Object   | SNS 연결 상태 정보 | O    |
|kakao | boolean   | kakao SNS 연결 상태 정보 |  O   |
|naver | boolean   | naver SNS 연결 상태 정보 |  O   |
|google | boolean   | google SNS 연결 상태 정보 | O    |
|marketingPreferences | Object   | 마케팅수신동의 상태 정보 |  O   |
|email | boolean   | email 마케팅수신동의 상태 정보 | O    |
|sms | boolean   | 문자 마케팅수신동의 상태 정보 | O    |
|pushNotification | boolean   | 앱푸시 마케팅수신동의 상태 정보 | O    |
|lastModifiedDate | String   | 최종프로필 수정일 | O    |
|joinedDate | String   | 가입일 | O    |
|point | Bigdecimal   | 소유 포인트 | O    |
|coupon | int   | 소유 쿠폰 수 | O    |


**Request for Updating** 수정할 컬럼만 입력
| 이름            | 타입     | 설명                         | 필수 |
|-----------------|----------|----------------------------|------|
| method          | String   | 'U'로 수정 작업 지정            | O    |
|name       | String   | 사용자 이름       |      |
|email       | String   | 사용자 이메일     |     |
|snsConnection | Object   | SNS 연결 상태 정보 |     |
|kakao | boolean   | kakao SNS 연결 상태 정보 |    |
|naver | boolean   | naver SNS 연결 상태 정보 |    |
|google | boolean   | google SNS 연결 상태 정보 |    |
|marketingPreferences | Object   | 마케팅수신동의 상태 정보 |     |
|email | boolean   | email 마케팅수신동의 상태 정보 |    |
|sms | boolean   | 문자 마케팅수신동의 상태 정보 |    |
|pushNotification | boolean   | 앱푸시 마케팅수신동의 상태 정보 |    |


**Response for Updating**
| 이름     | 타입     | 설명                           | 필수 |
|----------|----------|------------------------------|------|
| result   | String   | 작업 결과 ('success' 또는 'fail') | O    |
