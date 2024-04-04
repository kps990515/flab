## 포인트

###  포인트 테이블
```sql
CREATE TABLE Point (
    user_id VARCHAR(255) PRIMARY KEY,
    balance_ BIGDECIMAL NOT NULL
    create_at TIMESTAMP  NOT NULL,
    modified_at TIMESTAMP  NOT NULL
);
```

###  포인트 적립,이용내역 테이블
```sql
CREATE TABLE PointTransaction (
    transaction_id VARCHAR(255) PRIMARY KEY
    user_id VARCHAR(255) PRIMARY KEY,
    point INT NOT NULL,
    transaction_type VARCHAR(5) NOT NULL,
    transaction_at TIMESTAMP NOT NULL,
    description TEXT,
    created_at TIMESTAMP  NOT NULL,
    modifed_at TIMESTAMP  NOT NULL
);
```

### 포인트조회
 - URL : /points/balance
 - Method : GET

**Request:** 없음

 - request payload
```json
{
}
    
```

**Response**
| 이름     | 타입     | 설명                           | 필수 |
|----------|----------|------------------------------|------|
| balance   | BigDecimal  | 포인트 잔고               |  O  |

 - response payload
```json
{
  "balance": 100
}  
```

### 포인트 이용/적립내역 조회
 - URL : /points/transactions
 - Method : GET

**request** 
| 이름            | 타입     | 설명                         | 필수 |
|-----------------|----------|----------------------------|------|
|startDate       | String   | 조회시작일자       |      |
|endDate         | String   | 조회종료일자       |     |
|transactionType | String   | 조회종류(U:이용 A:적립 없으면 전체조회)|     |

 - request payload
```json
{
  "startDate" : "20230101",
  "endDate" : "20231231",
  "transactionType" : "use"
}
    
```

**response** 
| 이름            | 타입     | 설명                         | 필수 |
|-----------------|----------|----------------------------|------|
|currentPage | int   | 현재 페이지    |  O  |
|totalPages  | int   | 전체 페이지    |  O  |
|limit       | int   | 한 페이지 내역 개수 |  O  |
|transaction |       | 내역 VO |  O  |
|              |transactionId | String   | 내역 ID |  O  |
|              |transactionType | String   | 내역 종류(이용 or 적립)  |  O  |
|              |point | BigDecimal   | 이용, 적립 포인트 |  O  |
|              |transactionAt | String   | 이용, 적립 일시 |  O  |
|              |description | String   | 이용, 적립 설명 |  O  |

 - response payload
```json
{
  "currentPage": 1,
  "totalPages": 10,
  "limit": 10,
  "transactions": [
    {
      "transactionId": "transactionId",
      "transactionType": "use",
      "point": "-500",
      "transactionAt": "20230101235959",
      "description": "투어예약",
    },
    // 기타 내역들...
  ]
}
```

### 포인트적립
 - URL : /points/accumulations
 - Method : POST

**request** 
| 이름            | 타입     | 설명                         | 필수 |
|-----------------|----------|----------------------------|------|
|point       | BigDecimal   | 적립포인트       |  O   |
|description | String   | 적립설명       |  O   |

 - request payload
```json
{ 
  "point" : 100,
  "description" : "ddd"
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

### 포인트사용
 - URL : /points/use
 - Method : POST

**request** 
| 이름            | 타입     | 설명                         | 필수 |
|-----------------|----------|----------------------------|------|
|point       | BigDecimal   | 사용포인트       |  O   |
|description | String   | 사용설명       |  O   |

 - request payload
```json
{ 
  "point" : "100",
  "description" : "ddd"
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