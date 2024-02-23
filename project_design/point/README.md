## 포인트

###  포인트 테이블
```sql
CREATE TABLE Point (
    UserID VARCHAR(255) PRIMARY KEY,
    Balance INT NOT NULL
    CreatedAt TIMESTAMP  NOT NULL,
    ModifiedAt TIMESTAMP  NOT NULL
);
```

###  포인트 적립,이용내역 테이블
```sql
CREATE TABLE PointTransaction (
    TransactionId VARCHAR(255) PRIMARY KEY
    UserID VARCHAR(255) PRIMARY KEY,
    Point INT NOT NULL,
    TransactionType VARCHAR(5) NOT NULL,
    TransactionAt TIMESTAMP NOT NULL,
    Description TEXT,
    CreatedAt TIMESTAMP  NOT NULL,
    ModifiedAt TIMESTAMP  NOT NULL
);
```

### 포인트조회
 - URL : /points/balance
 - Method : GET

 - request payload
```json
{
}
    
```

 - response payload
```json
{
  "balance": 100
}  
```

### 포인트 이용/적립내역 조회
 - URL : /points/transactions
 - Method : GET

 - request payload
```json
{
  "startDate" : "20230101",
  "endDate" : "20231231",
  "transactionType" : "use"
}
    
```

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

 - request payload
```json
{ 
  "point" : "100",
  "description" : "ddd"
}
    
```

 - response payload
```json
{
    "result": "success" //fail
}
```

### 포인트사용
 - URL : /points/use
 - Method : POST

 - request payload
```json
{ 
  "point" : "100",
  "description" : "ddd"
}
    
```

 - response payload
```json
{
    "result": "success" //fail
}
```