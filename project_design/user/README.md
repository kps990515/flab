## 유저

### 유저 테이블
```sql
CREATE TABLE Users (
    UserId VARCHAR(255) PRIMARY KEY,
    Name VARCHAR(255) NOT NULL,
    Email VARCHAR(255) NOT NULL UNIQUE,
    Password VARCHAR(255) NOT NULL,
    PhoneNumber VARCHAR(255),
    JoinedDate DATE NOT NULL,
    EmailMarketingConsent BOOLEAN NOT NULL,
    EmailMarketingConsentLastModifiedAt DATE,
    SmsMarketingConsent BOOLEAN NOT NULL,
    SmsMarketingConsentLastModifiedAt DATE,
    PushMarketingConsent BOOLEAN NOT NULL,
    PushMarketingConsentLastModifiedAt DATE,
    CreatedAt TIMESTAMP  NOT NULL,
    ModifiedAt TIMESTAMP  NOT NULL
);
```

### SNS연결 테이블
```sql
CREATE TABLE SnsConnected (
    UserId VARCHAR(255) PRIMARY KEY,
    SnsProvider VARCHAR(255) NOT NULL,
    Connection BOOLEAN NOT NULL,
    CreatedAt TIMESTAMP  NOT NULL,
    ModifiedAt TIMESTAMP  NOT NULL
);
```

### 로그인
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

#### 이메일로그인의 경우
 - URL : /users/signin/email
 - Method : POST

 - request payload
```json
{
    "id": "userId",
    "encPassword": "a342kclx23154"    
}
```

 - response payload
```json
{
    "result": "success" //fail
}
```

### 로그아웃
 - URL : /users/logout
 - Method : POST

 - request payload
```json
{}
```

 - response payload
```json
{
    "result": "success" //fail
}
```

### 프로필조회, 수정
 - URL : /users/profiles
 - Method : GET

 - request payload
```json
{
  "method": "S"
}
```

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

### 프로필수정반영
 - URL : /users/profiles
 - Method : POST

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

 - response payload
```json
{
  "result": "success" //fail
}
```