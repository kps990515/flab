## 유저

### 유저 테이블
```sql
CREATE TABLE Users (
    user_id VARCHAR(255) PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    phone_number VARCHAR(255),
    joined_date DATE NOT NULL,
    email_marketing_consent BOOLEAN NOT NULL,
    email_marketing_consent_last_modifed_at DATE,
    sms_marketing_consent BOOLEAN NOT NULL,
    sms_marketing_consent_last_modifed_at DATE,
    push_marketing_consent BOOLEAN NOT NULL,
    push_marketing_consent_last_modifed_at DATE,
    created_at TIMESTAMP  NOT NULL,
    modified_at TIMESTAMP  NOT NULL
);
```

### SNS연결 테이블
```sql
CREATE TABLE SnsConnected (
    user_id VARCHAR(255) PRIMARY KEY,
    kakao_connect BOOLEAN NOT NULL,
    naver_connect BOOLEAN NOT NULL
    google_connect BOOLEAN NOT NULL
    created_at TIMESTAMP  NOT NULL,
    modified_at TIMESTAMP  NOT NULL
);
```

### 1. 로그인
 - URL : /users/signin
 - Method : POST

**Request**
| 이름          | 타입     | 설명                                                                                       | 필수 |
|---------------|----------|------------------------------------------------------------------------------------------|------|
| signinMethod  | String   | 로그인 방법 ('kakao', 'naver', 'google', 'email'). 사용자는 이 중 하나를 선택하여 제공해야 함. | O    |

 - request payload
```json
{
  "signinMethod": "kakao" //naver, google, email
}
    
```

**Response**
| 이름     | 타입     | 설명                           | 필수 |
|----------|----------|------------------------------|------|
| result   | String   | 작업 결과 ('success' 또는 'fail') | O    |

 - response payload
```json
{
    "result": "success" //fail
}
    
```

#### 2. 이메일로그인의 경우
 - URL : /users/signin/email
 - Method : POST

**Request**
| 이름         | 타입     | 설명         | 필수 |
|--------------|----------|------------|------|
| id           | String   | 사용자 ID     | O    |
| encPassword  | String   | 암호화된 비밀번호 | O    |

 - request payload
```json
{
    "id": "userId",
    "encPassword": "a342kclx23154"    
}
```

**Response**
| 이름     | 타입     | 설명                           | 필수 |
|----------|----------|------------------------------|------|
| result   | String   | 작업 결과 ('success' 또는 'fail') | O    |

 - response payload
```json
{
    "result": "success" //fail
}
```

### 3. 로그아웃
 - URL : /users/singout
 - Method : POST

**Request:** 없음

 - request payload
```json
{}
```

**Response**
| 이름     | 타입     | 설명                           | 필수 |
|----------|----------|------------------------------|------|
| result   | String   | 작업 결과 ('success' 또는 'fail') | O    |

 - response payload
```json
{
    "result": "success" //fail
}
```

### 4. 프로필조회, 수정
 - URL : /users/me
 - Method : GET

**조회 Request** : 없음

 - request payload
```json
{
}
```

**조회 Response**  
| 이름    | 타입    | 설명  | 필수 |
|---------|---------|--------------|------|
|id       | String   | 사용자 ID          | O     |
|name       | String   | 사용자 이름       | O     |
|email       | String   | 사용자 이메일     | O     |
|snsConnection |    | SNS 연결 상태 정보 | O    |
|               |kakao | boolean   | kakao SNS 연결 상태 정보 |  O   |
|               |naver | boolean   | naver SNS 연결 상태 정보 |  O   |
|               |google | boolean   | google SNS 연결 상태 정보 | O    |
|marketingPreferences |    | 마케팅수신동의 상태 정보 |  O   |
|                      |email | boolean   | email 마케팅수신동의 상태 정보 | O    |
|                      |sms | boolean   | 문자 마케팅수신동의 상태 정보 | O    |
|                      |push_notification | boolean   | 앱푸시 마케팅수신동의 상태 정보 | O    |
|lastModifiedAt | String   | 최종프로필 수정일 | O    |
|joinedDate | String   | 가입일 | O    |
|point | Bigdecimal   | 소유 포인트 | O    |
|coupon | int   | 소유 쿠폰 수 | O    |

 - response payload
```json
{
  "id": "userId",
  "name": "사용자 이름",
  "email": "이메일 주소",
  "phoneNumber": "+82 전화번호",
  "snsConnection": {
    "kakao": true,
    "naver": false,
    "google": false
  },
  "marketingPreferences": {
    "email": true,
    "sms": true,
    "pushNotification": false
  },
  "lastModifiedDate": "최종수정일",
  "joinedDate": "가입 날짜",
  "point": 19900,
  "coupon": 2
}
```

### 5. 프로필수정반영
 - URL : /users/me
 - Method : PATCH
 - 수정할 컬럼만 request에 입력

**request** 
| 이름            | 타입     | 설명                         | 필수 |
|-----------------|----------|----------------------------|------|
|name       | String   | 사용자 이름       |      |
|email       | String   | 사용자 이메일     |     |
|snsConnection |    | SNS 연결 상태 정보 |     |
|              |kakao | boolean   | kakao SNS 연결 상태 정보 |    |
|              |naver | boolean   | naver SNS 연결 상태 정보 |    |
|              |google | boolean   | google SNS 연결 상태 정보 |    |
|marketingPreferences |    | 마케팅수신동의 상태 정보 |     |
|                     |email | boolean   | email 마케팅수신동의 상태 정보 |    |
|                     |sms | boolean   | 문자 마케팅수신동의 상태 정보 |    |
|                     |pushNotification | boolean   | 앱푸시 마케팅수신동의 상태 정보 |    |

 - request payload
```json
{
  "method": "U",
  "name": "새로운 이름",
  "phoneNumber": "새로운 전화번호",
  "snsConnection": {
    "naver": true
  }
}
```

**Response**
| 이름     | 타입     | 설명                           | 필수 |
|----------|----------|------------------------------|------|
| result   | String   | 작업 결과 ('success' 또는 'fail') | O    |

 - response payload
```json
{
  "result": "success" //fail
}
```