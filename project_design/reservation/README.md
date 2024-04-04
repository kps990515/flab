## 예약

### 예약 테이블
```sql
CREATE TABLE Reservation (
    reservation_id VARCHAR(255) PRIMARY KEY,
    product_id VARCHAR(255) NOT NULL,
    user_id VARCHAR(255) NOT NULL,
    reservation_id DATE NOT NULL,
    price INT NOT NULL,
    coupon_use BOOLEAN NOT NULL,
    coupon_discount_price INT NOT NULL,
    point_use BOOLEAN NOT NULL,
    point_discount_price INT NOT NULL,
    final_price INT NOT NULL,
    quantity INT NOT NULL,
    extra_request TEXT,
    pay_method VARCHAR(50) NOT NULL,
    status VARCHAR(50) NOT NULL,
    created_at TIMESTAMP  NOT NULL,
    modified_at TIMESTAMP  NOT NULL
);
```

### 예약조회
 - URL : /reservations
 - Method : GET

**request** 
| 이름            | 타입     | 설명                         | 필수 |
|-----------------|----------|----------------------------|------|
|status       | String   | 예약상태(예약진행중, 예약완료 등)       |  O   |
|startDate       | String   | 조회 시작일자      |  O   |
|endDate       | String   | 조회 종료일자       |     |

 - request payload
```json
{
  "status" : "Complete", //선택사항
  "startDate" : "20230101", //선택사항
  "endDate" : "20231231" //선택사항
}
    
```

**response** 
| 이름            | 타입     | 설명                         | 필수 |
|-----------------|----------|----------------------------|------|
|currentPage | int   | 현재 페이지    |  O  |
|totalPages  | int   | 전체 페이지    |  O  |
|limit       | int   | 한 페이지 내역 개수 |  O  |
|reservations |       | 예약 VO |  O  |
|              |name | String   | 상품이름  |  O  |
|              |category | BigDecimal   | 상품 카테고리 |  O  |
|              |city | String   | 상품 도시 |  O  |
|              |price | BigDecimal   | 가격 |  O  |
|              |imageUrl | String   | 상품 이미지 URL |  O  |
|              |status | String   | 예약상태 |  O  |

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
 - URL : /reservations/{productId}
 - Method : POST

**request** 
| 이름            | 타입     | 설명                         | 필수 |
|-----------------|----------|----------------------------|------|
|reservationDate       | String   | 예약일자       |  O   |
|quantity       | int   | 예약인원      |  O   |
|coupon       | String   | 사용 쿠폰ID      |     |
|point       | BigDecimal   | 사용 포인트       |     |
|ExtraRequest       | String   | 추가 요청사항      |     |
|PayMethod       | String   | 결제수단      |  O   |

 - request payload
```json
{
  "productId" : "productId",
  "reservationDate" : "20230101",
  "quantity" : 2,
  "couponId" : "ddd",
  "point" : 100,
  "ExtraRequest" : "./..",
  "PayMethod" : "creditCard"
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

### 예약변경
 - URL : /reservations/{reservationId}
 - Method : PATCH

**request** 
| 이름            | 타입     | 설명                         | 필수 |
|-----------------|----------|----------------------------|------|
|reservationDate       | String   | 예약일자       |  O   |
|ExtraRequest       | String   | 추가 요청사항      |     |

 - request payload
```json
{ //날짜 변경, 요청사항 변경만 가능
  "reservationDate" : "20230101",
  "ExtraRequest" : "./..",
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

### 예약취소
 - URL : /reservations/{reservationId}/cancel
 - Method : POST

**request** 
| 이름            | 타입     | 설명                         | 필수 |
|-----------------|----------|----------------------------|------|
|reservationId       | String   | 예약ID       |  O   |
|reason       | String   | 취소사유    |     |

 - request payload
```json
{ 
  "reservationId" : "reservationId",
  "resion" : "이유"
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