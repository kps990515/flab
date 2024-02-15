## 유저

### 유저 테이블
|Column|Type|Description|
|------|------|------------|
|ID|UUID|사용자 고유 ID|
|Name|VARCHAR(255)|사용자 이름|
|Email|VARCHAR(255)|사용자 이메일|
|Password|VARCHAR(255)|해시된 비밀번호
|PhoneNumber|VARCHAR(255)|사용자 전화번호
|JoinedDate|DATE|사용자 가입 날짜
|LastModifiedDate|DATE|최종 수정 날짜

### 마케팅 동의 테이블
|Column|Type|Description|
|------|------|------------|
|ID|UUID|사용자 고유 ID|
|EmailMarketingConsent|BOOLEAN|이메일 마케팅 동의 여부
|EmailMarketingConsentLastModifedDate|DATE|이메일 최종 수정 날짜
|SmsMarketingConsent|BOOLEAN|SMS 마케팅 동의 여부
|SmsMarketingConsentLastModifedDate|DATE|이메일 최종 수정 날짜
|PushMarketingConsent|BOOLEAN|푸시 알림 동의 여부
|PushMarketingConsentLastModifedDate|DATE|이메일 최종 수정 날짜

### SNS연결 테이블
|Column|Type|Description|
|------|------|------------|
|ID|UUID|사용자 고유 ID|
|SnsProvider|VARCHAR(255)|SNS 서비스 제공자|
|Connection|BOOLEAN|연결 여부|
|ConnectionDate|DATE|연결 날짜|

### 로그인
URL : /user/signin
Method : POST

request payload
```json
    "signinMethod": "kakao" //naver, google, email
```

response payload
```json
{
    "result": "success" //fail
}
    
```

#### 이메일로그인의 경우
URL : /user/signin/email
Method : POST

request payload
```json
{
    "id": "userId",
    "encPassword": "a342kclx23154"    
}
```

response payload
```json
{
    "result": "success" //fail
}
```

### 로그아웃
URL : /user/logout
Method : POST

request payload
```json
   {}
```

response payload
```json
{
    "result": "success" //fail
}
```

### 프로필조회, 수정
URL : /user/profile
Method : POST

request payload
```json
{
  "method": "S"
}
```

response payload
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
URL : /user/profile
Method : POST

request payload
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

response payload
```json
{
  "result": "success" //fail
}
```