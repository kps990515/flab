## 상품

### 상품기본 테이블
```sql
CREATE TABLE Products (
    ProductID VARCHAR(255) PRIMARY KEY,
    Name VARCHAR(255) NOT NULL,
    Category VARCHAR(10) NOT NULL,
    City VARCHAR(255) NOT NULL,
    Price INT NOT NULL,
    ImageUrl TEXT,
    AverageScore DECIMAL(2, 1),
    CreatedAt TIMESTAMP NOT NULL,
    ModifiedAt TIMESTAMP NOT NULL
);
```


### 상품상세 테이블
```sql
CREATE TABLE ProductDetails (
    ProductDetailID VARCHAR(255) PRIMARY KEY,
    ProductID VARCHAR(255) NOT NULL UNIQUE,
    MinimumNumber INT NOT NULL,
    MaximumNumber INT NOT NULL,
    Transportation VARCHAR(20) NOT NULL,
    Language VARCHAR(20) NOT NULL,
    ProductOwner VARCHAR(255) NOT NULL,
    CreatedAt TIMESTAMP NOT NULL,
    ModifiedAt TIMESTAMP NOT NULL
);
```

### 상품 이미지 테이블
```sql
CREATE TABLE ProductImages  (
    ImageID VARCHAR(255) PRIMARY KEY,
    ProductDetailID VARCHAR(255) NOT NULL,
    ImageURL TEXT NOT NULL,
    ImageOrder INT NOT NULL,
    CreatedAt TIMESTAMP NOT NULL,
    ModifiedAt TIMESTAMP NOT NULL
);
```

### 상품 설명글 테이블
```sql
CREATE TABLE ProductDescriptions   (
    DescriptionID VARCHAR(255) PRIMARY KEY,
    ProductDetailID VARCHAR(255) NOT NULL ,
    DescriptionName VARCHAR(255) NOT NULL,
    HtmlContentURL TEXT NOT NULL,
    DescriptionOrder INT NOT NULL,
    CreatedAt TIMESTAMP NOT NULL,
    ModifiedAt TIMESTAMP NOT NULL
);
```

### 상품예약가능여부 테이블
```sql
CREATE TABLE ProductAvailability    (
    ProductID VARCHAR(255) PRIMARY KEY
    ProductDetailID VARCHAR(255) NOT NULL UNIQUE,
    Date DATE NOT NULL,
    Status VARCHAR(50) NOT NULL,
    QuantityAvailable INT NOT NULL,
    CreatedAt TIMESTAMP NOT NULL,
    ModifiedAt TIMESTAMP NOT NULL
);
```

### 상품후기 테이블
|Column|Type|Description|
```sql
CREATE TABLE ProductAvailability    (
    ReviewID VARCHAR(255) PRIMARY KEY
    ProductID VARCHAR(255) NOT NULL,
    ProductDetailID VARCHAR(255) NOT NULL,
    UserID VARCHAR(255) NOT NULL
    Score INT NOT NULL,
    Review TEXT NOT NULL,
    ImageURL TEXT,
    CreatedAt TIMESTAMP NOT NULL,
    ModifiedAt TIMESTAMP NOT NULL
);
```

### 메인상품조회(인기상품 배치로 미리저장 후 노출)
 - URL : /products/main
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
 - URL : /products/citys/{city}
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
 - URL : /products/detail/{productId}
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
      "Order": 1,
    },
     // 다른설명글들...
  ]
}  
```

#### 상품상세조회(예약가능날짜조회)
 - URL : /products/detail/{productId}/availableDates
 - Method : GET

 - request payload
```json
{
  "productId" : "34662",
  "date": "20230102" //하루단건조회 없으면 전체날짜 조회
}   
```

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
  "reviewList": [
    {
      "reviewId": "2353",
      "userId": 1234,
      "score": 3.5,
      "review": "리뷰내용",
      "imageUrl": "www....",
      "updatedDateTime": "20230101"
    },
    // 기타 리뷰들...
  ]
}  
```

### 후기등록
 - URL : /products/detail/{productId}/Reviews
 - Method : POST

 - request payload
```json
{
  "productId" : "34662",
  "score": 3.5,
  "image": "data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD...",
  "review": "리뷰내용"
}   
```

 - response payload
```json
{
  "result": "success",
  "reviewId" : 1233,
  "imageUrl": "www..."
}  
```

### 상품등록
 - URL : /products
 - Method : POST

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
 - request: multipar/formdata

 - response payload
```json
{
  "result": "success",
  "imageList": [
    {
      "imageId": "2353",
      "imageUrl": "www.",
      "order": 1,
    },
    // 기타 이미지들...
  ]
}  
```

