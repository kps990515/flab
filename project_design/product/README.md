## 상품

### 상품기본 테이블
```sql
CREATE TABLE Products (
    proudct_id VARCHAR(255) PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    category VARCHAR(10) NOT NULL,
    city VARCHAR(255) NOT NULL,
    price INT NOT NULL,
    image_url TEXT,
    average_score DECIMAL(2, 1),
    created_at TIMESTAMP NOT NULL,
    modified_at TIMESTAMP NOT NULL
);
```


### 상품상세 테이블
```sql
CREATE TABLE ProductDetails (
    product_detail_id VARCHAR(255) PRIMARY KEY,
    proudct_id VARCHAR(255) NOT NULL UNIQUE,
    minimum_number INT NOT NULL,
    maximum_number INT NOT NULL,
    transportation VARCHAR(20) NOT NULL,
    language VARCHAR(20) NOT NULL,
    product_owner VARCHAR(255) NOT NULL,
    created_at TIMESTAMP NOT NULL,
    modified_at TIMESTAMP NOT NULL
);
```

### 상품 이미지 테이블
```sql
CREATE TABLE ProductImages  (
    image_id VARCHAR(255) PRIMARY KEY,
    product_detail_id VARCHAR(255) NOT NULL,
    image_url TEXT NOT NULL,
    image_order INT NOT NULL,
    created_at TIMESTAMP NOT NULL,
    modified_at TIMESTAMP NOT NULL
);
```

### 상품 설명글 테이블
```sql
CREATE TABLE ProductDescriptions   (
    description_id VARCHAR(255) PRIMARY KEY,
    product_detail_id VARCHAR(255) NOT NULL ,
    description_name VARCHAR(255) NOT NULL,
    html_content_url TEXT NOT NULL,
    description_order INT NOT NULL,
    created_at TIMESTAMP NOT NULL,
    modified_at TIMESTAMP NOT NULL
);
```

### 상품예약가능여부 테이블
```sql
CREATE TABLE ProductAvailability    (
    proudct_id VARCHAR(255) PRIMARY KEY
    product_detail_id VARCHAR(255) NOT NULL UNIQUE,
    date DATE NOT NULL,
    status VARCHAR(50) NOT NULL,
    quantity_available INT NOT NULL,
    created_at TIMESTAMP NOT NULL,
    modified_at TIMESTAMP NOT NULL
);
```

### 상품후기 테이블
```sql
CREATE TABLE ProductAvailability    (
    review_id VARCHAR(255) PRIMARY KEY
    proudct_id VARCHAR(255) NOT NULL,
    product_detail_id VARCHAR(255) NOT NULL,
    user_id VARCHAR(255) NOT NULL
    score INT NOT NULL,
    review TEXT NOT NULL,
    image_url TEXT,
    created_at TIMESTAMP NOT NULL,
    modified_at TIMESTAMP NOT NULL
);
```

### 메인상품조회(인기상품 배치로 미리저장 후 노출)
 - URL : /products/main
 - Method : GET

 **Request:** 없음

 - request payload
```json
{}   
```

**response** 
| 이름            | 타입     | 설명                         | 필수 |
|-----------------|----------|----------------------------|------|
|currentPage | int   | 현재 페이지    |  O  |
|totalPages  | int   | 전체 페이지    |  O  |
|limit       | int   | 한 페이지 내역 개수 |  O  |
|products |       | 상품 VO |  O  |
|              |product_id | String   | 상품 ID |  O  |
|              |name | String   | 상품이름  |  O  |
|              |category | BigDecimal   | 상품 카테고리 |  O  |
|              |city | String   | 상품 도시 |  O  |
|              |price | BigDecimal   | 가격 |  O  |
|              |rating | BigDecimal   | 별점 |  O  |
|              |ratingCount | int   | 별점 매긴 숫자 |  O  |
|              |imageUrl | String   | 상품 이미지 URL |  O  |

 - response payload
```json
{
  "currentPage": 1,
  "totalPages": 10,
  "limit": 10,
  "products": [
    {
      "productId": "productId",
      "name": "투어 상품 이름",
      "category": "가이드투어",
      "city": "파리",
      "price": 43000,
      "rating": 4.95,
      "ratingCount": 275,
      "imageUrl": "상품 이미지 URL",
    },
    // 기타 상품들...
  ]
}  
```

### 도시상품조회
 - URL : /products/citys/{city}
 - Method : GET

**request** 
| 이름            | 타입     | 설명                         | 필수 |
|-----------------|----------|----------------------------|------|
|city       | String   | 도시       |  O   |
|cateogry | String   | 상품 카테고리       |     |
|minPrice | BigDecimal   | 조회 최저가격       |     |
|maxPrice | BigDecimal   | 조회 최고가격       |     |
|minRate | BigDecimal   | 조회 최저별점       |    |
|maxRate | BigDecimal   | 조회 최고별점       |     |

 - request payload
```json
{
  "city": "city name",
  "cateogry": "", //optional
  "minPrice": 10000, //optional
  "maxPrice": 99999, //optional
  "minRate": 1, //optional
  "maxRate": 5 //optional
}   
```

**response** 
| 이름            | 타입     | 설명                         | 필수 |
|-----------------|----------|----------------------------|------|
|currentPage | int   | 현재 페이지    |  O  |
|totalPages  | int   | 전체 페이지    |  O  |
|limit       | int   | 한 페이지 내역 개수 |  O  |
|products |       | 상품 VO |  O  |
|              |product_id | String   | 상품 ID |  O  |
|              |name | String   | 상품이름  |  O  |
|              |category | BigDecimal   | 상품 카테고리 |  O  |
|              |city | String   | 상품 도시 |  O  |
|              |price | BigDecimal   | 가격 |  O  |
|              |rating | BigDecimal   | 별점 |  O  |
|              |ratingCount | int   | 별점 매긴 숫자 |  O  |
|              |imageUrl | String   | 상품 이미지 URL |  O  |

 - response payload
```json
{
  "currentPage": 1,
  "totalPages": 10,
  "limit": 10,
  "products": [
    {
      "id": "productId",
      "name": "투어 상품 이름",
      "category": "가이드투어",
      "city": "파리",
      "price": 43000,
      "rating": 4.95,
      "ratingCount": 275,
      "imageUrl": "상품 이미지 URL",
    },
    // 기타 상품들...
  ]
}  
```

### 상품상세조회
 - URL : /products/detail/{productId}
 - Method : GET

**request** 
| 이름            | 타입     | 설명                         | 필수 |
|-----------------|----------|----------------------------|------|
|productId       | String   | 상품ID       |  O   |

 - request payload
```json
{
  "productId" : "34662"
}   
```

**response** 
| 이름            | 타입     | 설명                         | 필수 |
|-----------------|----------|----------------------------|------|
|product_id | String   | 상품 ID |  O  |
|name | String   | 상품이름  |  O  |
|category | BigDecimal   | 상품 카테고리 |  O  |
|city | String   | 상품 도시 |  O  |
|price | BigDecimal   | 가격 |  O  |
|rating | BigDecimal   | 별점 |  O  |
|ratingCount | int   | 별점 매긴 숫자 |  O  |
|imageUrl | String   | 상품 이미지 URL |  O  |
|minimumNumber | String   | 최저인원수 |  O  |
|maximumNumber | String   | 최고인원수 |  O  |
|trasportation | String   | 교통수단 |  O  |
|language | String   | 제공언어 |  O  |
|productOwner | String   | 상품등록자 |  O  |
|imageList |               | 이미지 리스트 |  O  |
|              |imageId | String   | 이미지 ID |  O  |
|              |imageUrl | String   | 이미지 주소 |  O  |
|              |order | int   | 이미지 순번 |  O  |
|descriptionList |               | 설명글 리스트 |  O  |
|              |descriptionId | String   | 설명글 ID |  O  |
|              |descriptionName | String   | 설명글 이름 |  O  |
|              |htmlContentUrl | int   | 설명글 주소 |  O  |
|              |order | int   | 설명글 순번 |  O  |

 - response payload
```json
{
  "id": "productId",
  "name": "투어 상품 이름",
  "category": "가이드투어",
  "city": "파리",
  "price": 43000,
  "rating": 4.95,
  "ratingCount": 275,
  "imageUrl": "상품 이미지 URL",
  "minimumNumber": 1,
  "maximumNumber": 20,
  "trasportation": "도보",
  "language": "한국어",
  "productOwner": "(주)데이트립",
  "imageList": [
    {
      "imageId": "23155423",
      "imageUrl": "www.",
      "order": 1
    },
    // 다른이미지들...
  ],
  "descriptionList": [
    {
      "descriptionId": 23125,
      "descriptionName": "코스소개",
      "htmlContentUrl" : "www...",
      "order": 1,
    },
     // 다른설명글들...
  ]
}  
```

#### 상품상세조회(예약가능날짜조회)
 - URL : /products/detail/{productId}/availableDates
 - Method : GET

**request** 
| 이름            | 타입     | 설명                         | 필수 |
|-----------------|----------|----------------------------|------|
|productId       | String   | 상품ID       |  O   |
|date       | String   | 날짜(하루단건조회 없으면 전체날짜 조회)      |     |

 - request payload
```json
{
  "productId" : "34662",
  "date": "20230102" //하루단건조회 없으면 전체날짜 조회
}   
```

**response** 
| 이름            | 타입     | 설명                         | 필수 |
|-----------------|----------|----------------------------|------|
|dateList |               | 날짜 리스트 |  O  |
|              |date | String   | 날짜 |  O  |
|              |status | String   | 예약가능상태 |  O  |
|              |quantityAvailable | int   | 예약가능인원 |  O  |

 - response payload
```json
{
  "dateList": [
    {
      "date": "20230101",
      "status": "Available",
      "quantityAvailable" : 10,
    },
     // 다른날짜들...
  ]
}  
```

#### 상품상세조회(후기조회)
 - URL : /products/detail/{productId}/reviews
 - Method : GET

**request** 
| 이름            | 타입     | 설명                         | 필수 |
|-----------------|----------|----------------------------|------|
|productId       | String   | 상품ID       |  O   |

 - request payload
```json
{
  "productId" : "34662"
}   
```

**response** 
| 이름            | 타입     | 설명                         | 필수 |
|-----------------|----------|----------------------------|------|
|currentPage | int   | 현재 페이지    |  O  |
|totalPages  | int   | 전체 페이지    |  O  |
|limit       | int   | 한 페이지 내역 개수 |  O  |
|reviewList |       | 리뷰 리스트 |  O  |
|              |reviewId | String   | 리뷰 ID |  O  |
|              |userId | String   | 작성자 ID  |  O  |
|              |score | BigDecimal   | 별점 |  O  |
|              |review | String   | 리뷰내용 |  O  |
|              |imageUrl | String   | 리뷰 이미지 주소 |  O  |
|              |updatedAt | String   | 최종 수정날짜 |  O  |

 - response payload
```json
{
  "currentPage": 1,
  "totalPages": 10,
  "limit": 10,
  "reviewList": [
    {
      "reviewId": "2353",
      "userId": 1234,
      "score": 3.5,
      "review": "리뷰내용",
      "imageUrl": "www....",
      "updatedAt": "20230101"
    },
    // 기타 리뷰들...
  ]
}  
```

### 후기등록
 - URL : /products/detail/{productId}/reviews
 - Method : POST

**request** 
| 이름            | 타입     | 설명                         | 필수 |
|-----------------|----------|----------------------------|------|
|productId       | String   | 상품ID       |  O   |
|score       | BigDecimal   | 별점      |  O   |
|imageUrl       | String   | 후기이미지 URL       |     |
|review       | String   | 후기내용       |  O   |

 - request payload
```json
{
  "productId" : "34662",
  "score": 3.5,
  "imageUrl": "data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD...",
  "review": "리뷰내용"
}   
```

**Response**
| 이름     | 타입     | 설명                           | 필수 |
|----------|----------|------------------------------|------|
| result   | String   | 작업 결과 ('success' 또는 'fail') | O    |

 - response payload
```json
{
  "result": "success",
}  
```

### 상품등록
 - URL : /products
 - Method : POST

**request** 
| 이름            | 타입     | 설명                         | 필수 |
|-----------------|----------|----------------------------|------|
|name | String   | 상품이름  |  O  |
|category | BigDecimal   | 상품 카테고리 |  O  |
|city | String   | 상품 도시 |  O  |
|price | BigDecimal   | 가격 |  O  |
|minimumNumber | String   | 최저인원수 |  O  |
|maximumNumber | String   | 최고인원수 |  O  |
|trasportation | String   | 교통수단 |  O  |
|language | String   | 제공언어 |  O  |
|productOwner | String   | 상품등록자 |  O  |

 - request payload
```json
{
  "name": "투어 상품 이름",
  "category": "가이드투어",
  "city": "파리",
  "price": 43000,
  "minimumNumber": 1,
  "maximumNumber": 20,
  "trasportation": "도보",
  "language": "한국어",
  "productOwner": "(주)데이트립"
}   
```

**Response**
| 이름     | 타입     | 설명                           | 필수 |
|----------|----------|------------------------------|------|
| result   | String   | 작업 결과 ('success' 또는 'fail') | O    |
| productId   | String   | 상품ID | O    |

 - response payload
```json
{
  "result": "success",
  "productId": 12345
}  
```

#### 상품등록(이미지)
 - URL : /products/images
 - Method : POST

**request** 
| 이름            | 타입     | 설명                         | 필수 |
| productId   | String   | 상품ID | O    |
|imageList |               | 이미지 리스트 |  O  |
|              |imageId | String   | 이미지 ID |  O  |
|              |imageUrl | String   | 이미지 주소 |  O  |
|              |order | int   | 이미지 순번 |  O  |
 
 - request payload
```json
{
  "productId": 12345,
  "imageList": [
  {
    "imageId": "2353",
    "imageUrl": "www.",
    "order": 1,
  },
  ]

}  
```

**Response**
| 이름     | 타입     | 설명                           | 필수 |
|----------|----------|------------------------------|------|
| result   | String   | 작업 결과 ('success' 또는 'fail') | O    |

 - response payload
```json
{
  "result": "success"
}  
```

