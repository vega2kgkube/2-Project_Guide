# **REST API 설계서**

## **문서 정보**

* 프로젝트명: 봤어?  
* 작성자: 김XX, 임XX, 정XX  
* 작성일: 2025-0x-0x
* Base URL: [https://localhost:8080/api](https://localhost:8080/api)

---

## **1\. API 설계 개요**

### **1.1 설계 목적**

RESTful 원칙에 따라 클라이언트-서버 간 통신 규격을 정의하여 일관되고 확장 가능한 API를 제공

### **1.2 설계 원칙**

* RESTful 아키텍처: HTTP 메서드와 상태 코드의 올바른 사용  
* 일관성: 모든 API 엔드포인트에서 동일한 규칙 적용  
* 버전 관리: URL 경로를 통한 버전 구분  
* 보안: JWT 기반 인증 및 HTTPS 필수  
* 성능: 페이지네이션, 캐싱, 압축 지원  
* 문서화: 명확한 요청/응답 스펙 제공

### **1.3 기술 스택**

* 프레임워크: Spring Boot 3.4.6  
* 인증: JWT (JSON Web Token)  
* 직렬화: JSON  
* API 문서: OpenAPI 3.1.0 (Swagger)

---

## **2\. API 공통 규칙**

### **2.1 URL 설계 규칙**

| 규칙 | 설명 | 좋은 예 | 나쁜 예 |
| ----- | ----- | ----- | ----- |
| 명사 사용 | 동사가 아닌 명사로 리소스 표현 | /api/movies | /api/getMovies |
| 복수형 사용 | 컬렉션은 복수형으로 표현 | /api/users | /api/user |
| 계층 구조 | 리소스 간 관계를 URL로 표현 | /api/members/123/loans | /api/getMemberLoans |
| 소문자 사용 | URL은 소문자와 하이픈 사용 | /api/book-categories | /api/BookCategories |
| 동작 표현 | HTTP 메서드로 동작 구분 | POST /api/loans | /api/createLoan |

### **2.2 HTTP 메서드 사용 규칙**

| 메서드 | 용도 | 멱등성 | 안전성 | 예시 |
| ----- | ----- | ----- | ----- | ----- |
| GET | 리소스 조회 | ✅ | ✅ | GET /api/books |
| POST | 리소스 생성 | ❌ | ❌ | POST /api/books |
| PUT | 리소스 전체 수정 | ✅ | ❌ | PUT /api/books/123 |
| PATCH | 리소스 부분 수정 | ❌ | ❌ | PATCH /api/books/123 |
| DELETE | 리소스 삭제 | ✅ | ❌ | DELETE /api/books/123 |

### **2.3 HTTP 상태 코드 가이드**

| 코드 | 상태 | 설명 | 사용 예시 |
| ----- | ----- | ----- | ----- |
| 200 | OK | 성공 (데이터 포함) | GET 요청 성공 |
| 201 | Created | 리소스 생성 성공 | POST 요청 성공 |
| 204 | No Content | 성공 (응답 데이터 없음) | DELETE 요청 성공 |
| 400 | Bad Request | 잘못된 요청 | 검증 실패 |
| 401 | Unauthorized | 인증 필요 | 토큰 없음/만료 |
| 403 | Forbidden | 권한 없음 | 접근 거부 |
| 404 | Not Found | 리소스 없음 | 존재하지 않는 ID |
| 409 | Conflict | 충돌 | 중복 생성 시도 |
| 422 | Unprocessable Entity | 의미상 오류 | 비즈니스 규칙 위반 |
| 500 | Internal Server Error | 서버 오류 | 예기치 못한 오류 |

### **2.4 공통 요청 헤더**

Content-Type: application/json  
Accept: application/json  
Authorization: Bearer {JWT\_TOKEN}  
X-Request-Id: {UUID}  
Accept-Language: ko-KR  
User-Agent: SeenItApp/1.0.0  

---

## **3\. 인증 및 권한 관리**

### **3.1 JWT 토큰 기반 인증**

#### **3.1.1 로그인 API**

GET /api/auth/kakao/callback  
Content-Type: application/json

Response (200 OK):  
{  
  "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",  
  "isNewUser": true  
}

Response (500 Unauthorized)  
{  
    "status":  
    "error": "LOGIN\_FAILED",  
    "message": "카카오 로그인 처리 중 오류가 발생했습니다"  
}

### **3.2 권한 레벨 정의**

| 역할 | 권한 | 설명 |
| ----- | ----- | ----- |
| USER | 일반 사용자 | 콘텐츠 검색, 리뷰 작성, 별점 평가, 댓글 작성 가능 |
| ADMIN | 시스템 관리자 | 회원 상태 변경, 전체 콘텐츠 및 리뷰 관리 가능 등 전체 권한 보유 |

---

## **4\. 상세 API 명세**

### **4.1 마이페이지 API**

#### **4.1.1  현재 로그인한 사용자 정보 조회**

GET /api/auth/me  
Authorization: Bearer {JWT\_TOKEN}

Description  
JWT 토큰 기반으로 현재 로그인한 사용자의 상세 정보를 조회하는 API입니다.

Response (200 OK)  
{  
  "userId": 1,  
  "name": "홍길동",  
  "preferredGenres": \["드라마", "스릴러"\],  
  "email": "hong@example.com",  
  "profileImageUrl": "https://example.com/profile.jpg"  
}

Response (401 Unauthorized)  
{  
  "status": 401,  
  "error": "Unauthorized",  
  "message": "인증 정보가 없습니다."  
}

Response (404 Not Found)  
{  
  "status": 404,  
  "error": "Not Found",  
  "message": "사용자를 찾을 수 없습니다."  
}

#### **4.1.2 사용자 정보 수정**

PUT /api/auth/me  
Authorization: Bearer {JWT\_TOKEN}

Request Body  
{  
  "name": "홍길동",  
  "preferredGenres": \["드라마", "코미디", "스릴러"\]  
}

Response (200 OK)  
{  
  "userId": 1,  
  "name": "홍길동",  
  "preferredGenres": \["드라마", "코미디", "스릴러"\],  
  "email": "hong@example.com",  
  "profileImageUrl": "https://example.com/profile.jpg"  
}

Response (400 Bad Request)  
{  
  "status": 400,  
  "error": "Bad Request",  
  "message": "이름은 2-50자 사이여야 합니다"  
}

Response (401 Unauthorized)  
{  
  "status": 401,  
  "error": "Unauthorized",  
  "message": "인증 정보가 없습니다."  
}

Response (404 Not Found)  
{  
  "status": 404,  
  "error": "Not Found",  
  "message": "사용자를 찾을 수 없습니다."  
}

#### **4.1.3 회원 탈퇴 요청 (Soft Delete)**

DELETE /api/auth/me  
Authorization: Bearer {JWT\_TOKEN}

Description  
현재 로그인한 사용자의 계정을 비활성화 처리(soft delete)하는 API입니다.

Response (200 OK)  
"회원 탈퇴가 완료되었습니다."

Response (401 Unauthorized)  
{  
  "status": 401,  
  "error": "Unauthorized",  
  "message": "인증 정보가 없습니다."  
}

### **4.2 관리자 페이지 API**

#### **4.2.1 사용자 전체 목록 조회** 

GET /api/admin/users  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: ADMIN, LIBRARIAN, SUPER\_ADMIN (관리자 권한 필요)

Path Parameters  
\- 없음 

Response (200 OK)  
{  
  \[  
    {  
      "userID": 1,  
      "name": "김철수",  
      "status": "ACTIVE",  
     },  
    {  
      "userID": 2,  
      "name": "이영희",  
      "status": "SUSPENDED",  
     }  
  \]  
}

#### **4.2.2 사용자 상태 변경**

PUT /api/admin/users/{userId}/status  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: ADMIN, LIBRARIAN, SUPER\_ADMIN

Request Body  
{  
  "status": "SUSPENDED"  
}

Response (200 OK)  
상태 변경 성공 시 (예: ACTIVE → SUSPENDED)  
{  
  "success": true,  
  "message": "✅ 사용자 상태가 SUSPENDED로 변경되었습니다."  
}

계정 삭제 시 (DELETED)  
{  
  "success": true,  
  "message": "🗑️ 사용자 계정이 완전히 삭제되었습니다."  
}

Response (404 Not Found)  
{  
  "success": false,  
  "error": {  
    "code": "USER\_NOT\_FOUND",  
    "message": "해당 사용자를 찾을 수 없습니다."  
  }  
}

### **4.3 영화 관리 API**

#### **4.3.1 영화 목록 조회**

GET /api/movies

Query Parameters:  
\- page: integer (default: 0\) \- 페이지 번호  
\- size: integer (default: 20, max: 100\) \- 페이지 크기  
\- sort: string (default: "combinedRating") \- 정렬 기준(combinedRating, releaseDate, title)

Response (200 OK):  
{  
  "content": \[  
  	{  
		"id": 67,  
"tmdbId": 1087192,  
"title": "드래곤 길들이기",  
"posterPath": "/8vywrRg1wrY4fo7EqgrFmUJgchG.jpg",  
"voteAverage": 9.2,  
"voteCount": 8,  
"releaseDate": "2025-06-06",  
"genreNames": \[  
	"액션",  
	"가족",  
	"판타지”  
\],  
"reviewCount": 0  
},  
"currentPage": 0,  
"totalPages": 11,  
"totalElements": 212,  
"size": 20,  
"hasNext": true,  
"hasPrevious": false,  
"isFirst": true,  
"isLast": false  
}

#### **4.3.2 영화 상세정보 조회**

GET /api/movies/{id}

Path Parameters:  
\- id: integer (required) \- 영화 ID

Response (200 OK):  
{  
	"id": 67,  
"tmdbId": 1087192,  
"title": "드래곤 길들이기",  
"overview": "수백년간 지속되어온 바이킹과 드래곤의 전쟁, 드래곤을 없애는 것이 삶의 모든 목적인 바이킹들과 다른 신념을 가진 ’히컵’은 무리 속에 속하지 못하고 족장인 아버지에게도 인정받지 못한다. 그러던 어느 날, 히컵은 베일에 싸인 전설의 드래곤 나이트 퓨어리인 ’투슬리스’와 만나게 되고, 드래곤을 죽이라는 바이킹의 신념을 깨고 투슬리스와 친구가 된다. 하지만 드래곤을 죽여야 도니다고 믿는 바이킹 족과 모든 드래곤을 위협하는 더 거대한 존재와 맞닥뜨리게 된 히컵과 투슬리스. 세상을 변화시키기 위한 특별한 여정을 시작하게 되는데… 다르다는 건, 특별하다는 것, 세상을 바꿀 우리들의 모험이 시작된다\!",  
"releaseDate": "2025–06-06",  
"posterPath": "/8vywrRg1wrY4fo7EqgrFmUJgchG.jpg",  
"voteAverage": 9.2,  
"voteCount": 8,  
"genres": \[  
	{  
		"id": 28,  
		"name": "액션",  
	},  
	{  
		"id": 10751,  
		"name": "가족",  
	}  
	{  
		"id": 14,  
		"name": "판타지"  
	}  
\],  
"reviewCount": 0  
}

#### **4.3.3 TMDB ID 영화 조회**

GET /api/movies/tmdb/{tmdbId}

Path Parameters:  
\- tmdbId: integer (required) \- TMDB API 내 영화 ID

Response (200 OK):  
{  
	"id": 67,  
"tmdbId": 1087192,  
"title": "드래곤 길들이기",  
"overview": "수백년간 지속되어온 바이킹과 드래곤의 전쟁, 드래곤을 없애는 것이 삶의 모든 목적인 바이킹들과 다른 신념을 가진 ’히컵’은 무리 속에 속하지 못하고 족장인 아버지에게도 인정받지 못한다. 그러던 어느 날, 히컵은 베일에 싸인 전설의 드래곤 나이트 퓨어리인 ’투슬리스’와 만나게 되고, 드래곤을 죽이라는 바이킹의 신념을 깨고 투슬리스와 친구가 된다. 하지만 드래곤을 죽여야 도니다고 믿는 바이킹 족과 모든 드래곤을 위협하는 더 거대한 존재와 맞닥뜨리게 된 히컵과 투슬리스. 세상을 변화시키기 위한 특별한 여정을 시작하게 되는데… 다르다는 건, 특별하다는 것, 세상을 바꿀 우리들의 모험이 시작된다\!",  
"releaseDate": "2025–06-06",  
"posterPath": "/8vywrRg1wrY4fo7EqgrFmUJgchG.jpg",  
"voteAverage": 9.2,  
"voteCount": 8,  
"genres": \[  
	{  
		"id": 28,  
		"name": "액션",  
	},  
	{  
		"id": 10751,  
		"name": "가족",  
	}  
	{  
		"id": 14,  
		"name": "판타지"  
	}  
\],  
"reviewCount": 0  
}

#### **4.3.4 장르별 영화 조회**

GET /api/movies/genre/{genreId}

Path Parameters:  
\- genreId: integer (required) \- 장르 ID  
\- page: integer (default: 0\) \- 페이지 번호  
\- size: integer (default: 20, max: 100\) \- 페이지 크기

Response (200 OK):  
{  
  "content": \[  
  	{  
		"id": 67,  
"tmdbId": 1087192,  
"title": "드래곤 길들이기",  
"posterPath": "/8vywrRg1wrY4fo7EqgrFmUJgchG.jpg",  
"voteAverage": 9.2,  
"voteCount": 8,  
"releaseDate": "2025-06-06",  
"genreNames": \[  
	"액션",  
	"가족",  
	"판타지”  
\],  
"reviewCount": 0  
},  
{  
		"id": 112,  
"tmdbId": 122,  
"title": "반지의 제왕: 왕의 귀환",  
"posterPath": "/n8BPIRqvj1SdTRND828ANXhmSng.jpg",  
"voteAverage": 8.5,  
"voteCount": 25121,  
"releaseDate": "2003-12-17",  
"genreNames": \[  
	"모험",  
	"판타지",  
	"액션”  
\],  
"reviewCount": 0  
},  
{  
		"id": 106,  
"tmdbId": 129,  
"title": "센과 치히로의 행방불명",  
"posterPath": "/aZuBfbR0PnCb2up7lqHDsgJlLjs.jpg",  
"voteAverage": 8.5,  
"voteCount": 17178,  
"releaseDate": "2011-07-20",  
"genreNames": \[  
	"애니메이션",  
	"가족",  
	"판타지”  
\],  
"reviewCount": 0  
},  
"currentPage": 0,  
"totalPages": 2,  
"totalElements": 26,  
"size": 20,  
"hasNext": true,  
"hasPrevious": false,  
"isFirst": true,  
"isLast": false  
}

#### **4.3.5 인기 영화 조회**

GET /api/movies/popular  
Query Parameters:  
\- limit(default: 20\) \- 조회할 개수

Response (200 OK):  
{  
		"id": 67,  
"tmdbId": 1087192,  
"title": "드래곤 길들이기",  
"posterPath": "/8vywrRg1wrY4fo7EqgrFmUJgchG.jpg",  
"voteAverage": 9.2,  
"voteCount": 8,  
"releaseDate": "2025-06-06",  
"genreNames": \[  
	"액션",  
	"가족",  
	"판타지”  
\],  
"reviewCount": 0  
},

#### **4.3.6 영화 제목 검색**

GET /api/movies/search

Query Parameters:  
\- title: String (required) \- 검색할 제목  
\- page: integer (default: 0\) \- 페이지 번호  
\- size: integer (default: 20, max: 100\) \- 페이지 크기  
\- sort: string (default: "voteAverage") \- 정렬 기준(voteAverage, releaseDate, title)

Response (200 OK):  
{  
  "content": \[  
  	{  
		"id": 166,  
"tmdbId": 299534,  
"title": "어벤져스: 엔드게임",  
"posterPath": "/z7ilT5rNN9kDo8JZmgyhM6ej2xv.jpg",  
"voteAverage": 8.239,  
"voteCount": 26391,  
"releaseDate": "2019-04-24",  
"genreNames": \[  
	"모험",  
	"SF",  
	"액션”  
\],  
"reviewCount": 0  
},  
"currentPage": 0,  
"totalPages": 1,  
"totalElements": 14,  
"size": 20,  
"hasNext": false,  
"hasPrevious": false,  
"isFirst": true,  
"isLast": true  
}

#### **4.3.7 영화 세부 검색**

POST /api/movies/search

Query Parameters:  
\- page: integer (default: 0\) \- 페이지 번호  
\- size: integer (default: 20, max: 100\) \- 페이지 크기

Request Body:  
{   
"title": "아바타",   
"genreIds": \[1, 2\],   
"minRating": 3.5,   
"maxRating": 5.0,   
"sortBy": "rating",   
"sortDirection": "desc"   
}

Response (200 OK):  
{  
    "content": \[  
        {  
            "id": 302,  
            "tmdbId": 19995,  
            "title": "아바타",  
            "posterPath": "/m5lCha2XcbDowDoYHPc0DTNaCPU.jpg",  
            "voteAverage": 7.6,  
            "voteCount": 32289,  
            "combinedRating": 3.8,  
            "releaseDate": "2009-12-15",  
            "genreNames": \[  
                "액션",  
                "모험",  
                "판타지",  
                "SF"  
            \],  
            "reviewCount": 0  
        },  
        {  
            "id": 303,  
            "tmdbId": 76600,  
            "title": "아바타: 물의 길",  
            "posterPath": "/u2aVXft5GLBQnjzWVNda7sdDpdu.jpg",  
            "voteAverage": 7.611,  
            "voteCount": 12597,  
            "combinedRating": 3.81,  
            "releaseDate": "2022-12-14",  
            "genreNames": \[  
                "SF",  
                "모험",  
                "액션"  
            \],  
            "reviewCount": 0  
        },  
        {  
            "id": 307,  
            "tmdbId": 1059673,  
            "title": "아바타: 딥 다이브",  
            "posterPath": "/oisvwIV2OfXWQgtroTpMSwfHDU6.jpg",  
            "voteAverage": 7.1,  
            "voteCount": 81,  
            "combinedRating": 3.55,  
            "releaseDate": "2022-12-13",  
            "genreNames": \[  
                "다큐멘터리"  
            \],  
            "reviewCount": 0  
        }  
    \],  
    "currentPage": 0,  
    "totalPages": 1,  
    "totalElements": 3,  
    "size": 20,  
    "hasNext": false,  
    "hasPrevious": false,  
    "isFirst": true,  
    "isLast": true  
}

## **4.3.8 영화 생성 (관리자)**

POST /api/movies  
Content-Type: application/json  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: ADMIN, SUPER\_ADMIN

**Request Body:**

{  
  "tmdbId": 1087192,  
  "title": "드래곤 길들이기",  
  "overview": "수백년간 지속되어온 바이킹과 드래곤의 전쟁...",  
  "releaseDate": "2025-06-06",  
  "posterPath": "/8vywrRg1wrY4fo7EqgrFmUJgchG.jpg",  
  "voteAverage": 9.2,  
  "voteCount": 8,  
  "genreIds": \[28, 10751, 14\]  
}

**Response (201 Created):**

{  
  "success": true,  
  "data": {  
    "id": 67,  
    "tmdbId": 1087192,  
    "title": "드래곤 길들이기",  
    "overview": "수백년간 지속되어온 바이킹과 드래곤의 전쟁...",  
    "releaseDate": "2025-06-06",  
    "posterPath": "/8vywrRg1wrY4fo7EqgrFmUJgchG.jpg",  
    "voteAverage": 9.2,  
    "voteCount": 8,  
    "combinedRating": 4.6,  
    "genres": \[  
      {"id": 28, "name": "액션"},  
      {"id": 10751, "name": "가족"},  
      {"id": 14, "name": "판타지"}  
    \],  
    "reviewCount": 0,  
    "createdAt": "2025-06-07T03:00:00Z"  
  },  
  "message": "영화가 성공적으로 생성되었습니다",  
  "timestamp": "2025-06-07T03:00:00Z"  
}

## **4.3.9 영화 수정 (관리자)**

PUT /api/movies/{id}  
Content-Type: application/json  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: ADMIN, SUPER\_ADMIN

**Path Parameters:**

\- id: integer(required) \- 영화 ID

**Request Body:**

{  
  "title": "드래곤 길들이기 (수정됨)",  
  "overview": "수정된 줄거리...",  
  "releaseDate": "2025-06-06",  
  "genreIds": \[28, 10751, 14, 12\]  
}

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "id": 67,  
    "title": "드래곤 길들이기 (수정됨)",  
    "overview": "수정된 줄거리...",  
    "updatedAt": "2025-06-07T03:15:00Z"  
  },  
  "message": "영화가 성공적으로 수정되었습니다",  
  "timestamp": "2025-06-07T03:15:00Z"  
}

## **4.3.10 영화 삭제 (관리자)**

DELETE /api/movies/{id}  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: ADMIN, SUPER\_ADMIN

**Path Parameters:**

\- id: integer(required) \- 영화 ID

**Business Rules Validation:**

\- 관련된 리뷰, 별점, 시청 기록도 함께 삭제됨

\- 삭제된 데이터는 복구할 수 없음

**Response (204 No Content)**

**Response (409 Conflict):**

{  
  "success": false,  
  "error": {  
    "code": "MOVIE\_HAS\_DEPENDENCIES",  
    "message": "해당 영화에 연결된 데이터가 있습니다",  
    "detail": "리뷰 5개, 별점 25개, 시청 기록 150개가 함께 삭제됩니다."  
  },  
  "timestamp": "2025-06-07T03:30:00Z"  
}

## **4.3.11 영화 일괄 가져오기 (TMDB 동기화)**

POST /api/movies/sync/tmdb  
Content-Type: application/json  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: ADMIN, SUPER\_ADMIN

**Request Body:**

{  
  "category": "popular",  
  "page": 1,  
  "limit": 20,  
  "genreFilter": \[28, 12\],  
  "minVoteAverage": 6.0  
}

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "imported": 15,  
    "updated": 3,  
    "skipped": 2,  
    "failed": 0,  
    "details": \[  
      {  
        "tmdbId": 550,  
        "title": "Fight Club",  
        "status": "IMPORTED"  
      },  
      {  
        "tmdbId": 155,  
        "title": "The Dark Knight",  
        "status": "UPDATED"  
      }  
    \]  
  },  
  "message": "TMDB 영화 동기화가 완료되었습니다",  
  "timestamp": "2025-06-07T04:00:00Z"  
}

## **4.3.12 영화 메타데이터 새로고침**

PUT /api/movies/{id}/refresh  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: ADMIN, SUPER\_ADMIN

**Path Parameters:**

* id: integer(required) \- 영화 ID

**Description** TMDB에서 최신 메타데이터를 가져와 영화 정보를 업데이트합니다.

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "id": 67,  
    "title": "드래곤 길들이기",  
    "updatedFields": \[  
      "voteAverage",  
      "voteCount",  
      "overview"  
    \],  
    "lastRefreshed": "2025-06-07T04:15:00Z"  
  },  
  "message": "영화 메타데이터가 새로고침되었습니다",  
  "timestamp": "2025-06-07T04:15:00Z"  
}

## **4.3.13 영화 추천 알고리즘**

GET /api/movies/{id}/recommendations

**Path Parameters:**

* id: integer(required) \- 기준 영화 ID

**Query Parameters:**

* limit: integer (default: 10\) \- 추천 개수  
* algorithm: string (default: "collaborative") \- 추천 알고리즘 (collaborative, content\_based, hybrid)

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "baseMovie": {  
      "id": 67,  
      "title": "드래곤 길들이기"  
    },  
    "recommendations": \[  
      {  
        "id": 112,  
        "title": "반지의 제왕: 왕의 귀환",  
        "posterPath": "/n8BPIRqvj1SdTRND828ANXhmSng.jpg",  
        "similarity": 0.85,  
        "reason": "같은 판타지 장르, 높은 평점"  
      },  
      {  
        "id": 106,  
        "title": "센과 치히로의 행방불명",  
        "posterPath": "/aZuBfbR0PnCb2up7lqHDsgJlLjs.jpg",  
        "similarity": 0.78,  
        "reason": "가족 영화, 애니메이션"  
      }  
    \],  
    "algorithm": "collaborative",  
    "generatedAt": "2025-06-07T04:30:00Z"  
  },  
  "message": "영화 추천이 완료되었습니다",  
  "timestamp": "2025-06-07T04:30:00Z"  
}

## **4.3.14 개인 맞춤 영화 추천**

GET /api/movies/recommendations/personal  
Authorization: Bearer {JWT\_TOKEN}

**Query Parameters:**

\- limit: integer (default: 20\) \- 추천 개수

\- category: string (default: "mixed") \- 추천 카테고리 (mixed, similar\_taste, new\_genres, trending)

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "userId": 8,  
    "recommendations": \[  
      {  
        "category": "비슷한 취향",  
        "movies": \[  
          {  
            "id": 67,  
            "title": "드래곤 길들이기",  
            "posterPath": "/8vywrRg1wrY4fo7EqgrFmUJgchG.jpg",  
            "reason": "액션/판타지 장르를 좋아하시는군요\!",  
            "confidence": 0.92  
          }  
        \]  
      },  
      {  
        "category": "새로운 장르 도전",  
        "movies": \[  
          {  
            "id": 301,  
            "title": "라라랜드",  
            "posterPath": "/poster.jpg",  
            "reason": "뮤지컬 장르도 도전해보세요\!",  
            "confidence": 0.65  
          }  
        \]  
      }  
    \],  
    "userPreferences": {  
      "favoriteGenres": \["액션", "판타지", "가족"\],  
      "averageRating": 3.8,  
      "totalRatings": 25  
    }  
  },  
  "message": "개인 맞춤 추천이 완료되었습니다",  
  "timestamp": "2025-06-07T04:45:00Z"  
}

### **4.4 드라마 관리 API**

#### **4.4.1 드라마 목록 조회**

GET /api/dramas

Query Parameters:  
\- page: integer (default: 0\) \- 페이지 번호  
\- size: integer (default: 20, max: 100\) \- 페이지 크기  
\- sort: string (default: "combinedRating") \- 정렬 기준(combinedRating, firstAirDate, title)

{  
    "content": \[  
        {  
            "id": 78,  
            "tmdbId": 237349,  
            "title": "Ek Mahanayak \- Dr B R Ambedkar",  
            "posterPath": "/nUWsTE6fXf7tv02bI2pEdnUntjK.jpg",  
            "voteAverage": 10.0,  
            "voteCount": 1,  
            "combinedRating": 5.0,  
            "firstAirDate": "2019-12-17",  
            "genreNames": \[  
                "가족",  
                "드라마",  
                "Reality"  
            \],  
            "reviewCount": 0  
        },  
        {  
            "id": 192,  
            "tmdbId": 220150,  
            "title": "포켓몬스터",  
            "posterPath": "/bHBf7URUEZGUPORE6dp1hDtSUt9.jpg",  
            "voteAverage": 9.1,  
            "voteCount": 83,  
            "combinedRating": 4.55,  
            "firstAirDate": "2023-04-14",  
            "genreNames": \[\],  
            "reviewCount": 0  
        },  
    \],  
    "currentPage": 0,  
    "totalPages": 12,  
    "totalElements": 236,  
    "size": 20,  
    "hasNext": true,  
    "hasPrevious": false,  
    "isFirst": true,  
    "isLast": false  
}

#### **4.4.2 드라마 상세정보 조회**

GET /api/dramas/{id}

Path Parameters:  
\- id: integer (required) \- 영화 ID

Response (200 OK):  
{  
    "id": 192,  
    "tmdbId": 220150,  
    "title": "포켓몬스터",  
    "overview": "라이징 볼트태클즈에 가입한 신예 포켓몬 트레이너 리코와 로드. 베일에 싸인 조직, 익스플로러즈의 음모를 막으며 포켓몬의 세계에서 종횡무진 모험을 펼친다.",  
    "firstAirDate": "2023-04-14",  
    "posterPath": "/bHBf7URUEZGUPORE6dp1hDtSUt9.jpg",  
    "voteAverage": 9.1,  
    "voteCount": 83,  
    "combinedRating": 4.55,  
    "genres": \[\],  
    "reviewCount": 0  
}

#### **4.4.3 드라마 제목 검색**

GET /api/dramas/search

Query Parameters:  
\- title: String (required) \- 검색할 제목  
\- page: integer (default: 0\) \- 페이지 번호  
\- size: integer (default: 20, max: 100\) \- 페이지 크기  
\- sort: string (default: "combinedRating") \- 정렬 기준(combinedRating, releaseDate, title)

Response (200 OK):  
{  
    "content": \[  
        {  
            "id": 237,  
            "tmdbId": 204082,  
            "title": "오징어 게임: 더 챌린지",  
            "posterPath": "/8AfJrS6yxk9X0be0WBdTzDOhy1A.jpg",  
            "voteAverage": 6.335,  
            "voteCount": 511,  
            "combinedRating": 3.17,  
            "firstAirDate": "2023-11-22",  
            "genreNames": \[  
                "Reality"  
            \],  
            "reviewCount": 0  
        },  
        {  
            "id": 238,  
            "tmdbId": 96105,  
            "title": "이태리 오징어순대집",  
            "posterPath": "/61kq0hOmC0WPT6qXaDX1D3wyjDY.jpg",  
            "voteAverage": 0.0,  
            "voteCount": 0,  
            "combinedRating": 0.0,  
            "firstAirDate": "2019-12-02",  
            "genreNames": \[  
                "Reality"  
            \],  
            "reviewCount": 0  
        },  
        {  
            "id": 239,  
            "tmdbId": 35447,  
            "title": "침략\! 오징어 소녀",  
            "posterPath": "/2HLbnsCymhJMuy53PMwo7icxd3T.jpg",  
            "voteAverage": 6.731,  
            "voteCount": 26,  
            "combinedRating": 3.37,  
            "firstAirDate": "2010-10-05",  
            "genreNames": \[  
                "애니메이션",  
                "코미디",  
                "Sci-Fi & Fantasy"  
            \],  
            "reviewCount": 0  
        },  
        {  
            "id": 240,  
            "tmdbId": 93405,  
            "title": "오징어 게임",  
            "posterPath": "/yACIAqAkSLkX4coHafpyLWAtQjw.jpg",  
            "voteAverage": 7.9,  
            "voteCount": 15827,  
            "combinedRating": 3.95,  
            "firstAirDate": "2021-09-17",  
            "genreNames": \[  
                "Action & Adventure",  
                "미스터리",  
                "드라마"  
            \],  
            "reviewCount": 0  
        }  
    \],  
    "currentPage": 0,  
    "totalPages": 1,  
    "totalElements": 4,  
    "size": 20,  
    "hasNext": false,  
    "hasPrevious": false,  
    "isFirst": true,  
    "isLast": true  
}

#### **4.4.4 드라마 세부 검색**

POST /api/dramas/search

Query Parameters:  
\- page: integer (default: 0\) \- 페이지 번호  
\- size: integer (default: 20, max: 100\) \- 페이지 크기

Request Body:  
{   
"title": "짱구",   
"genreIds": \[\],   
"minRating": 3.0,   
"maxRating": 5.0,   
"sortBy": "rating",   
"sortDirection": "desc"   
}

Response (200 OK):  
{  
    "content": \[  
        {  
            "id": 185,  
            "tmdbId": 30623,  
            "title": "짱구는 못말려",  
            "posterPath": "/1mQZZjZdFXXSUyGpEuPb3PThiE6.jpg",  
            "voteAverage": 7.8,  
            "voteCount": 100,  
            "combinedRating": 3.9,  
            "firstAirDate": "1992-04-13",  
            "genreNames": \[\],  
            "reviewCount": 0  
        },  
        {  
            "id": 187,  
            "tmdbId": 67324,  
            "title": "짱구는 못말려 외전",  
            "posterPath": "/zL7zV68jIpq2IBOtlK38mlwfgI9.jpg",  
            "voteAverage": 7.8,  
            "voteCount": 5,  
            "combinedRating": 3.9,  
            "firstAirDate": "2016-08-03",  
            "genreNames": \[\],  
            "reviewCount": 0  
        },  
        {  
            "id": 186,  
            "tmdbId": 209168,  
            "title": "짱구는 못말려: 신맨",  
            "posterPath": "/s6Jh3ONAi8KMI6vetq5aUfkRJyR.jpg",  
            "voteAverage": 7.0,  
            "voteCount": 1,  
            "combinedRating": 3.5,  
            "firstAirDate": "2010-11-26",  
            "genreNames": \[\],  
            "reviewCount": 0  
        }  
    \],  
    "currentPage": 0,  
    "totalPages": 1,  
    "totalElements": 3,  
    "size": 20,  
    "hasNext": false,  
    "hasPrevious": false,  
    "isFirst": true,  
    "isLast": true  
}

## **4.4.5 드라마 생성 (관리자)**

POST /api/dramas  
Content-Type: application/json  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: ADMIN, SUPER\_ADMIN

## **4.4.6 드라마 수정 (관리자)**

PUT /api/dramas/{id}  
Content-Type: application/json  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: ADMIN, SUPER\_ADMIN

## **4.4.7 드라마 삭제 (관리자)**

DELETE /api/dramas/{id}  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: ADMIN, SUPER\_ADMIN

### **4.5 게시글 관리 API(Post)**

#### **4.5.1 콘텐츠 별 게시글 목록 조회**

GET /api/posts/content?type={type}\&id={id}

Content-Type: application/json  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: USER, ADMIN

Query Parameters:  
\-type: ContentType \- 콘텐츠 타입 (MOVIE 또는 DRAMA, ENUM)  
\-id: integer \- 콘텐츠 ID (MOVIE ID 또는 DRAMA ID)

Business Rules Validation:  
\- contentType이 MOVIE 또는 DRAMA 인지 확인  
\- contentId에 해당하는 콘텐츠가 존재하는지 확인

Response (200 Ok):  
{  
  \[  
    {  
        "id": 8,  
        "code": "P25060500004",  
        "title": "제목 수정",  
        "body": "내용 수정",  
        "contentType": "MOVIE",  
        "contentId": 1,  
        "user": {  
            "userId": 7,  
            "name": "정재운"  
        }  
    }  
\]

}

Response (400 Bad Request):  
{  
  {  
    "success": **false**,  
    "error": {  
        "code": "VALID\_001",  
        "message": "요청 파라미터 타입이 올바르지 않습니다.",  
        "detail": "요청 파라미터 'type'에 대해 'MOVI' 값을 'ContentType'(으)로 변환할 수         없습니다."  
    },  
    "timestamp": "2025-06-05T15:53:24.5733009"  
}

}

Response (404 Not Found):  
{  
  {  
    "success": **false**,  
    "error": {  
        "code": "CT002",  
        "message": "컨텐츠를 찾을 수 없습니다."  
    },  
    "timestamp": "2025-06-05T15:55:20.9121964"  
  }

}

#### **4.5.2 게시글 단건 조회**

#### GET /api/posts/{code}

Content-Type: application/json  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: USER, ADMIN

Path Parameters:  
\- code: string(required) \- 조회할 게시글의 고유 코드(“P”+ yymmdd+ 순서)

Business Rules Validation:  
\-같은 code 값을 가진 post가 존재하는지 확인

Response (200 OK):  
{  
  {  
    "id": 8,  
    "code": "P25060500004",  
    "title": "제목 수정",  
    "body": "내용 수정",  
    "contentType": "MOVIE",  
    "contentId": 1,  
    "createdAt": "2025-06-05T13:14:32.042222",  
    "updatedAt": "2025-06-05T15:34:17.302623",  
    "user": {  
        "userId": 7,  
        "name": "정재운"  
    }  
}

}

Response (404 Not Found):  
{  
  {  
    "success": **false**,  
    "error": {  
        "code": "POST\_001",  
        "message": "게시글을 찾을 수 없습니다"  
    },  
    "timestamp": "2025-06-05T16:00:21.4391343"  
   }

}

**4.5.3 게시글 생성**

POST /api/posts

Content-Type: application/json  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: USER, ADMIN

Business Rules Validation:  
\-인증된 user인지 확인  
\-필수 입력값(title, body) 확인

Request Body:  
{  
  "title": "미션 임파서블 영화 후기",  
  "body": "추천하는 영화 꼭 보세요\!",  
  //"image": 이미지는 선택사항  
  "contentType": "MOVIE",  
  "contentId": 7,  
}

Response (200 OK):  
{  
   {  
    "id": 10,  
    "code": "P25060500005",  
    "title": "미션 임파서블 영화 후기",  
    "body": "추천하는 영화 꼭 보세요\!",  
    "contentType": "MOVIE",  
    "contentId": 7,  
    "createdAt": "2025-06-05T16:13:03.4653163",  
    "updatedAt": "2025-06-05T16:13:03.4653163",  
    "user": {  
        "userId": 7,  
        "name": "정재운"  
    }  
}  
Response (400 Bad Request):

{  
    "success": **false**,  
    "error": {  
        "code": "VALID\_001",  
        "message": "입력값이 올바르지 않습니다",  
        "fieldErrors": \[  
            {  
                "field": "body",  
                "message": "내용은 필수 입력사항 입니다."  
            },  
            {  
                "field": "title",  
                "message": "제목은 필수 입력사항 입니다."  
            }  
        \]  
    },  
    "timestamp": "2025-06-05T16:14:21.7111803"  
}  
Response (401 Unauthorized):

{  
    "path": "/api/posts",  
    "error": "Unauthorized",  
    "message": "인증 정보가 없습니다. 유효한 JWT 토큰을 포함해 주세요.",  
    "status": 401  
}  
    
}

**4.5.4 게시글 수정**

PUT /api/posts/{code}

Content-Type: application/json  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: USER, ADMIN

Path Parameters:  
\- code: string(required) \- 수정할 post의 고유 code값

Business Rules Validation:  
\-인증된 user인지 확인  
\-post를 작성한 user인지 확인  
\-필수 입력값(title, body) 확인  
\-같은 code 값을 가진 post가 존재하는지 확인

Request Body:  
{  
  "title": "미션 임파서블 영화 후기 수정",  
  "body": "그냥 그래요",  
  //"image": 이미지는 선택사항,  
  //”deleteExistingImage”: 기존 이미지 삭제 원할 때 true로   
 }

Response (200 OK):  
{  
  {  
    "id": 10,  
    "code": "P25060500005",  
    "title": "미션 임파서블 영화 후기 수정",  
    "body": "그냥 그래요",  
    "contentType": "MOVIE",  
    "contentId": 7,  
    "createdAt": "2025-06-05T16:13:03.465316",  
    "updatedAt": "2025-06-05T16:13:03.465316",  
    "user": {  
        "userId": 7,  
        "name": "정재운"  
    }  
}

}

Response (400 Bad Request):  
{

    "success": **false**,

    "error": {

        "code": "VALID\_001",

        "message": "입력값이 올바르지 않습니다",

        "fieldErrors": \[

            {

                "field": "body",

                "message": "내용은 필수 입력사항 입니다."

            },

            {

                "field": "title",

                "message": "제목은 필수 입력사항 입니다."

            }

        \]

    },

    "timestamp": "2025-06-05T17:29:10.5740473"

}

Response (401 Unauthorized):  
{

    "path": "/api/posts/P25060500007",

    "error": "Unauthorized",

    "message": "인증 정보가 없습니다. 유효한 JWT 토큰을 포함해 주세요.",

    "status": 401

}

Response (403 Forbidden):  
{

    "success": **false**,

    "error": {

        "code": "POST\_003",

        "message": "작성자가 아닙니다",

        "detail": "게시글의 작성자만 수정할 수 있습니다."

    },

    "timestamp": "2025-06-05T17:24:55.5267008"

}

Response (404 Not Found):  
{

    "success": **false**,

    "error": {

        "code": "POST\_001",

        "message": "게시글을 찾을 수 없습니다"

    },

    "timestamp": "2025-06-05T17:35:08.3051846"

}

**4.5.5 게시글 삭제**

DELETE /api/posts/{code}

Content-Type: application/json  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: USER, ADMIN

Path Parameters:  
\- code: string(required) \- 삭제할 post의 고유 code값

Business Rules Validation:  
\-인증된 user인지 확인  
\-post를 작성한 user인지 확인  
\-같은 code 값을 가진 post가 존재하는지 확인

Response (204 No Content):

Response (401 Unauthorized):  
{  
    "path": "/api/posts/P25060500007",  
    "error": "Unauthorized",  
    "message": "인증 정보가 없습니다. 유효한 JWT 토큰을 포함해 주세요.",  
    "status": 401  
}

Response (403 Forbidden):  
{

    "success": **false**,

    "error": {

        "code": "POST\_003",

        "message": "작성자가 아닙니다",

        "detail": "게시글의 작성자만 삭제할 수 있습니다."

    },

    "timestamp": "2025-06-05T17:56:06.7620771"

}

Response (404 Not Found):  
{

    "success": **false**,

    "error": {

        "code": "POST\_001",

        "message": "게시글을 찾을 수 없습니다"

    },

    "timestamp": "2025-06-05T17:57:18.3166449"

}

**4.5.6 게시글과 연결된 콘텐츠 일부 정보 조회**

GET /api/posts/content/{contentType}/{contentId}

Content-Type: application/json  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: USER, ADMIN

Path Parameters:  
\- contentType: string(required)- post와 연결된 콘텐츠 타입(MOVIE 또는 DRAMA,             ENUM)  
\- contentId: integer(required)- post와 연결된 콘텐츠 Id(MOVIE ID 또는 DRAMA ID)

Business Rules Validation:  
\- contentType이 MOVIE 또는 DRAMA 인지 확인  
\- contentId에 해당하는 콘텐츠가 존재하는지 확인

Response (200 Ok):  
{  
    "id": 7,  
    "title": "미션 임파서블: 파이널 레코닝",  
    "posterPath": "/5Zxdorl5TharlI9S47YxoKzGCsi.jpg"  
}

Response (400 Bad Request):  
{  
    "success": **false**,  
    "error": {  
        "code": "VALID\_001",  
        "message": "요청 파라미터 타입이 올바르지 않습니다.",  
        "detail": "요청 파라미터 'contentType'에 대해 'MOVI' 값을 'ContentType'(으)로 변환할 수 없습니다."  
    },  
    "timestamp": "2025-06-06T14:06:54.9185753"  
}  
Response (404 Not Found):

{  
    "success": **false**,  
    "error": {  
        "code": "CT002",  
        "message": "컨텐츠를 찾을 수 없습니다.",  
        "detail": "Movie를 찾지 못했습니다."  
    },  
    "timestamp": "2025-06-06T14:07:31.9514777"  
}

### **4.6 댓글 관리 API(Comment)**

#### **4.6.1 게시글 별 댓글들 조회**

GET /api/posts/{postCode}/comments

Content-Type: application/json  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: USER, ADMIN

Path Parameters:  
\- postCode: string(required)- 조회할 게시글의 고유 코드(댓글과 연결된 게시글)

Business Rules Validation:  
\-같은 postCode 값을 가진 post가 존재하는지 확인

Response (200 Ok):  
//11번 댓글 안 대댓글 12 확인 가능, 12번은 대댓글  
\[  
    {  
        "id": 11,  
        "content": "그런가요? 저는 재밌었는데..",  
        "childComments": \[  
            {  
                "id": 12,  
                "content": "맞아요\! 저도 재미있게 봤어요\!",  
                "parentCommentId": 11,  
                "childComments": \[\],  
                "createdAt": "2025-06-06T14:28:29.809228",  
                "updatedAt": "2025-06-06T14:28:29.809228",  
                "user": {  
                    "userId": 7,  
                    "name": "정재운"  
                },  
                "postId": 10  
            }  
        \],  
        "createdAt": "2025-06-06T14:27:12.424082",  
        "updatedAt": "2025-06-06T14:27:12.424082",  
        "user": {  
            "userId": 7,  
            "name": "정재운"  
        },  
        "postId": 10  
    },  
    {  
        "id": 12,  
        "content": "맞아요\! 저도 재미있게 봤어요\!",  
        "parentCommentId": 11,  
        "childComments": \[\],  
        "createdAt": "2025-06-06T14:28:29.809228",  
        "updatedAt": "2025-06-06T14:28:29.809228",  
        "user": {  
            "userId": 7,  
            "name": "정재운"  
        },  
        "postId": 10  
    }  
\]  
Response (404 Not Found):  
{  
    "success": **false**,  
    "error": {  
        "code": "POST\_001",  
        "message": "게시글을 찾을 수 없습니다"  
    },  
    "timestamp": "2025-06-06T14:35:04.8633145"  
}

**4.6.2 댓글 생성**

POST /api/posts/{postCode}/comments

Content-Type: application/json  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: USER, ADMIN

Path Parameters:  
\- postCode: string(required)- 조회할 게시글의 고유 코드(댓글과 연결된 게시글)

Business Rules Validation:  
\-필수 입력값(content) 확인  
\-인증된 user인지 확인  
\-같은 postCode 값을 가진 post가 존재하는지 확인

Request Body:  
{  
  "content": "댓글 내용",  
  "parentId"(부모 Id): ""(기본 댓글) or 3(3번 댓글의 대댓글 일 때)  
}

Response (200 OK):  
//댓글일 때  
{  
    "id": 11,  
    "content": "그런가요? 저는 재밌었는데..",  
    "childComments": \[\],  
    "createdAt": "2025-06-06T14:27:12.4240829",  
    "updatedAt": "2025-06-06T14:27:12.4240829",  
    "user": {  
        "userId": 7,  
        "name": "정재운"  
    },  
    "postId": 10  
}  
//대댓글일 때  
{  
    "id": 12,  
    "content": "맞아요\! 저도 재미있게 봤어요\!",  
    "parentCommentId": 11,  
    "childComments": \[\],  
    "createdAt": "2025-06-06T14:28:29.8092287",  
    "updatedAt": "2025-06-06T14:28:29.8092287",  
    "user": {  
        "userId": 7,  
        "name": "정재운"  
    },  
    "postId": 10  
}

Response (400 Bad Request):  
{  
    "success": **false**,  
    "error": {  
        "code": "VALID\_001",  
        "message": "입력값이 올바르지 않습니다",  
        "fieldErrors": \[  
            {  
                "field": "content",  
                "message": "내용은 필수 입력사항 입니다.",  
                "rejectedValue": ""  
            }  
        \]  
    },  
    "timestamp": "2025-06-06T14:30:02.3867229"  
}

Response (401 Unauthorized):

{  
    "path": "/api/posts/P25060500005/comments",  
    "error": "Unauthorized",  
    "message": "인증 정보가 없습니다. 유효한 JWT 토큰을 포함해 주세요.",  
    "status": 401  
}

Response (404 Not Found):  
{  
    "success": **false**,  
    "error": {  
        "code": "POST\_001",  
        "message": "게시글을 찾을 수 없습니다"  
    },  
    "timestamp": "2025-06-06T14:31:15.8768499"  
}

//부모 댓글을 찾을 수 없을 때  
{  
    "success": **false**,  
    "error": {  
        "code": "COMMENT\_001",  
        "message": "댓글을 찾을 수 없습니다"  
    },  
    "timestamp": "2025-06-06T14:32:22.0091436"  
}

**4.6.3 댓글 수정**

PUT /api/comments/{id}  
Content-Type: application/json  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: USER, ADMIN

Path Parameters:  
\- id : integer(required)- 수정할 댓글의 고유 Id

Business Rules Validation:  
\-필수 입력값(content) 확인  
\-인증된 user인지 확인  
\-인증된 user가 수정할 댓글의 작성자인지 확인  
\-같은 id 값을 가진 댓글이 존재하는지 확인

Request Body:  
{  
  "content": "수정할 댓글 내용"  
}

Response (200 OK):  
{  
    "id": 12,  
    "content": "대댓글 내용 수정입니다.",  
    "parentCommentId": 11,  
    "childComments": \[\],  
    "createdAt": "2025-06-06T14:28:29.809228",  
    "updatedAt": "2025-06-06T14:28:29.809228",  
    "user": {  
        "userId": 7,  
        "name": "정재운"  
    },  
    "postId": 10  
}  
Response (400 Bad Request):  
{  
    "success": **false**,  
    "error": {  
        "code": "VALID\_001",  
        "message": "입력값이 올바르지 않습니다",  
        "fieldErrors": \[  
            {  
                "field": "content",  
                "message": "내용은 필수 입력사항 입니다.",  
                "rejectedValue": ""  
            }  
        \]  
    },  
    "timestamp": "2025-06-06T14:44:15.4879732"  
}  
Response (401 Bad Unauthorized):

{  
    "path": "/api/comments/12",  
    "error": "Unauthorized",  
    "message": "인증 정보가 없습니다. 유효한 JWT 토큰을 포함해 주세요.",  
    "status": 401  
}  
Response (403 Forbidden):  
{  
    "success": **false**,  
    "error": {  
        "code": "COMMENT\_002",  
        "message": "작성자가 아닙니다",  
        "detail": "댓글의 작성자만 수정할 수 있습니다."  
    },  
    "timestamp": "2025-06-06T14:47:10.6411052"  
}

Response (404 Not Found):  
{  
    "success": **false**,  
    "error": {  
        "code": "COMMENT\_001",  
        "message": "댓글을 찾을 수 없습니다"  
    },  
    "timestamp": "2025-06-06T14:43:42.1738521"  
}

**4.6.4 댓글 삭제**

DELETE /api/comments/{id}  
Content-Type: application/json  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: USER, ADMIN

Path Parameters:  
\- id : integer(required)- 삭제할 댓글의 고유 Id

Business Rules Validation:  
\-인증된 user인지 확인  
\-인증된 user가 삭제할 댓글의 작성자인지 확인  
\-같은 id 값을 가진 댓글이 존재하는지 확인

Response (204 No Content):  
//댓글 삭제 시 대댓글도 함께 삭제됨

Response (401 Unauthorized):  
{  
    "path": "/api/comments/12",  
    "error": "Unauthorized",  
    "message": "인증 정보가 없습니다. 유효한 JWT 토큰을 포함해 주세요.",  
    "status": 401  
}

Response (403 Forbidden):  
{  
    "success": **false**,  
    "error": {  
        "code": "COMMENT\_002",  
        "message": "작성자가 아닙니다",  
        "detail": "댓글의 작성자만 삭제할 수 있습니다."  
    },  
    "timestamp": "2025-06-06T14:55:22.3356293"  
}

Response (404 Not Found):

{  
    "success": **false**,  
    "error": {  
        "code": "COMMENT\_001",  
        "message": "댓글을 찾을 수 없습니다"  
    },  
    "timestamp": "2025-06-06T14:54:12.129038"  
}

# **4.7 리뷰 관리 API**

## **4.7.1 리뷰+별점 생성**

POST /api/reviews  
Content-Type: application/json  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: MEMBER, LIBRARIAN, ADMIN, SUPER\_ADMIN

**Description** 새로운 리뷰와 별점을 동시에 작성하는 API입니다.

**Business Rules Validation:**

* 인증된 user인지 확인  
* 필수 입력값(content, score) 확인  
* movieId 또는 dramaId 중 하나만 입력 가능  
* 별점은 0.5\~5점 사이  
* 같은 콘텐츠에 대해 중복 리뷰 작성 불가

**Request Body:**

{  
  "content": "정말 재미있는 영화였습니다\! 스토리도 좋고 연기도 훌륭했어요.",  
  "score": 4.5,  
  "movieId": 67,  
  "dramaId": null  
}

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "id": 1,  
    "content": "정말 재미있는 영화였습니다\! 스토리도 좋고 연기도 훌륭했어요.",  
    "score": 4.5,  
    "username": "홍길동",  
    "userId": 1,  
    "movieId": 67,  
    "movieTitle": "드래곤 길들이기",  
    "moviePosterPath": "/8vywrRg1wrY4fo7EqgrFmUJgchG.jpg",  
    "contentType": "MOVIE",  
    "createdAt": "2025-06-06T10:30:00Z",  
    "updatedAt": "2025-06-06T10:30:00Z"  
  },  
  "message": "리뷰가 성공적으로 등록되었습니다",  
  "timestamp": "2025-06-06T10:30:00Z"  
}

**Response (400 Bad Request):**

{  
  "success": false,  
  "error": {  
    "code": "VALIDATION\_ERROR",  
    "message": "입력값이 올바르지 않습니다",  
    "fieldErrors": \[  
      {  
        "field": "content",  
        "message": "리뷰 내용은 필수 입력사항입니다.",  
        "rejectedValue": ""  
      },  
      {  
        "field": "score",  
        "message": "별점은 0.5\~5점 사이여야 합니다.",  
        "rejectedValue": 6  
      }  
    \]  
  },  
  "timestamp": "2025-06-06T10:30:00Z"  
}

**Response (409 Conflict):**

{  
  "success": false,  
  "error": {  
    "code": "REVIEW\_ALREADY\_EXISTS",  
    "message": "이미 해당 콘텐츠에 대한 리뷰가 존재합니다",  
    "detail": "기존 리뷰를 수정하거나 삭제 후 다시 작성해주세요."  
  },  
  "timestamp": "2025-06-06T10:30:00Z"  
}

## **4.7.2 리뷰+별점 수정**

PUT /api/reviews/{reviewId}  
Content-Type: application/json  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: MEMBER (본인만), LIBRARIAN, ADMIN, SUPER\_ADMIN

**Path Parameters:**

* reviewId: integer(required) \- 수정할 리뷰의 고유 ID

**Business Rules Validation:**

* 인증된 user인지 확인  
* 리뷰 작성자인지 확인  
* 필수 입력값(content, score) 확인  
* 리뷰가 존재하는지 확인

**Request Body:**

{  
  "content": "다시 보니 더 좋은 영화네요. 평점을 올립니다.",  
  "score": 5.0  
}

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "id": 1,  
    "content": "다시 보니 더 좋은 영화네요. 평점을 올립니다.",  
    "score": 5.0,  
    "username": "홍길동",  
    "userId": 1,  
    "movieId": 67,  
    "movieTitle": "드래곤 길들이기",  
    "moviePosterPath": "/8vywrRg1wrY4fo7EqgrFmUJgchG.jpg",  
    "contentType": "MOVIE",  
    "createdAt": "2025-06-06T10:30:00Z",  
    "updatedAt": "2025-06-06T11:15:00Z"  
  },  
  "message": "리뷰가 성공적으로 수정되었습니다",  
  "timestamp": "2025-06-06T11:15:00Z"  
}

**Response (403 Forbidden):**

{  
  "success": false,  
  "error": {  
    "code": "REVIEW\_PERMISSION\_DENIED",  
    "message": "리뷰 작성자만 수정할 수 있습니다"  
  },  
  "timestamp": "2025-06-06T11:15:00Z"  
}

## **4.7.3 리뷰 삭제**

DELETE /api/reviews/{reviewId}  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: MEMBER (본인만), LIBRARIAN, ADMIN, SUPER\_ADMIN

**Path Parameters:**

* reviewId: integer(required) \- 삭제할 리뷰의 고유 ID

**Business Rules Validation:**

* 인증된 user인지 확인  
* 리뷰 작성자인지 확인  
* 리뷰가 존재하는지 확인

**Response (204 No Content)**

**Response (403 Forbidden):**

{  
  "success": false,  
  "error": {  
    "code": "REVIEW\_PERMISSION\_DENIED",  
    "message": "리뷰 작성자만 삭제할 수 있습니다"  
  },  
  "timestamp": "2025-06-06T11:20:00Z"  
}

## **4.7.4 리뷰 상세 조회**

GET /api/reviews/{reviewId}

**Path Parameters:**

* reviewId: integer(required) \- 조회할 리뷰의 고유 ID

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "id": 1,  
    "content": "정말 재미있는 영화였습니다\! 스토리도 좋고 연기도 훌륭했어요.",  
    "score": 4.5,  
    "username": "홍길동",  
    "userId": 1,  
    "movieId": 67,  
    "movieTitle": "드래곤 길들이기",  
    "moviePosterPath": "/8vywrRg1wrY4fo7EqgrFmUJgchG.jpg",  
    "contentType": "MOVIE",  
    "createdAt": "2025-06-06T10:30:00Z",  
    "updatedAt": "2025-06-06T10:30:00Z"  
  },  
  "message": "리뷰 조회가 완료되었습니다",  
  "timestamp": "2025-06-06T12:00:00Z"  
}

## **4.7.5 영화 리뷰 목록 조회**

GET /api/reviews/movies/{movieId}

**Path Parameters:**

* movieId: integer(required) \- 영화 ID

**Query Parameters:**

* page: integer (default: 0\) \- 페이지 번호  
* size: integer (default: 10\) \- 페이지 크기

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "content": \[  
      {  
        "id": 1,  
        "content": "정말 재미있는 영화였습니다\!",  
        "score": 4.5,  
        "username": "홍길동",  
        "userId": 1,  
        "createdAt": "2025-06-06T10:30:00Z",  
        "updatedAt": "2025-06-06T10:30:00Z"  
      },  
      {  
        "id": 2,  
        "content": "아이들과 함께 보기 좋은 영화예요.",  
        "score": 4.0,  
        "username": "김영희",  
        "userId": 2,  
        "createdAt": "2025-06-06T09:15:00Z",  
        "updatedAt": "2025-06-06T09:15:00Z"  
      }  
    \],  
    "pageable": {  
      "pageNumber": 0,  
      "pageSize": 10,  
      "sort": {  
        "empty": false,  
        "sorted": true,  
        "unsorted": false  
      }  
    },  
    "totalElements": 25,  
    "totalPages": 3,  
    "last": false,  
    "first": true,  
    "numberOfElements": 10  
  },  
  "message": "영화 리뷰 목록 조회가 완료되었습니다",  
  "timestamp": "2025-06-06T12:00:00Z"  
}

## **4.7.6 드라마 리뷰 목록 조회**

GET /api/reviews/dramas/{dramaId}

**Path Parameters:**

* dramaId: integer(required) \- 드라마 ID

**Query Parameters:**

* page: integer (default: 0\) \- 페이지 번호  
* size: integer (default: 10\) \- 페이지 크기

*응답 형식은 4.6.5와 동일*

## **4.7.7 사용자 리뷰 목록 조회**

GET /api/reviews/users/{userId}

**Path Parameters:**

* userId: integer(required) \- 사용자 ID

**Query Parameters:**

* page: integer (default: 0\) \- 페이지 번호  
* size: integer (default: 10\) \- 페이지 크기

## **4.6.8 내 리뷰 목록 조회**

GET /api/reviews/my  
Authorization: Bearer {JWT\_TOKEN}

**Query Parameters:**

* page: integer (default: 0\) \- 페이지 번호  
* size: integer (default: 10\) \- 페이지 크기

## **4.7.9 최신 리뷰 목록 조회**

GET /api/reviews/latest

**Query Parameters:**

* page: integer (default: 0\) \- 페이지 번호  
* size: integer (default: 20\) \- 페이지 크기

**Description** 최근에 작성된 리뷰들을 시간순으로 조회합니다.

## **4.7.10 영화 리뷰 개수 조회**

GET /api/reviews/movies/{movieId}/count

**Path Parameters:**

* movieId: integer(required) \- 영화 ID

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "count": 25  
  },  
  "message": "리뷰 개수 조회가 완료되었습니다",  
  "timestamp": "2025-06-06T12:00:00Z"  
}

# **4.8 별점 관리 API (레거시 지원)**

## **4.8.1 별점만 등록/수정 (레거시)**

POST /api/ratings  
Content-Type: application/json  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: MEMBER, LIBRARIAN, ADMIN, SUPER\_ADMIN

**Description** 리뷰 없이 별점만 등록하거나 수정하는 레거시 API입니다. 이미 리뷰가 존재하는 경우 사용할 수 없습니다.

**Business Rules Validation:**

* 별점은 0.5\~5점 사이  
* movieId, dramaId 중 하나만 입력 가능  
* 이미 리뷰가 존재하는 경우 등록 불가  
* 인증된 사용자인지 확인

**Request Body:**

{  
  "score": 4.0,  
  "movieId": 67,  
  "dramaId": null  
}

**Response (201 Created):**

{  
  "success": true,  
  "data": {  
    "id": 9,  
    "score": 4.0,  
    "username": "임종섭",  
    "userId": 8,  
    "movieId": 67,  
    "movieTitle": "드래곤 길들이기",  
    "moviePosterPath": "/8vywrRg1wrY4fo7EqgrFmUJgchG.jpg",  
    "contentType": "MOVIE",  
    "createdAt": "2025-06-06 21:28:52",  
    "updatedAt": "2025-06-06 21:28:52"  
  },  
  "message": "별점이 성공적으로 등록되었습니다",  
  "timestamp": "2025-06-06T21:28:52Z"  
}

**Response (409 Conflict):**

{  
  "success": false,  
  "error": {  
    "code": "REVIEW\_ALREADY\_EXISTS",  
    "message": "이미 해당 콘텐츠에 대한 리뷰가 존재합니다",  
    "detail": "리뷰가 있는 경우 별점만 따로 등록할 수 없습니다. 리뷰를 수정해주세요."  
  },  
  "timestamp": "2025-06-06T21:28:52Z"  
}

## **4.8.2 별점 삭제**

DELETE /api/ratings/{ratingId}  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: MEMBER (본인만), LIBRARIAN, ADMIN, SUPER\_ADMIN

**Path Parameters:**

* ratingId: integer(required) \- 별점 ID

**Business Rules Validation:**

* 인증된 사용자인지 확인  
* 별점 작성자인지 확인  
* 별점이 존재하는지 확인

**Response (204 No Content)**

**Response (403 Forbidden):**

{  
  "success": false,  
  "error": {  
    "code": "RATING\_PERMISSION\_DENIED",  
    "message": "별점 작성자만 삭제할 수 있습니다"  
  },  
  "timestamp": "2025-06-06T21:30:00Z"  
}

## **4.8.3 내 영화 별점 조회**

GET /api/ratings/my/movies/{movieId}  
Authorization: Bearer {JWT\_TOKEN}

**Path Parameters:**

* movieId: integer(required) \- 영화 ID

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "id": 9,  
    "score": 4.0,  
    "username": "임종섭",  
    "userId": 8,  
    "movieId": 67,  
    "movieTitle": "드래곤 길들이기",  
    "moviePosterPath": "/8vywrRg1wrY4fo7EqgrFmUJgchG.jpg",  
    "contentType": "MOVIE",  
    "createdAt": "2025-06-06 21:28:52",  
    "updatedAt": "2025-06-06 21:28:52"  
  },  
  "message": "별점 조회가 완료되었습니다",  
  "timestamp": "2025-06-06T21:30:00Z"  
}

**Response (404 Not Found):**

{  
  "success": false,  
  "error": {  
    "code": "RATING\_NOT\_FOUND",  
    "message": "해당 영화에 대한 별점을 찾을 수 없습니다"  
  },  
  "timestamp": "2025-06-06T21:30:00Z"  
}

## **4.8.4 내 드라마 별점 조회**

GET /api/ratings/my/dramas/{dramaId}  
Authorization: Bearer {JWT\_TOKEN}

**Path Parameters:**

* dramaId: integer(required) \- 드라마 ID

*응답 형식은 4.7.3과 동일*

## **4.8.5 영화 평균 별점 조회**

GET /api/ratings/movies/{movieId}/average

**Path Parameters:**

* movieId: integer(required) \- 영화 ID

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "averageScore": 4.2,  
    "totalCount": 150,  
    "movieId": 67,  
    "movieTitle": "드래곤 길들이기"  
  },  
  "message": "영화 평균 별점 조회가 완료되었습니다",  
  "timestamp": "2025-06-06T21:30:00Z"  
}

## **4.8.6 드라마 평균 별점 조회**

GET /api/ratings/dramas/{dramaId}/average

**Path Parameters:**

* dramaId: integer(required) \- 드라마 ID

*응답 형식은 4.7.5와 동일*

## **4.8.7 영화 별점 목록 조회**

GET /api/ratings/movies/{movieId}

**Path Parameters:**

* movieId: integer(required) \- 영화 ID

**Query Parameters:**

* page: integer (default: 0\) \- 페이지 번호  
* size: integer (default: 10\) \- 페이지 크기

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "content": \[  
      {  
        "id": 8,  
        "score": 4.0,  
        "username": "임종섭",  
        "userId": 8,  
        "createdAt": "2025-06-06 21:27:30",  
        "updatedAt": "2025-06-06 21:27:30"  
      },  
      {  
        "id": 5,  
        "score": 3.5,  
        "username": "김철수",  
        "userId": 5,  
        "createdAt": "2025-06-06 17:16:51",  
        "updatedAt": "2025-06-06 17:16:51"  
      }  
    \],  
    "pageable": {  
      "pageNumber": 0,  
      "pageSize": 10  
    },  
    "totalElements": 25,  
    "totalPages": 3,  
    "last": false,  
    "first": true  
  },  
  "message": "영화 별점 목록 조회가 완료되었습니다",  
  "timestamp": "2025-06-06T21:30:00Z"  
}

## **4.8.8 내 별점 목록 조회**

GET /api/ratings/my  
Authorization: Bearer {JWT\_TOKEN}

**Query Parameters:**

* page: integer (default: 0\) \- 페이지 번호  
* size: integer (default: 10\) \- 페이지 크기

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "content": \[  
      {  
        "id": 8,  
        "score": 4.0,  
        "username": "임종섭",  
        "userId": 8,  
        "movieId": 67,  
        "movieTitle": "드래곤 길들이기",  
        "moviePosterPath": "/8vywrRg1wrY4fo7EqgrFmUJgchG.jpg",  
        "contentType": "MOVIE",  
        "createdAt": "2025-06-06 21:27:30",  
        "updatedAt": "2025-06-06 21:27:30"  
      }  
    \],  
    "pageable": {  
      "pageNumber": 0,  
      "pageSize": 10  
    },  
    "totalElements": 5,  
    "totalPages": 1,  
    "last": true,  
    "first": true  
  },  
  "message": "내 별점 목록 조회가 완료되었습니다",  
  "timestamp": "2025-06-06T21:30:00Z"  
}

## **4.8.9 드라마 별점 목록 조회**

GET /api/ratings/dramas/{dramaId}

**Path Parameters:**

* dramaId: integer(required) \- 드라마 ID

**Query Parameters:**

* page: integer (default: 0\) \- 페이지 번호  
* size: integer (default: 10\) \- 페이지 크기

*응답 형식은 4.7.7과 동일*

## **4.8.10 사용자 별점 목록 조회**

GET /api/ratings/users/{userId}

**Path Parameters:**

* userId: integer(required) \- 사용자 ID

**Query Parameters:**

* page: integer (default: 0\) \- 페이지 번호  
* size: integer (default: 10\) \- 페이지 크기

## **4.8.11 영화 별점 분포 조회**

GET /api/ratings/movies/{movieId}/distribution

**Path Parameters:**

* movieId: integer(required) \- 영화 ID

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "1.0": 5,  
    "1.5": 3,  
    "2.0": 8,  
    "2.5": 12,  
    "3.0": 25,  
    "3.5": 30,  
    "4.0": 40,  
    "4.5": 35,  
    "5.0": 42  
  },  
  "message": "영화 별점 분포 조회가 완료되었습니다",  
  "timestamp": "2025-06-06T21:30:00Z"  
}

## **4.8.12 드라마 별점 분포 조회**

GET /api/ratings/dramas/{dramaId}/distribution

**Path Parameters:**

* dramaId: integer(required) \- 드라마 ID

*응답 형식은 4.7.11과 동일*

# **4.9 통계 API**

## **4.9.1 영화 평점 통계 조회**

GET /api/statistics/movies/{movieId}

**Path Parameters:**

* movieId: integer(required) \- 영화 ID

**Description** 특정 영화의 상세 평점 통계 정보를 조회합니다.

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "movieId": 67,  
    "movieTitle": "드래곤 길들이기",  
    "averageScore": 4.2,  
    "totalRatings": 150,  
    "scoreDistribution": {  
      "1.0": 5,  
      "1.5": 3,  
      "2.0": 8,  
      "2.5": 12,  
      "3.0": 25,  
      "3.5": 30,  
      "4.0": 40,  
      "4.5": 35,  
      "5.0": 42  
    },  
    "percentageByScore": {  
      "1.0": 3.3,  
      "1.5": 2.0,  
      "2.0": 5.3,  
      "2.5": 8.0,  
      "3.0": 16.7,  
      "3.5": 20.0,  
      "4.0": 26.7,  
      "4.5": 23.3,  
      "5.0": 28.0  
    },  
    "reviewCount": 85,  
    "ratingOnlyCount": 65  
  },  
  "message": "영화 평점 통계 조회가 완료되었습니다",  
  "timestamp": "2025-06-06T22:00:00Z"  
}

**Response (404 Not Found):**

{  
  "success": false,  
  "error": {  
    "code": "MOVIE\_NOT\_FOUND",  
    "message": "해당 영화를 찾을 수 없습니다"  
  },  
  "timestamp": "2025-06-06T22:00:00Z"  
}

## **4.9.2 드라마 평점 통계 조회**

GET /api/statistics/dramas/{dramaId}

**Path Parameters:**

* dramaId: integer(required) \- 드라마 ID

**Description** 특정 드라마의 상세 평점 통계 정보를 조회합니다.

*응답 형식은 4.8.1과 동일*

## **4.8.3 평점 높은 영화 목록 조회**

GET /api/statistics/top-rated/movies

**Query Parameters:**

* page: integer (default: 0\) \- 페이지 번호  
* size: integer (default: 20\) \- 페이지 크기

**Description** 평점이 높은 영화들을 순위별로 조회합니다.

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "content": \[  
      {  
        "id": 67,  
        "title": "드래곤 길들이기",  
        "posterPath": "/8vywrRg1wrY4fo7EqgrFmUJgchG.jpg",  
        "averageScore": 4.8,  
        "totalRatings": 250,  
        "contentType": "MOVIE",  
        "rank": 1  
      },  
      {  
        "id": 112,  
        "title": "반지의 제왕: 왕의 귀환",  
        "posterPath": "/n8BPIRqvj1SdTRND828ANXhmSng.jpg",  
        "averageScore": 4.7,  
        "totalRatings": 320,  
        "contentType": "MOVIE",  
        "rank": 2  
      }  
    \],  
    "pageable": {  
      "pageNumber": 0,  
      "pageSize": 20  
    },  
    "totalElements": 150,  
    "totalPages": 8,  
    "last": false,  
    "first": true  
  },  
  "message": "평점 높은 영화 목록 조회가 완료되었습니다",  
  "timestamp": "2025-06-06T22:00:00Z"  
}

## **4.9.4 평점 높은 드라마 목록 조회**

GET /api/statistics/top-rated/dramas

**Query Parameters:**

* page: integer (default: 0\) \- 페이지 번호  
* size: integer (default: 20\) \- 페이지 크기

*응답 형식은 4.8.3과 동일*

## **4.8.5 최근 인기 작품 조회**

GET /api/statistics/recently-popular

**Query Parameters:**

* limit: integer (default: 10\) \- 조회할 개수

**Description** 최근 평점이 많이 등록된 인기 작품들을 조회합니다.

**Response (200 OK):**

{  
  "success": true,  
  "data": \[  
    {  
      "id": 67,  
      "title": "드래곤 길들이기",  
      "posterPath": "/8vywrRg1wrY4fo7EqgrFmUJgchG.jpg",  
      "averageScore": 4.5,  
      "recentRatingsCount": 45,  
      "contentType": "MOVIE",  
      "popularityRank": 1  
    },  
    {  
      "id": 240,  
      "title": "오징어 게임",  
      "posterPath": "/yACIAqAkSLkX4coHafpyLWAtQjw.jpg",  
      "averageScore": 4.2,  
      "recentRatingsCount": 38,  
      "contentType": "DRAMA",  
      "popularityRank": 2  
    }  
  \],  
  "message": "최근 인기 작품 조회가 완료되었습니다",  
  "timestamp": "2025-06-06T22:00:00Z"  
}

## **4.9.6 장르별 평균 평점 조회**

GET /api/statistics/genres/average-rating

**Description** 모든 장르별 평균 평점 통계를 조회합니다.

**Response (200 OK):**

{  
  "success": true,  
  "data": \[  
    {  
      "genreId": 28,  
      "genreName": "액션",  
      "averageScore": 4.1,  
      "totalRatings": 1250,  
      "movieCount": 85,  
      "dramaCount": 12  
    },  
    {  
      "genreId": 35,  
      "genreName": "코미디",  
      "averageScore": 3.9,  
      "totalRatings": 980,  
      "movieCount": 72,  
      "dramaCount": 15  
    },  
    {  
      "genreId": 18,  
      "genreName": "드라마",  
      "averageScore": 4.3,  
      "totalRatings": 1580,  
      "movieCount": 45,  
      "dramaCount": 98  
    }  
  \],  
  "message": "장르별 평균 평점 조회가 완료되었습니다",  
  "timestamp": "2025-06-06T22:00:00Z"  
}

## **4.9.7 개인 평점 통계 조회**

GET /api/statistics/my  
Authorization: Bearer {JWT\_TOKEN}

**Description** 현재 로그인한 사용자의 평점 통계를 조회합니다.

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "userId": 8,  
    "username": "임종섭",  
    "totalRatings": 25,  
    "averageScore": 3.8,  
    "movieRatings": 18,  
    "dramaRatings": 7,  
    "reviewCount": 12,  
    "scoreDistribution": {  
      "1.0": 0,  
      "1.5": 1,  
      "2.0": 2,  
      "2.5": 3,  
      "3.0": 5,  
      "3.5": 4,  
      "4.0": 6,  
      "4.5": 3,  
      "5.0": 1  
    },  
    "favoriteGenres": \[  
      {  
        "genreId": 28,  
        "genreName": "액션",  
        "averageScore": 4.2,  
        "count": 8  
      },  
      {  
        "genreId": 14,  
        "genreName": "판타지",  
        "averageScore": 4.0,  
        "count": 5  
      }  
    \],  
    "monthlyActivity": {  
      "2025-04": 3,  
      "2025-05": 8,  
      "2025-06": 14  
    }  
  },  
  "message": "개인 평점 통계 조회가 완료되었습니다",  
  "timestamp": "2025-06-06T22:00:00Z"  
}

# **4.10 장르 관리 API**

## **4.10.1 모든 장르 조회**

GET /api/genres

**Description** 모든 장르의 상세 정보를 조회합니다.

**Response (200 OK):**

{  
  "success": true,  
  "data": \[  
    {  
      "id": 28,  
      "name": "액션",  
      "description": "액션과 모험이 가득한 영화들",  
      "movieCount": 85,  
      "dramaCount": 12,  
      "totalContentCount": 97,  
      "averageRating": 4.1,  
      "createdAt": "2025-01-01T00:00:00Z",  
      "updatedAt": "2025-06-01T12:00:00Z"  
    },  
    {  
      "id": 35,  
      "name": "코미디",  
      "description": "웃음과 유머가 넘치는 작품들",  
      "movieCount": 72,  
      "dramaCount": 15,  
      "totalContentCount": 87,  
      "averageRating": 3.9,  
      "createdAt": "2025-01-01T00:00:00Z",  
      "updatedAt": "2025-06-01T12:00:00Z"  
    }  
  \],  
  "message": "장르 목록 조회가 완료되었습니다",  
  "timestamp": "2025-06-06T22:30:00Z"  
}

## **4.10.2 장르 목록 조회 (간단 정보)**

GET /api/genres/list

**Description** 장르의 간단한 정보 목록을 조회합니다.

**Response (200 OK):**

{  
  "success": true,  
  "data": \[  
    {  
      "id": 28,  
      "name": "액션"  
    },  
    {  
      "id": 35,  
      "name": "코미디"  
    },  
    {  
      "id": 18,  
      "name": "드라마"  
    },  
    {  
      "id": 14,  
      "name": "판타지"  
    }  
  \],  
  "message": "장르 목록 조회가 완료되었습니다",  
  "timestamp": "2025-06-06T22:30:00Z"  
}

## **4.10.3 장르 상세 조회**

GET /api/genres/{id}

**Path Parameters:**

* id: integer(required) \- 장르 ID

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "id": 28,  
    "name": "액션",  
    "description": "액션과 모험이 가득한 영화들",  
    "movieCount": 85,  
    "dramaCount": 12,  
    "totalContentCount": 97,  
    "averageRating": 4.1,  
    "popularMovies": \[  
      {  
        "id": 67,  
        "title": "드래곤 길들이기",  
        "posterPath": "/8vywrRg1wrY4fo7EqgrFmUJgchG.jpg",  
        "voteAverage": 9.2  
      }  
    \],  
    "popularDramas": \[  
      {  
        "id": 240,  
        "title": "오징어 게임",  
        "posterPath": "/yACIAqAkSLkX4coHafpyLWAtQjw.jpg",  
        "voteAverage": 7.9  
      }  
    \],  
    "createdAt": "2025-01-01T00:00:00Z",  
    "updatedAt": "2025-06-01T12:00:00Z"  
  },  
  "message": "장르 상세 조회가 완료되었습니다",  
  "timestamp": "2025-06-06T22:30:00Z"  
}

**Response (404 Not Found):**

{  
  "success": false,  
  "error": {  
    "code": "GENRE\_NOT\_FOUND",  
    "message": "해당 장르를 찾을 수 없습니다"  
  },  
  "timestamp": "2025-06-06T22:30:00Z"  
}

## **4.10.4 장르 생성**

POST /api/genres  
Content-Type: application/json  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: ADMIN, SUPER\_ADMIN

**Request Body:**

{  
  "name": "스릴러",  
  "description": "긴장감 넘치는 스릴러 작품들"  
}

**Response (201 Created):**

{  
  "success": true,  
  "data": {  
    "id": 53,  
    "name": "스릴러",  
    "description": "긴장감 넘치는 스릴러 작품들",  
    "movieCount": 0,  
    "dramaCount": 0,  
    "totalContentCount": 0,  
    "averageRating": 0.0,  
    "createdAt": "2025-06-06T22:30:00Z",  
    "updatedAt": "2025-06-06T22:30:00Z"  
  },  
  "message": "장르가 성공적으로 생성되었습니다",  
  "timestamp": "2025-06-06T22:30:00Z"  
}

**Response (400 Bad Request):**

{  
  "success": false,  
  "error": {  
    "code": "VALIDATION\_ERROR",  
    "message": "입력값이 올바르지 않습니다",  
    "fieldErrors": \[  
      {  
        "field": "name",  
        "message": "장르명은 필수 입력사항입니다.",  
        "rejectedValue": ""  
      }  
    \]  
  },  
  "timestamp": "2025-06-06T22:30:00Z"  
}

## **4.10.5 장르 수정**

PUT /api/genres/{id}  
Content-Type: application/json  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: ADMIN, SUPER\_ADMIN

**Path Parameters:**

* id: integer(required) \- 장르 ID

**Request Body:**

{  
  "name": "액션/어드벤처",  
  "description": "액션과 모험이 가득한 영화 및 드라마들"  
}

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "id": 28,  
    "name": "액션/어드벤처",  
    "description": "액션과 모험이 가득한 영화 및 드라마들",  
    "movieCount": 85,  
    "dramaCount": 12,  
    "totalContentCount": 97,  
    "averageRating": 4.1,  
    "createdAt": "2025-01-01T00:00:00Z",  
    "updatedAt": "2025-06-06T22:30:00Z"  
  },  
  "message": "장르가 성공적으로 수정되었습니다",  
  "timestamp": "2025-06-06T22:30:00Z"  
}

## **4.10.6 장르 삭제**

DELETE /api/genres/{id}  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: ADMIN, SUPER\_ADMIN

**Path Parameters:**

* id: integer(required) \- 장르 ID

**Business Rules Validation:**

* 해당 장르에 연결된 콘텐츠가 있는 경우 삭제 불가

**Response (204 No Content)**

**Response (409 Conflict):**

{  
  "success": false,  
  "error": {  
    "code": "GENRE\_HAS\_CONTENT",  
    "message": "해당 장르에 연결된 콘텐츠가 있어 삭제할 수 없습니다",  
    "detail": "연결된 영화: 85개, 드라마: 12개"  
  },  
  "timestamp": "2025-06-06T22:30:00Z"  
}

## **4.10.7 장르 통계 조회**

GET /api/genres/{id}/statistics

**Path Parameters:**

* id: integer(required) \- 장르 ID

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "genreId": 28,  
    "genreName": "액션",  
    "totalContent": 97,  
    "movieCount": 85,  
    "dramaCount": 12,  
    "averageRating": 4.1,  
    "totalRatings": 1250,  
    "popularityRank": 2,  
    "ratingDistribution": {  
      "1.0": 25,  
      "2.0": 50,  
      "3.0": 200,  
      "4.0": 500,  
      "5.0": 475  
    },  
    "monthlyTrend": {  
      "2025-04": {  
        "newContent": 5,  
        "newRatings": 120  
      },  
      "2025-05": {  
        "newContent": 8,  
        "newRatings": 195  
      },  
      "2025-06": {  
        "newContent": 3,  
        "newRatings": 87  
      }  
    }  
  },  
  "message": "장르 통계 조회가 완료되었습니다",  
  "timestamp": "2025-06-06T22:30:00Z"  
}

## **4.10.8 장르별 인기 컨텐츠 조회**

GET /api/genres/{id}/popular

**Path Parameters:**

* id: integer(required) \- 장르 ID

**Query Parameters:**

* limit: integer (default: 10\) \- 조회할 컨텐츠 개수

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "genreId": 28,  
    "genreName": "액션",  
    "popularMovies": \[  
      {  
        "id": 67,  
        "title": "드래곤 길들이기",  
        "posterPath": "/8vywrRg1wrY4fo7EqgrFmUJgchG.jpg",  
        "voteAverage": 9.2,  
        "voteCount": 8,  
        "popularityRank": 1  
      },  
      {  
        "id": 112,  
        "title": "반지의 제왕: 왕의 귀환",  
        "posterPath": "/n8BPIRqvj1SdTRND828ANXhmSng.jpg",  
        "voteAverage": 8.5,  
        "voteCount": 25121,  
        "popularityRank": 2  
      }  
    \],  
    "popularDramas": \[  
      {  
        "id": 240,  
        "title": "오징어 게임",  
        "posterPath": "/yACIAqAkSLkX4coHafpyLWAtQjw.jpg",  
        "voteAverage": 7.9,  
        "voteCount": 15827,  
        "popularityRank": 1  
      }  
    \]  
  },  
  "message": "장르별 인기 컨텐츠 조회가 완료되었습니다",  
  "timestamp": "2025-06-06T22:30:00Z"  
}

## **4.10.9 TMDB 장르 동기화**

POST /api/genres/sync  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: ADMIN, SUPER\_ADMIN

**Description** TMDB에서 장르 정보를 동기화합니다.

**Response (200 OK):**

{  
  "success": true,  
  "data": \[  
    {  
      "id": 28,  
      "name": "액션",  
      "status": "UPDATED"  
    },  
    {  
      "id": 53,  
      "name": "스릴러",  
      "status": "CREATED"  
    }  
  \],  
  "message": "TMDB 장르 동기화가 완료되었습니다",  
  "timestamp": "2025-06-06T22:30:00Z",  
  "summary": {  
    "created": 5,  
    "updated": 12,  
    "unchanged": 8  
  }  
}

**Response (500 Internal Server Error):**

{  
  "success": false,  
  "error": {  
    "code": "EXTERNAL\_API\_ERROR",  
    "message": "TMDB API 호출 중 오류가 발생했습니다",  
    "detail": "API 키가 유효하지 않거나 서비스에 접근할 수 없습니다."  
  },  
  "timestamp": "2025-06-06T22:30:00Z"  
}

## **4.10.10 장르 검색**

GET /api/genres/search

**Query Parameters:**

* name: string(required) \- 검색할 장르명

**Response (200 OK):**

{  
  "success": true,  
  "data": \[  
    {  
      "id": 28,  
      "name": "액션"  
    },  
    {  
      "id": 12,  
      "name": "액션/어드벤처"  
    }  
  \],  
  "message": "장르 검색이 완료되었습니다",  
  "timestamp": "2025-06-06T22:30:00Z"  
}

# **4.11 시청 기록 관리 API**

## **4.11.1 영화 시청 완료 표시**

POST /api/movies/{id}/watched  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: MEMBER, LIBRARIAN, ADMIN, SUPER\_ADMIN

**Path Parameters:**

* id: integer(required) \- 영화 ID

**Description** 특정 영화를 시청했다고 표시합니다.

**Business Rules Validation:**

* 인증된 사용자인지 확인  
* 영화가 존재하는지 확인  
* 중복 시청 기록은 업데이트됨

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "userId": 8,  
    "movieId": 67,  
    "movieTitle": "드래곤 길들이기",  
    "contentType": "MOVIE",  
    "watchedAt": "2025-06-06T23:00:00Z",  
    "isWatched": true  
  },  
  "message": "영화를 시청 완료로 표시했습니다",  
  "timestamp": "2025-06-06T23:00:00Z"  
}

**Response (401 Unauthorized):**

{  
  "success": false,  
  "error": {  
    "code": "UNAUTHORIZED",  
    "message": "로그인이 필요합니다"  
  },  
  "timestamp": "2025-06-06T23:00:00Z"  
}

**Response (404 Not Found):**

{  
  "success": false,  
  "error": {  
    "code": "MOVIE\_NOT\_FOUND",  
    "message": "해당 영화를 찾을 수 없습니다"  
  },  
  "timestamp": "2025-06-06T23:00:00Z"  
}

## **4.11.2 영화 시청 여부 확인**

GET /api/movies/{id}/watched  
Authorization: Bearer {JWT\_TOKEN} (선택적)

**Path Parameters:**

* id: integer(required) \- 영화 ID

**Description** 특정 영화의 시청 여부를 확인합니다. 로그인하지 않은 경우 false를 반환합니다.

**Response (200 OK) \- 로그인한 경우:**

{  
  "success": true,  
  "data": {  
    "isWatched": true,  
    "watchedAt": "2025-06-06T23:00:00Z",  
    "movieId": 67,  
    "movieTitle": "드래곤 길들이기"  
  },  
  "message": "시청 여부 확인이 완료되었습니다",  
  "timestamp": "2025-06-06T23:15:00Z"  
}

**Response (200 OK) \- 로그인하지 않은 경우:**

{  
  "success": true,  
  "data": {  
    "isWatched": false  
  },  
  "message": "시청 여부 확인이 완료되었습니다",  
  "timestamp": "2025-06-06T23:15:00Z"  
}

## **4.11.3 영화 시청 기록 삭제**

DELETE /api/movies/{id}/watched  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: MEMBER, LIBRARIAN, ADMIN, SUPER\_ADMIN

**Path Parameters:**

* id: integer(required) \- 영화 ID

**Description** 특정 영화의 시청 기록을 삭제합니다.

**Business Rules Validation:**

* 인증된 사용자인지 확인  
* 시청 기록이 존재하는지 확인

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "userId": 8,  
    "movieId": 67,  
    "movieTitle": "드래곤 길들이기",  
    "contentType": "MOVIE",  
    "isWatched": false  
  },  
  "message": "영화 시청 기록을 삭제했습니다",  
  "timestamp": "2025-06-06T23:30:00Z"  
}

**Response (404 Not Found):**

{  
  "success": false,  
  "error": {  
    "code": "WATCHED\_RECORD\_NOT\_FOUND",  
    "message": "해당 영화의 시청 기록을 찾을 수 없습니다"  
  },  
  "timestamp": "2025-06-06T23:30:00Z"  
}

## **4.11.4 드라마 시청 완료 표시**

POST /api/dramas/{id}/watched  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: MEMBER, LIBRARIAN, ADMIN, SUPER\_ADMIN

**Path Parameters:**

* id: integer(required) \- 드라마 ID

**Description** 특정 드라마를 시청했다고 표시합니다.

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "userId": 8,  
    "dramaId": 240,  
    "dramaTitle": "오징어 게임",  
    "contentType": "DRAMA",  
    "watchedAt": "2025-06-06T23:45:00Z",  
    "isWatched": true  
  },  
  "message": "드라마를 시청 완료로 표시했습니다",  
  "timestamp": "2025-06-06T23:45:00Z"  
}

## **4.11.5 드라마 시청 여부 확인**

GET /api/dramas/{id}/watched  
Authorization: Bearer {JWT\_TOKEN} (선택적)

**Path Parameters:**

* id: integer(required) \- 드라마 ID

*응답 형식은 4.10.2와 동일*

## **4.11.6 드라마 시청 기록 삭제**

DELETE /api/dramas/{id}/watched  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: MEMBER, LIBRARIAN, ADMIN, SUPER\_ADMIN

**Path Parameters:**

* id: integer(required) \- 드라마 ID

*응답 형식은 4.10.3과 동일*

## **4.11.7 내 시청 기록 목록 조회**

GET /api/watched/my  
Authorization: Bearer {JWT\_TOKEN}

**Query Parameters:**

* contentType: string (optional) \- 콘텐츠 타입 필터 (MOVIE, DRAMA)  
* page: integer (default: 0\) \- 페이지 번호  
* size: integer (default: 20\) \- 페이지 크기  
* sortBy: string (default: "watchedAt") \- 정렬 기준  
* sortDirection: string (default: "desc") \- 정렬 방향

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "content": \[  
      {  
        "userId": 8,  
        "contentId": 67,  
        "contentTitle": "드래곤 길들이기",  
        "contentType": "MOVIE",  
        "posterPath": "/8vywrRg1wrY4fo7EqgrFmUJgchG.jpg",  
        "watchedAt": "2025-06-06T23:00:00Z",  
        "myRating": 4.5,  
        "myReview": "정말 재미있었어요\!"  
      },  
      {  
        "userId": 8,  
        "contentId": 240,  
        "contentTitle": "오징어 게임",  
        "contentType": "DRAMA",  
        "posterPath": "/yACIAqAkSLkX4coHafpyLWAtQjw.jpg",  
        "watchedAt": "2025-06-06T22:30:00Z",  
        "myRating": 4.0,  
        "myReview": null  
      }  
    \],  
    "pageable": {  
      "pageNumber": 0,  
      "pageSize": 20  
    },  
    "totalElements": 25,  
    "totalPages": 2,  
    "last": false,  
    "first": true  
  },  
  "message": "내 시청 기록 조회가 완료되었습니다",  
  "timestamp": "2025-06-07T00:00:00Z"  
}

## **4.11.8 시청 기록 통계 조회**

GET /api/watched/my/statistics  
Authorization: Bearer {JWT\_TOKEN}

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "userId": 8,  
    "totalWatched": 25,  
    "movieCount": 18,  
    "dramaCount": 7,  
    "averageRating": 3.8,  
    "favoriteGenres": \[  
      {  
        "genreId": 28,  
        "genreName": "액션",  
        "count": 8  
      },  
      {  
        "genreId": 14,  
        "genreName": "판타지",  
        "count": 5  
      }  
    \],  
    "monthlyWatchCount": {  
      "2025-04": 3,  
      "2025-05": 8,  
      "2025-06": 14  
    },  
    "watchingStreak": {  
      "current": 5,  
      "longest": 12  
    },  
    "completionRate": {  
      "movies": 85.7,  
      "dramas": 70.0  
    }  
  },  
  "message": "시청 기록 통계 조회가 완료되었습니다",  
  "timestamp": "2025-06-07T00:00:00Z"  
}

## **4.11.9 시청 기록 일괄 관리**

POST /api/watched/batch  
Authorization: Bearer {JWT\_TOKEN}  
Content-Type: application/json

**Request Body:**

{  
  "action": "ADD",  
  "items": \[  
    {  
      "contentType": "MOVIE",  
      "contentId": 67  
    },  
    {  
      "contentType": "DRAMA",  
      "contentId": 240  
    }  
  \]  
}

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "processed": 2,  
    "succeeded": 2,  
    "failed": 0,  
    "results": \[  
      {  
        "contentType": "MOVIE",  
        "contentId": 67,  
        "status": "SUCCESS",  
        "message": "시청 기록이 추가되었습니다"  
      },  
      {  
        "contentType": "DRAMA",   
        "contentId": 240,  
        "status": "SUCCESS",  
        "message": "시청 기록이 추가되었습니다"  
      }  
    \]  
  },  
  "message": "일괄 처리가 완료되었습니다",  
  "timestamp": "2025-06-07T00:15:00Z"  
}

# **4.12 통합 검색 API**

## **4.12.1 상세 검색 (POST)**

POST /api/content/search  
Content-Type: application/json

**Query Parameters:**

* page: integer (default: 0\) \- 페이지 번호  
* size: integer (default: 20\) \- 페이지 크기

**Description** POST 방식으로 상세 조건을 포함한 영화와 드라마 통합 검색을 수행합니다.

**Request Body:**

{  
  "query": "아바타",  
  "contentType": "ALL",  
  "genreIds": \[28, 878\],  
  "minRating": 3.0,  
  "maxRating": 5.0,  
  "minYear": 2000,  
  "maxYear": 2025,  
  "sortBy": "voteAverage",  
  "sortDirection": "desc",  
  "useApi": false  
}

**Request Body Fields:**

* query: string (optional) \- 검색 키워드  
* contentType: string (default: "ALL") \- 콘텐츠 타입 (ALL, MOVIE, DRAMA)  
* genreIds: array (optional) \- 장르 ID 배열  
* minRating: double (optional) \- 최소 평점  
* maxRating: double (optional) \- 최대 평점  
* minYear: integer (optional) \- 최소 출시 연도  
* maxYear: integer (optional) \- 최대 출시 연도  
* sortBy: string (default: "voteAverage") \- 정렬 기준  
* sortDirection: string (default: "desc") \- 정렬 방향  
* useApi: boolean (default: false) \- 외부 API 사용 여부

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "movies": {  
      "content": \[  
        {  
          "id": 302,  
          "tmdbId": 19995,  
          "title": "아바타",  
          "posterPath": "/m5lCha2XcbDowDoYHPc0DTNaCPU.jpg",  
          "voteAverage": 7.6,  
          "voteCount": 32289,  
          "combinedRating": 3.8,  
          "releaseDate": "2009-12-15",  
          "genreNames": \["액션", "모험", "판타지", "SF"\],  
          "reviewCount": 0,  
          "contentType": "MOVIE"  
        },  
        {  
          "id": 303,  
          "tmdbId": 76600,  
          "title": "아바타: 물의 길",  
          "posterPath": "/u2aVXft5GLBQnjzWVNda7sdDpdu.jpg",  
          "voteAverage": 7.611,  
          "voteCount": 12597,  
          "combinedRating": 3.81,  
          "releaseDate": "2022-12-14",  
          "genreNames": \["SF", "모험", "액션"\],  
          "reviewCount": 0,  
          "contentType": "MOVIE"  
        }  
      \],  
      "totalElements": 3,  
      "totalPages": 1,  
      "currentPage": 0,  
      "hasNext": false,  
      "hasPrevious": false  
    },  
    "dramas": {  
      "content": \[\],  
      "totalElements": 0,  
      "totalPages": 0,  
      "currentPage": 0,  
      "hasNext": false,  
      "hasPrevious": false  
    },  
    "searchMetadata": {  
      "query": "아바타",  
      "contentType": "ALL",  
      "totalResults": 3,  
      "searchTime": "45ms",  
      "useApi": false  
    }  
  },  
  "message": "검색이 완료되었습니다",  
  "timestamp": "2025-06-07T01:00:00Z"  
}

**Response (400 Bad Request):**

{  
  "success": false,  
  "error": {  
    "code": "VALIDATION\_ERROR",  
    "message": "검색 조건이 올바르지 않습니다",  
    "fieldErrors": \[  
      {  
        "field": "minRating",  
        "message": "최소 평점은 0.0 이상이어야 합니다",  
        "rejectedValue": \-1.0  
      },  
      {  
        "field": "maxRating",  
        "message": "최대 평점은 5.0 이하여야 합니다",  
        "rejectedValue": 6.0  
      }  
    \]  
  },  
  "timestamp": "2025-06-07T01:00:00Z"  
}

## **4.12.2 간단 검색 (GET)**

GET /api/content/search

**Query Parameters:**

* query: string (required) \- 검색 키워드  
* contentType: string (default: "ALL") \- 콘텐츠 타입 (ALL, MOVIE, DRAMA)  
* useApi: boolean (default: false) \- 외부 API 사용 여부  
* page: integer (default: 0\) \- 페이지 번호  
* size: integer (default: 20\) \- 페이지 크기

**Description** GET 방식으로 간단한 키워드 검색을 수행합니다.

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "movies": {  
      "content": \[  
        {  
          "id": 302,  
          "title": "아바타",  
          "posterPath": "/m5lCha2XcbDowDoYHPc0DTNaCPU.jpg",  
          "voteAverage": 7.6,  
          "releaseDate": "2009-12-15",  
          "genreNames": \["액션", "모험", "판타지", "SF"\],  
          "contentType": "MOVIE"  
        }  
      \],  
      "totalElements": 3,  
      "currentPage": 0  
    },  
    "dramas": {  
      "content": \[\],  
      "totalElements": 0,  
      "currentPage": 0  
    },  
    "searchMetadata": {  
      "query": "아바타",  
      "contentType": "ALL",  
      "totalResults": 3,  
      "searchTime": "32ms"  
    }  
  },  
  "message": "검색이 완료되었습니다",  
  "timestamp": "2025-06-07T01:15:00Z"  
}

## **4.12.3 추천 검색어 조회**

GET /api/content/search/suggestions

**Query Parameters:**

* q: string (required) \- 입력 중인 검색어  
* limit: integer (default: 10\) \- 추천 개수

**Description** 사용자가 입력 중인 검색어에 대한 자동완성 추천을 제공합니다.

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "suggestions": \[  
      {  
        "text": "아바타",  
        "type": "MOVIE",  
        "count": 3  
      },  
      {  
        "text": "아바타: 물의 길",  
        "type": "MOVIE",  
        "count": 1  
      },  
      {  
        "text": "아바타 정글의 비밀",  
        "type": "MOVIE",  
        "count": 1  
      }  
    \],  
    "popularSearches": \[  
      "오징어 게임",  
      "드래곤 길들이기",  
      "어벤져스"  
    \]  
  },  
  "message": "검색어 추천이 완료되었습니다",  
  "timestamp": "2025-06-07T01:30:00Z"  
}

## **4.12.4 인기 검색어 조회**

GET /api/content/search/trending

**Query Parameters:**

* period: string (default: "daily") \- 기간 (daily, weekly, monthly)  
* limit: integer (default: 10\) \- 조회 개수

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "period": "daily",  
    "trendingSearches": \[  
      {  
        "rank": 1,  
        "keyword": "오징어 게임",  
        "searchCount": 1250,  
        "changeFromPrevious": "+15%"  
      },  
      {  
        "rank": 2,  
        "keyword": "드래곤 길들이기",  
        "searchCount": 980,  
        "changeFromPrevious": "+8%"  
      },  
      {  
        "rank": 3,  
        "keyword": "어벤져스",  
        "searchCount": 875,  
        "changeFromPrevious": "-3%"  
      }  
    \],  
    "lastUpdated": "2025-06-07T00:00:00Z"  
  },  
  "message": "인기 검색어 조회가 완료되었습니다",  
  "timestamp": "2025-06-07T01:45:00Z"  
}

## **4.12.5 검색 기록 조회**

GET /api/content/search/history  
Authorization: Bearer {JWT\_TOKEN}

**Query Parameters:**

* limit: integer (default: 20\) \- 조회 개수

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "searchHistory": \[  
      {  
        "keyword": "아바타",  
        "contentType": "ALL",  
        "searchedAt": "2025-06-07T01:00:00Z",  
        "resultCount": 3  
      },  
      {  
        "keyword": "오징어 게임",  
        "contentType": "DRAMA",  
        "searchedAt": "2025-06-06T23:30:00Z",  
        "resultCount": 4  
      },  
      {  
        "keyword": "액션",  
        "contentType": "MOVIE",  
        "searchedAt": "2025-06-06T22:15:00Z",  
        "resultCount": 85  
      }  
    \],  
    "totalCount": 15  
  },  
  "message": "검색 기록 조회가 완료되었습니다",  
  "timestamp": "2025-06-07T02:00:00Z"  
}

## **4.12.6 검색 기록 삭제**

DELETE /api/content/search/history  
Authorization: Bearer {JWT\_TOKEN}

**Query Parameters:**

* keyword: string (optional) \- 특정 검색어만 삭제  
* all: boolean (default: false) \- 전체 삭제 여부

**Response (200 OK) \- 특정 검색어 삭제:**

{  
  "success": true,  
  "data": {  
    "deletedKeyword": "아바타",  
    "remainingCount": 14  
  },  
  "message": "검색 기록이 삭제되었습니다",  
  "timestamp": "2025-06-07T02:15:00Z"  
}

**Response (200 OK) \- 전체 삭제:**

{  
  "success": true,  
  "data": {  
    "deletedCount": 15  
  },  
  "message": "모든 검색 기록이 삭제되었습니다",  
  "timestamp": "2025-06-07T02:15:00Z"  
}

## **4.12.7 외부 API 검색**

POST /api/content/search/external  
Content-Type: application/json  
Authorization: Bearer {JWT\_TOKEN}  
Required Role: ADMIN, SUPER\_ADMIN

**Description** TMDB 등 외부 API를 통해 새로운 콘텐츠를 검색하고 데이터베이스에 추가합니다.

**Request Body:**

{  
  "query": "신작 영화",  
  "contentType": "MOVIE",  
  "addToDatabase": true,  
  "limit": 20  
}

**Response (200 OK):**

{  
  "success": true,  
  "data": {  
    "externalResults": \[  
      {  
        "tmdbId": 1234567,  
        "title": "새로운 영화",  
        "overview": "흥미진진한 새 영화입니다",  
        "releaseDate": "2025-07-01",  
        "status": "ADDED\_TO\_DB"  
      }  
    \],  
    "summary": {  
      "searched": 20,  
      "addedToDatabase": 5,  
      "alreadyExists": 15  
    }  
  },  
  "message": "외부 API 검색이 완료되었습니다",  
  "timestamp": "2025-06-07T02:30:00Z"  
}

---

## **5\. 에러 코드 및 처리**

# **5.1 에러 코드 정의**

# **5.1.1 공통 에러 코드**

| 코드 | HTTP 상태 | 설명 | 해결 방법 |
| ----- | ----- | ----- | ----- |
| C002 | 400 | 잘못된 입력값입니다 | 요청 데이터 확인 후 재시도 |
| C003 | 405 | 허용되지 않은 HTTP 메서드입니다 | HTTP 메서드 확인 |
| C004 | 404 | 엔티티를 찾을 수 없습니다 | 리소스 ID 확인 |
| C005 | 400 | 잘못된 타입 값입니다 | 데이터 타입 확인 |
| C006 | 403 | 접근이 거부되었습니다 | 권한 확인 |

## **5.1.2 사용자 관련 에러 코드**

| 코드 | HTTP 상태 | 설명 | 해결 방법 |
| ----- | ----- | ----- | ----- |
| U001 | 404 | 사용자를 찾을 수 없습니다 | 사용자 ID 확인 |
| U002 | 409 | 이미 존재하는 사용자입니다 | 사용자 정보 확인 |
| U003 | 403 | 사용자 접근이 거부되었습니다 | 사용자 권한 확인 |
| U004 | 400 | 잘못된 비밀번호입니다 | 비밀번호 확인 |
| U005 | 403 | 정지된 사용자입니다 | 사용자 상태 확인 |

## **5.1.3 영화 관련 에러 코드**

| 코드 | HTTP 상태 | 설명 | 해결 방법 |
| ----- | ----- | ----- | ----- |
| M001 | 404 | 영화를 찾을 수 없습니다 | 영화 ID 확인 |
| M002 | 409 | 이미 존재하는 영화입니다 | 기존 영화 확인 |
| M003 | 403 | 영화 접근이 거부되었습니다 | 접근 권한 확인 |

## **5.1.4 드라마 관련 에러 코드**

| 코드 | HTTP 상태 | 설명 | 해결 방법 |
| ----- | ----- | ----- | ----- |
| D001 | 404 | 드라마를 찾을 수 없습니다 | 드라마 ID 확인 |
| D002 | 409 | 이미 존재하는 드라마입니다 | 기존 드라마 확인 |
| D003 | 403 | 드라마 접근이 거부되었습니다 | 접근 권한 확인 |

## **5.1.5 장르 관련 에러 코드**

| 코드 | HTTP 상태 | 설명 | 해결 방법 |
| ----- | ----- | ----- | ----- |
| G001 | 404 | 장르를 찾을 수 없습니다 | 장르 ID 확인 |
| G002 | 409 | 이미 존재하는 장르입니다 | 기존 장르명 확인 |
| G003 | 409 | 연관된 컨텐츠가 있어 삭제할 수 없습니다 | 연결된 컨텐츠 정리 후 삭제 |

## **5.1.6 리뷰 관련 에러 코드**

| 코드 | HTTP 상태 | 설명 | 해결 방법 |
| ----- | ----- | ----- | ----- |
| R001 | 404 | 리뷰를 찾을 수 없습니다 | 리뷰 ID 확인 |
| R002 | 409 | 이미 리뷰를 작성하셨습니다 | 기존 리뷰 수정 또는 삭제 후 재작성 |
| R003 | 403 | 리뷰 접근이 거부되었습니다 | 작성자 본인 또는 관리자만 수정/삭제 가능 |
| R004 | 400 | 리뷰 내용이 올바르지 않습니다 | 리뷰 내용 확인 |

## **5.1.7 별점 관련 에러 코드**

| 코드 | HTTP 상태 | 설명 | 해결 방법 |
| ----- | ----- | ----- | ----- |
| RT001 | 404 | 별점을 찾을 수 없습니다 | 별점 ID 확인 |
| RT002 | 409 | 이미 별점을 등록하셨습니다 | 기존 별점 수정 |
| RT003 | 403 | 별점 접근이 거부되었습니다 | 작성자 본인만 수정/삭제 가능 |
| RT004 | 400 | 별점은 1\~10 사이여야 합니다 | 1-10 사이 값 입력 |

## **5.1.8 컨텐츠 관련 에러 코드**

| 코드 | HTTP 상태 | 설명 | 해결 방법 |
| ----- | ----- | ----- | ----- |
| CT001 | 400 | 컨텐츠 타입이 올바르지 않습니다 | 컨텐츠 타입 확인 (MOVIE, DRAMA) |
| CT002 | 404 | 컨텐츠를 찾을 수 없습니다 | 컨텐츠 ID 및 타입 확인 |

## **5.1.9 TMDB API 관련 에러 코드**

| 코드 | HTTP 상태 | 설명 | 해결 방법 |
| ----- | ----- | ----- | ----- |
| T001 | 503 | TMDB API 호출에 실패했습니다 | 잠시 후 재시도 |
| T002 | 429 | TMDB API 호출 한도를 초과했습니다 | 잠시 후 재시도 |
| T003 | 404 | TMDB에서 데이터를 찾을 수 없습니다 | 검색 조건 확인 |

## **5.1.10 인증/인가 관련 에러 코드**

| 코드 | HTTP 상태 | 설명 | 해결 방법 |
| ----- | ----- | ----- | ----- |
| A001 | 401 | 인증이 필요합니다 | 로그인 필요 |
| A002 | 401 | 유효하지 않은 토큰입니다 | 토큰 확인 |
| A003 | 401 | 만료된 토큰입니다 | 토큰 갱신 또는 재로그인 |
| A004 | 403 | 권한이 부족합니다 | 권한 확인 |
| AUTH\_001 | 401 | 이메일 또는 비밀번호가 올바르지 않습니다 | 로그인 정보 확인 |
| AUTH\_002 | 401 | 토큰이 만료되었습니다 | 토큰 갱신 |
| AUTH\_003 | 401 | 유효하지 않은 토큰입니다 | 토큰 확인 |
| AUTH\_004 | 403 | 접근 권한이 없습니다 | 권한 확인 |
| AUTH\_005 | 403 | 계정이 잠겨있습니다 | 관리자 문의 |

## **5.1.11 게시글 관련 에러 코드**

| 코드 | HTTP 상태 | 설명 | 해결 방법 |
| ----- | ----- | ----- | ----- |
| POST\_001 | 404 | 게시글을 찾을 수 없습니다 | 게시글 코드 확인 |
| POST\_002 | 409 | 이미 사용 중인 게시글 코드입니다 | 다른 코드 사용 |
| POST\_003 | 403 | 작성자가 아닙니다 | 게시글 작성자만 수정/삭제 가능 |

## **5.1.12 댓글 관련 에러 코드**

| 코드 | HTTP 상태 | 설명 | 해결 방법 |
| ----- | ----- | ----- | ----- |
| COMMENT\_001 | 404 | 댓글을 찾을 수 없습니다 | 댓글 ID 확인 |
| COMMENT\_002 | 403 | 작성자가 아닙니다 | 댓글 작성자만 수정/삭제 가능 |

## **5.1.13 검증 관련 에러 코드**

| 코드 | HTTP 상태 | 설명 | 해결 방법 |
| ----- | ----- | ----- | ----- |
| VALID\_001 | 400 | 입력값이 올바르지 않습니다 | 입력값 검증 후 재시도 |
| VALID\_002 | 400 | 필수 항목이 누락되었습니다 | 필수 필드 입력 |
| VALID\_003 | 400 | 형식이 올바르지 않습니다 | 데이터 형식 확인 |

## **5.1.14 비즈니스 로직 관련 에러 코드**

| 코드 | HTTP 상태 | 설명 | 해결 방법 |
| ----- | ----- | ----- | ----- |
| BIZ\_001 | 400 | 비즈니스 규칙 위반입니다 | 비즈니스 규칙 확인 |
| BIZ\_002 | 400 | 허용되지 않은 작업입니다 | 작업 조건 확인 |

## **5.1.15 서버 오류 관련 에러 코드**

| 코드 | HTTP 상태 | 설명 | 해결 방법 |
| ----- | ----- | ----- | ----- |
| SERVER\_001 | 500 | 서버 내부 오류가 발생했습니다 | 관리자 문의 |
| SERVER\_002 | 500 | 데이터베이스 오류가 발생했습니다 | 관리자 문의 |
| SERVER\_003 | 502 | 외부 API 호출 중 오류가 발생했습니다 | 잠시 후 재시도 |

## **5.1.16 파일 관련 에러 코드**

| 코드 | HTTP 상태 | 설명 | 해결 방법 |
| ----- | ----- | ----- | ----- |
| F001 | 400 | 파일 업로드에 실패했습니다 | 파일 확인 후 재시도 |
| F002 | 400 | 파일 크기가 제한을 초과했습니다 | 파일 크기 조정 |
| F003 | 400 | 지원하지 않는 파일 형식입니다 | 지원 형식 확인 |
| IMAGE\_001 | 500 | 이미지 저장에 실패했습니다 | 잠시 후 재시도 |
| IMAGE\_002 | 500 | 이미지 삭제에 실패했습니다 | 관리자 문의 |

# **5.2 응답 형식 표준화**

## **5.2.1 성공 응답 표준 형식**

### **단일 객체 응답**

{  
  "success": true,  
  "data": {  
    // 실제 데이터 객체  
  },  
  "message": "요청이 성공적으로 처리되었습니다",  
  "timestamp": "2025-06-07T05:00:00Z",  
  "requestId": "550e8400-e29b-41d4-a716-446655440000"  
}

### **목록 응답 (페이지네이션 포함)**

{  
  "success": true,  
  "data": {  
    "content": \[  
      // 데이터 배열  
    \],  
    "pageable": {  
      "pageNumber": 0,  
      "pageSize": 20,  
      "sort": {  
        "empty": false,  
        "sorted": true,  
        "unsorted": false  
      },  
      "offset": 0,  
      "unpaged": false,  
      "paged": true  
    },  
    "totalElements": 150,  
    "totalPages": 8,  
    "last": false,  
    "first": true,  
    "numberOfElements": 20,  
    "empty": false  
  },  
  "message": "목록 조회가 완료되었습니다",  
  "timestamp": "2025-06-07T05:00:00Z",  
  "requestId": "550e8400-e29b-41d4-a716-446655440001"  
}

### **간단한 목록 응답 (페이지네이션 없음)**

{  
  "success": true,  
  "data": \[  
    // 데이터 배열  
  \],  
  "message": "목록 조회가 완료되었습니다",  
  "timestamp": "2025-06-07T05:00:00Z",  
  "requestId": "550e8400-e29b-41d4-a716-446655440002"  
}

### **생성/수정 성공 응답**

{  
  "success": true,  
  "data": {  
    "id": 123,  
    // 생성/수정된 객체 데이터  
  },  
  "message": "리소스가 성공적으로 생성되었습니다",  
  "timestamp": "2025-06-07T05:00:00Z",  
  "requestId": "550e8400-e29b-41d4-a716-446655440003"  
}

### **삭제 성공 응답 (No Content)**

HTTP/1.1 204 No Content

### **삭제 성공 응답 (메시지 포함)**

{  
  "success": true,  
  "message": "리소스가 성공적으로 삭제되었습니다",  
  "timestamp": "2025-06-07T05:00:00Z",  
  "requestId": "550e8400-e29b-41d4-a716-446655440004"  
}

## **5.2.2 에러 응답 표준 형식**

### **일반 에러 응답**

{  
  "success": false,  
  "error": {  
    "code": "RESOURCE\_NOT\_FOUND",  
    "message": "요청한 리소스를 찾을 수 없습니다",  
    "detail": "ID 123에 해당하는 영화가 존재하지 않습니다",  
    "path": "/api/movies/123",  
    "method": "GET"  
  },  
  "timestamp": "2025-06-07T05:00:00Z",  
  "requestId": "550e8400-e29b-41d4-a716-446655440005"  
}

### **입력값 검증 에러 응답**

{  
  "success": false,  
  "error": {  
    "code": "VALIDATION\_ERROR",  
    "message": "입력값이 올바르지 않습니다",  
    "fieldErrors": \[  
      {  
        "field": "email",  
        "message": "이메일 형식이 올바르지 않습니다",  
        "rejectedValue": "invalid-email",  
        "code": "EMAIL\_FORMAT\_INVALID"  
      },  
      {  
        "field": "score",  
        "message": "별점은 0.5\~5점 사이여야 합니다",  
        "rejectedValue": 6,  
        "code": "SCORE\_OUT\_OF\_RANGE"  
      }  
    \],  
    "path": "/api/reviews",  
    "method": "POST"  
  },  
  "timestamp": "2025-06-07T05:00:00Z",  
  "requestId": "550e8400-e29b-41d4-a716-446655440006"  
}

### **인증/권한 에러 응답**

{  
  "success": false,  
  "error": {  
    "code": "UNAUTHORIZED",  
    "message": "인증 정보가 없습니다",  
    "detail": "유효한 JWT 토큰을 포함해 주세요",  
    "path": "/api/reviews",  
    "method": "POST"  
  },  
  "timestamp": "2025-06-07T05:00:00Z",  
  "requestId": "550e8400-e29b-41d4-a716-446655440007"  
}

{  
  "success": false,  
  "error": {  
    "code": "ACCESS\_DENIED",  
    "message": "접근 권한이 없습니다",  
    "detail": "관리자 권한이 필요한 기능입니다",  
    "requiredRole": "ADMIN",  
    "userRole": "MEMBER",  
    "path": "/api/movies",  
    "method": "POST"  
  },  
  "timestamp": "2025-06-07T05:00:00Z",  
  "requestId": "550e8400-e29b-41d4-a716-446655440008"  
}

### **비즈니스 로직 에러 응답**

{  
  "success": false,  
  "error": {  
    "code": "REVIEW\_ALREADY\_EXISTS",  
    "message": "이미 해당 콘텐츠에 대한 리뷰가 존재합니다",  
    "detail": "기존 리뷰를 수정하거나 삭제 후 다시 작성해주세요",  
    "existingReviewId": 456,  
    "path": "/api/reviews",  
    "method": "POST"  
  },  
  "timestamp": "2025-06-07T05:00:00Z",  
  "requestId": "550e8400-e29b-41d4-a716-446655440009"  
}

### **서버 내부 에러 응답**

{  
  "success": false,  
  "error": {  
    "code": "INTERNAL\_SERVER\_ERROR",  
    "message": "서버 내부 오류가 발생했습니다",  
    "detail": "잠시 후 다시 시도해주세요. 문제가 지속되면 관리자에게 문의해주세요",  
    "path": "/api/movies",  
    "method": "GET"  
  },  
  "timestamp": "2025-06-07T05:00:00Z",  
  "requestId": "550e8400-e29b-41d4-a716-446655440010"  
}

## **5.2.3 특수 응답 형식**

### **검색 결과 응답**

{  
  "success": true,  
  "data": {  
    "movies": {  
      "content": \[...\],  
      "totalElements": 15,  
      "currentPage": 0,  
      "totalPages": 2  
    },  
    "dramas": {  
      "content": \[...\],  
      "totalElements": 8,  
      "currentPage": 0,  
      "totalPages": 1  
    },  
    "searchMetadata": {  
      "query": "아바타",  
      "contentType": "ALL",  
      "totalResults": 23,  
      "searchTime": "45ms",  
      "filters": {...}  
    }  
  },  
  "message": "검색이 완료되었습니다",  
  "timestamp": "2025-06-07T05:00:00Z"  
}

### **통계 응답**

{  
  "success": true,  
  "data": {  
    "summary": {  
      "totalCount": 150,  
      "averageScore": 4.2,  
      "period": "monthly"  
    },  
    "details": {...},  
    "trends": {...},  
    "lastUpdated": "2025-06-07T04:00:00Z"  
  },  
  "message": "통계 조회가 완료되었습니다",  
  "timestamp": "2025-06-07T05:00:00Z"  
}

### **일괄 처리 응답**

{  
  "success": true,  
  "data": {  
    "processed": 10,  
    "succeeded": 8,  
    "failed": 2,  
    "results": \[  
      {  
        "id": 1,  
        "status": "SUCCESS",  
        "message": "처리 완료"  
      },  
      {  
        "id": 2,  
        "status": "FAILED",  
        "message": "권한 없음",  
        "error": "ACCESS\_DENIED"  
      }  
    \]  
  },  
  "message": "일괄 처리가 완료되었습니다",  
  "timestamp": "2025-06-07T05:00:00Z"  
}

---

## **6\. API 문서화**

### **6.1 OpenAPI 3.1.0 스펙**

| openapi | "3.1.0" |
| :---- | :---- |
| info | "3.1.0" |
| title | "MiniPjt5 API" |
| description | "JWT 인증이 적용된 API 문서입니다." |
| version | "v1" |
| servers |  |
| 0 |  |
| url | "[*http://localhost:8080*](http://localhost:8080/)" |
| description | "Generated server url" |
| security |  |
| 0 |  |
| bearerAuth | \[\] |
| tags |  |
| 0 |  |
| name | "통합 검색" |
| description | "영화와 드라마 통합 검색 API" |
| 1 |  |
| name | "장르" |
| description | "장르 관리 API" |
| 2 |  |
| name | "관리자" |
| description | "전체 사용자 조회 및 상태 변경 관련 API" |
| 3 |  |
| name | "마이페이지" |
| description | "사용자 개인의 정보 조회, 변경, 탈퇴 요청 API" |
| 4 |  |
| name | "시청 기록" |
| description | "사용자 시청 기록 관리 API" |
| 5 |  |
| name | "드라마" |
| description | "드라마 관련 API" |
| 6 |  |
| name | "게시글 API" |
| description | "게시글 생성, 조회, 수정, 삭제, 현재 연결된 content(MOVIE or DRAMA) 일부 정보 조회 관련 API" |
| 7 |  |
| name | "로그인" |
| description | "카카오 로그인 콜백 처리 API" |
| 8 |  |
| name | "영화" |
| description | "영화 관련 API" |
| 9 |  |
| name | "별점" |
| description | "별점 관리 API" |
| 10 |  |
| name | "댓글 API" |
| description | "댓글 생성, 조회, 수정, 삭제 관련 API" |
| 11 |  |
| name | "통계" |
| description | "평점 및 통계 관련 API" |
| 12 |  |
| name | "리뷰" |
| description | "리뷰 관리 API" |
| paths |  |
| /api/reviews/{reviewId} |  |
| get |  |
| tags |  |
| 0 | "리뷰" |
| summary | "리뷰 상세 조회" |
| description | "특정 리뷰의 상세 정보 조회" |
| operationId | "getReview" |
| parameters |  |
| 0 |  |
| name | "reviewId" |
| in | "path" |
| description | "리뷰 ID" |
| required | true |
| schema |  |
| type | "integer" |
| format | "int64" |
| example | 1 |
| responses |  |
| 200 |  |
| description | "OK" |
| content |  |
| application/json |  |
| schema | {…} |
| put |  |
| tags |  |
| 0 | "리뷰" |
| summary | "리뷰+별점 수정" |
| description | "기존 리뷰와 별점을 동시에 수정" |
| operationId | "updateReview" |
| parameters |  |
| 0 |  |
| name | "reviewId" |
| in | "path" |
| description | "리뷰 ID" |
| required | true |
| schema |  |
| type | "integer" |
| format | "int64" |
| example | 1 |
| requestBody |  |
| content |  |
| application/json |  |
| schema |  |
| $ref | "\#/components/schemas/UpdateRequest" |
| required | true |
| responses |  |
| 200 |  |
| description | "OK" |
| content |  |
| application/json |  |
| schema | {…} |
| security |  |
| 0 |  |
| bearerAuth | \[\] |

### **6.2 API 문서 생성 도구(Swagger)**

@Configuration  
public class OpenApiConfig {

   @Bean  
   public OpenAPI openAPI() {  
       final String securitySchemeName \= "bearerAuth";

       return new OpenAPI()  
               .info(new Info()  
                       .title("MiniPjt5 API")  
                       .description("JWT 인증이 적용된 API 문서입니다.")  
                       .version("v1"))  
               .components(new Components()  
                       .addSecuritySchemes(securitySchemeName, createBearerScheme()))  
               .addSecurityItem(new SecurityRequirement().addList(securitySchemeName));  
   }

   private SecurityScheme createBearerScheme() {  
       return new SecurityScheme()  
               .name("Authorization")  
               .type(SecurityScheme.Type.*HTTP*)  
               .scheme("bearer")  
               .bearerFormat("JWT");  
   }  
}

// PostController 어노테이션 예시  
@RestController  
@RequestMapping("/api/posts")  
@RequiredArgsConstructor  
@Tag(name \= "게시글 API", description \= "게시글 생성, 조회, 수정, 삭제, 현재 연결된 content(MOVIE or DRAMA) 일부 정보 조회 관련 API")  
public class PostController {  
   private final PostService postService;  
   private final TMDBDataInitializationService tmdbDataInitializationService;

   @Operation(summary \= "전체 게시글 조회")  
   @ApiResponses(value \= {  
           @ApiResponse(responseCode \= "200", description \= "게시글 목록 조회 성공",    
                        content \= @Content(schema \=   
                                @Schema(implementation \= PostDTO.ListResponse.class)))})  
   @GetMapping  
   public ResponseEntity\<List\<PostDTO.ListResponse\>\> getAllPost(){  
       List\<PostDTO.ListResponse\> posts \= postService.getAllPosts();  
       return ResponseEntity.*ok*(posts);  
   }

   @Operation(summary \= "콘텐츠 별 게시글 조회",   
              description \= "type과 id를 통한 콘텐츠 별 게시글 조회")  
   @ApiResponses(value \= {  
           @ApiResponse(responseCode \= "200", description \= "게시글 목록 조회 성공",  
                   content \= @Content(schema \=   
                      @Schema(implementation \= PostDTO.ListResponse.class))),  
           @ApiResponse(responseCode \= "400",   
                        description \= "잘못된 요청입니다.(유효하지 않은 콘텐츠 타입)",  
                   content \= @Content(schema \=   
                      @Schema(implementation \= ErrorResponse.class))),  
           @ApiResponse(responseCode \= "404",   
                        description \= "요청된 콘텐츠(영화/드라마)를 찾을 수 없습니다.",  
                   content \= @Content(schema \=   
                      @Schema(implementation \= ErrorResponse.class)))  
   })  
   @GetMapping("/content")  
   public ResponseEntity\<List\<PostDTO.ListResponse\>\>getPostsByContent(  
                       @Parameter(name \= "type",   
                                 description \= "콘텐츠 타입 (MOVIE 또는 DRAMA)",   
                                 in \= ParameterIn.*QUERY*, required \= true,  
                                       schema \= @Schema(type \= "String",   
                      allowableValues \= {"MOVIE", "DRAMA"}, example \= "MOVIE"))  
                                                                        @RequestParam("type") ContentType contentType,  
                                                                       @Parameter(name \= "id", description \= "콘텐츠 ID (MOVIE ID 또는 DRAMA ID)",   
           in \= ParameterIn.*QUERY*, required \= true,  
           schema \= @Schema(type \= "integer", example \= "1"))                                                                      @RequestParam("id") Long contentId){  
       List\<PostDTO.ListResponse\> posts \= postService.getPostsByContent(contentType, contentId);  
       return ResponseEntity.*ok*(posts);  
   }

   @Operation(summary \= "게시글 단건 조회", description \= "코드를 통한 게시글 단건 조회")  
   @ApiResponses(value \= {  
           @ApiResponse(responseCode \= "200", description \= "게시글 조회 성공",  
                   content \= @Content(schema \=   
                             @Schema(implementation \= PostDTO.Response.class))),  
           @ApiResponse(responseCode \= "404", description \= "게시글을 찾을 수 없습니다.(잘못된 형식의 코드 또는 존재하지 않는 게시글)",  
                   content \= @Content(schema \=   
                             @Schema(implementation \=  ErrorResponse.class)))  
   })  
   @GetMapping("/{code}")  
   public ResponseEntity\<PostDTO.Response\> getPostByCode(  
        @Parameter(name \= "code", description \= "조회할 게시글의 고유 코드",   
                   in \= ParameterIn.*PATH*, required \= true,  
                   schema \= @Schema(type \= "string", example \= "P25052800001"))  
                   @PathVariable String code){  
       PostDTO.Response post \= postService.getPostByCode(code);  
       return ResponseEntity.*ok*(post);  
   }  
---

## **7\. 향후 고도화 방안**

* GraphQL 지원: 클라이언트별 맞춤 데이터 제공  
* WebSocket: 실시간 알림 및 채팅 기능  
* 이벤트 기반 아키텍처: 마이크로서비스 간 느슨한 결합  
* API 게이트웨이: 중앙화된 API 관리

