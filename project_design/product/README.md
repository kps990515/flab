## 상품

### 상품기본 테이블
|Column|Type|Description|
|------|------|------------|
|ProductID|UUID|상품 고유 ID|
|Name|VARCHAR(255)|상품 이름|
|Category|VARCHAR(10)|상품 카테고리|
|City|VARCHAR(255)|도시|
|Price|INT|상품 가격|
|ImageUrl|TEXT|상품 이미지 URL|
|AverageScore|DECIMAL(1,1)|평균 후기 점수|
|CreatedDateTime|TIMESTAMP|상품 생성 날짜|
|UpdatedDateTime|TIMESTAMP|상품 정보 갱신 날짜|

### 상품상세 테이블
|Column|Type|Description|
|------|------|------------|
|ProductID|UUID|상품 고유 ID|
|MinimumNumber|INT|최소 출발 인원|
|MaximumNumber|INT|최대 출발 인원|
|Transportation|VARCHAR(20)|이동수단|
|Language|VARCHAR(20)|언어|
|ProductOwner|VARCHAR(255)|상품등록자이름|

### 상품 이미지 테이블
|Column|Type|Description|
|------|------|------------|
|ImageID|UUID|이미지 ID|
|ProductID|UUID|상품 고유 ID|
|ImageURL|TEXT|이미지 URL|
|Order|INT|이미지 표시 순서|

### 상품 설명글 테이블
|Column|Type|Description|
|------|------|------------|
|DescriptionID|UUID|설명글 ID|
|ProductID|UUID|상품 고유 ID|
|DescriptionName|상품 설명 제목(상품정보, 코스, 이용안내)|
|HtmlContentURL|TEXT|HTML 형식의 설명 URL|
|Order|INT|상품설명글 순서|

### 상품예약가능여부 테이블
|Column|Type|Description|
|------|------|------------|
|AvailabilityID|UUID|예약가능여부 ID|
|ProductID|UUID|상품 고유 ID|
|Date|DATE|예약 가능한 날짜
|Status|VARCHAR(50)|예약 가능 상태|
|QuantityAvailable|INT|해당 날짜에 사용 가능한 예약 수량|

### 상품후기 테이블
|Column|Type|Description|
|------|------|------------|
|ReviewID|UUID|후기 ID|
|ProductID|UUID|상품 고유 ID|
|UserID|UUID|고객 고유 ID|
|Score|INT|후기 점수|
|Review|TEXT|후기|
|ImageURL|TEXT|후기 이미지 URL|
|CreatedDateTime|TIMESTAMP|후기 생성 날짜|
|UpdatedDateTime|TIMESTAMP|후기 갱신 날짜|


### 메인상품조회(인기상품 배치로 미리저장 후 노출)
 - URL : /product
 - Method : GET

 - request payload
```json
{}   
```

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

### 도시상품조회
 - URL : /product/city/{city}
 - Method : GET

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
 - URL : /product/{productId}
 - Method : GET

 - request payload
```json
{
  "productId" : "34662"
}   
```

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