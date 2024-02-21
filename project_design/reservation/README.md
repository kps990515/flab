## 예약

### 예약 테이블
```sql
CREATE TABLE Reservation (
    ReservationID VARCHAR(255) PRIMARY KEY,
    ProductID VARCHAR(255) NOT NULL,
    UserID VARCHAR(255) NOT NULL,
    ReservationDate DATE NOT NULL,
    Price INT NOT NULL,
    CouponUse BOOLEAN NOT NULL,
    CouponDiscountPrice INT NOT NULL,
    PointUse BOOLEAN NOT NULL,
    PointDiscountPrice INT NOT NULL,
    FinalPrice INT NOT NULL,
    Quantity INT NOT NULL,
    ExtraRequest TEXT,
    PayMethod VARCHAR(50) NOT NULL,
    Status VARCHAR(50) NOT NULL,
    CreatedAt TIMESTAMP  NOT NULL,
    ModifiedAt TIMESTAMP  NOT NULL
);
```

### 예약조회
 - URL : /reservations
 - Method : GET

 - request payload
```json
{
  "status" : "Complete", //선택사항
  "startDate" : "20230101", //선택사항
  "endDate" : "20231231" //선택사항
}
    
```

 - response payload
```json
{
  "currentPage": 1,
  "totalPages": 10,
  "limit": 10,
  "reservations": [
    {
      "name": "투어 상품 이름",
      "category": "가이드투어",
      "city": "파리",
      "price": 43000,
      "imageUrl": "상품 이미지 URL",
      "status": "Complete"
    },
    // 기타 예약들...
  ]
}  
```

### 예약하기
 - URL : /reservations
 - Method : POST

 - request payload
```json
{
  "reservationDate" : "20230101",
  "quantity" : 2,
  "coupon" : "ddd",
  "point" : 100,
  "ExtraRequest" : "./..",
  "PayMethod" : "creditCard"
}
    
```

 - response payload
```json
{
    "result": "success" //fail
}
```

### 예약변경
 - URL : /reservations/{reservationsId}
 - Method : PATCH

 - request payload
```json
{ //날짜 변경, 요청사항 변경만 가능
  "reservationDate" : "20230101",
  "ExtraRequest" : "./..",
}
    
```

 - response payload
```json
{
    "result": "success" //fail
}
```

### 예약취소
 - URL : /reservations/{reservationsId}/cancel
 - Method : PATCH

 - request payload
```json
{ 
  "reason" : "dddd"
}
    
```

 - response payload
```json
{
    "result": "success" //fail
}
```