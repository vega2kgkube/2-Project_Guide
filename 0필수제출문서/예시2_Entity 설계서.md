# **Entity 설계서**

## **문서 정보**

* 프로젝트명: 봤어?  
* 작성자: 김XX, 임XX, 정XX  
* 작성일: 2025-0x-0x

---

## **1\. Entity 설계 개요**

### **1.1 설계 목적**

JPA Entity 클래스 설계를 통해 객체-관계 매핑(ORM)을 정의하고, 비즈니스 도메인을 코드로 표현하여 유지보수가 용이한 시스템을 구축

### **1.2 설계 원칙**

* 단일 책임 원칙: 하나의 Entity는 하나의 비즈니스 개념만 표현  
* 캡슐화: 비즈니스 로직을 Entity 내부에 구현  
* 불변성: 가능한 한 불변 객체로 설계  
* 연관관계 최소화: 필요한 관계만 매핑하여 복잡도 감소

### **1.3 기술 스택**

* ORM 프레임워크: Spring Data JPA 3.4.6  
* 데이터베이스: MariaDB 10.11  
* 검증 프레임워크: Bean Validation 2.0  
* 감사 기능: Spring Data JPA Auditing

---

## **2\. Entity 목록 및 분류**

### **2.1 Entity 분류 매트릭스**

| Entity명 | 유형 | 비즈니스 중요도 | 기술적 복잡도 | 연관관계 수 | 우선순위 |
| ----- | ----- | ----- | ----- | ----- | ----- |
| User | 핵심 | 높음 | 중간 | 4개 | 1순위 |
| Movie | 핵심 | 높음 | 중간 | 3개 | 1순위 |
| Drama | 핵심 | 높음 | 중간 | 3개 | 1순위 |
| Review | 지원 | 중간 | 낮음 | 3개 | 2순위 |
| Post | 지원 | 중간 | 중간 | 2개 | 2순위 |
| Genre | 지원 | 중간 | 낮음 | 2개 | 3순위 |
| Comment | 지원 | 낮음 | 낮음 | 2개 | 3순위 |
| Rating | 지원 | 낮음 | 낮음 | 2개 | 3순위 |

### **2.2 Entity 구조**

![][image1]

---

## **3\. 공통 설계 규칙**

### **3.1 네이밍 규칙**

| 구분 | 규칙 | 예시 | 비고 |
| ----- | ----- | ----- | ----- |
| Entity 클래스명 | PascalCase | User, Movie, Drama | 단수형 사용 |
| 테이블명 | snake\_case | users, movies, dramas | 복수형 사용 |
| 컬럼명 | snake\_case | user\_id, created\_at | 언더스코어 구분 |
| 연관관계 필드 | camelCase | user, posts | 객체 참조명 |
| boolean 필드 | is \+ 형용사 | isActive, isDeleted | 명확한 의미 |

### **3.2 공통 어노테이션 규칙**

// 기본 Entity 구조  
@Entity  
@Table(name \= "테이블명")  
@EntityListeners(AuditingEntityListener.class)  
@NoArgsConstructor(access \= AccessLevel.PROTECTED)  
@Getter  
public class EntityName extends BaseEntity {  
    // 필드 정의  
}

### **3.3 ID 생성 전략**

| Entity | 전략 | 이유 | 예시 |
| ----- | ----- | ----- | ----- |
| User | IDENTITY | MariaDB Auto Increment 활용 | 1, 2, 3, ... |
| Book | IDENTITY | 단순한 순차 증가 | 1, 2, 3, ... |
| Post | IDENTITY | MariaDB Auto Increment 활용 | 1, 2, 3, ... |
| Comment | IDENTITY | MariaDB Auto Increment 활용/ 댓글, 대댓글 여부 계층 구조 지원 | 1, 2, 3, ... |
| Category | IDENTITY | 계층 구조 지원 | 1, 2, 3, ... |

---

## **4\. 상세 Entity 설계**

### **4.1 User Entity**

#### **4.1.1 필드 상세 명세**

#### 

| 필드명 | 데이터 타입 | 컬럼명 | 제약조건 | 설명 | 비즈니스 규칙 |
| ----- | ----- | ----- | ----- | ----- | ----- |
| userId | Long | user\_id | PK, NOT NULL, AUTO\_INCREMENT | 사용자 고유 식별자 | 시스템 자동 생성 |
| kakaoId | String | kakao\_id | UNIQUE, NOT NULL | 카카오 사용자 ID | 카카오 로그인으로만 가입 가능 |
| name | String | name | NOT NULL, LENGTH(2\~50) | 사용자 닉네임 | 2-50자 한글/영문 |
| status | UserStatus | status | NOT NULL, ENUM | 사용자 상태 | ACTIVE, SUSPENDED, WITHDRAWN, DELETED |
| preferredGeners | String | preferred\_geners | LENGTH(300) | 선호 장르 | 쉼표로 구분된 문자열 등 가능 |
| joinDate | LocalDate | join\_date | NOT NULL, DEFAULT(CURRENT\_DATE) | 가입일 | 시스템 자동 입력 |
| profileImageUrl | String | profile\_image\_url | LENGTH(200) | 프로필 이미지 URL | 기본 이미지 설정 가능  |
| role | Role | role | NOT NULL, ENUM | 사용자 권한한 | USER, ADMIN |

**4.1.2 구현 코드 예시**

@Entity  
@Table(name \= "users", indexes \= {  
    @Index(name \= "idx\_user\_number", columnList \= "user\_number"),  
    @Index(name \= "idx\_email", columnList \= "email"),  
    @Index(name \= "idx\_status", columnList \= "status")  
})  
@EntityListeners(AuditingEntityListener.class)  
@NoArgsConstructor(access \= AccessLevel.PROTECTED)  
@Getter  
@Setter  
public class User extends BaseEntity {  
// 사용자 고유 ID (PK)  
@Id  
@GeneratedValue(strategy \= GenerationType.IDENTITY)  
@Column(name \= "user\_id")  
private Long userId;

// 카카오 로그인 ID (Unique, 필수)  
@Column(name \= "kakao\_id", nullable \= false, unique \= true)  
private String kakaoId;

// 사용자 닉네임 (2\~50자)  
@Column(nullable \= false, length \= 50\)  
@NotBlank(message \= "이름은 필수입니다")  
@Size(min \= 2, max \= 50, message \= "이름은 2-50자 사이여야 합니다")  
private String name;

// 사용자 상태 (ENUM: ACTIVE, SUSPENDED, DELETED)  
@Enumerated(EnumType.STRING)  
@Column(nullable \= false)  
private UserStatus status;

// 선호 장르 (쉼표로 구분된 문자열 등)  
@Convert(converter \= ListToStringConverter.class)  
@Column(name \= "preferred\_genres", length \= 300\)  
private List\<String\> preferredGenres \= new ArrayList\<\>();

// 가입일 (기본값: 오늘 날짜)  
@Column(name \= "join\_date", nullable \= false)  
private LocalDate joinDate;

// 프로필 이미지 URL   
@Column(name \= "profile\_image\_url", length \= 200\)  
private String profileImageUrl;

// 사용자 권한 (USER, ADMIN)  
@Enumerated(EnumType.STRING)  
    @Column(nullable \= false)  
    @Setter  
    private UserRole role \= UserRole.USER;

#### **4.1.3 연관관계 매핑**

// 리뷰 목록 (1:N)  
@OneToMany(mappedBy \= "user", cascade \= CascadeType.ALL, fetch \= FetchType.LAZY)  
private List\<Review\> reviews \= new ArrayList\<\>();

// 게시글 목록 (1:N)  
@OneToMany(mappedBy \= "user", cascade \= CascadeType.ALL, fetch \= FetchType.LAZY)  
private List\<Post\> posts \= new ArrayList\<\>();

// 댓글 목록 (1:N)  
@OneToMany(mappedBy \= "user", cascade \= CascadeType.ALL, fetch \= FetchType.LAZY)  
private List\<Comment\> comments \= new ArrayList\<\>();

// 별점 목록 (1:N)  
@OneToMany(mappedBy \= "user", cascade \= CascadeType.ALL, fetch \= FetchType.LAZY)  
private List\<Rating\> ratings \= new ArrayList\<\>();

#### **4.1.4 비즈니스 메서드**

//활성 상태인지 확인  
public boolean isActive() {  
        return this.status \== UserStatus.ACTIVE;  
}

//관리자인지 확인  
public boolean isAdmin() {  
        return this.role \== UserRole.ADMIN;  
    }

//사용자 상태 변경  
public void changeStatus(UserStatus newStatus, String reason) {  
	if (this.status \!= newStatus) {  
          this.status \= newStatus;  
      }  
}  
//프로필 업데이트  
public void updateProfile(String name, String profileImageUrl, List\<String\> preferredGenres) {  
	this.name \= name;  
	this.profileImageUrl \= profileImageUrl;  
	this.preferredGenres \= preferredGenres \!= null ? preferredGenres : new ArrayList\<\>();  
    }  
//가입 날짜 자동 설정  
@PrePersist  
protected void onCreate(){  
	if (this.joinDate \== null) {  
	    this.joinDate \= LocalDate.now();  
	}  
}

#### **4.1.5 생성자 및 팩토리 메서드**

@Builder  
private User(String kakaoId, String name,String profileImageUrl, List\<String\> preferredGenres,  
             UserStatus status, LocalDate joinDate, UserRole role) {  
    this.kakaoId \= kakaoId;  
    this.name \= name;  
    this.profileImageUrl \= profileImageUrl;  
    this.preferredGenres \= preferredGenres \!= null ? preferredGenres : new ArrayList\<\>();  
    this.status \= status \!= null ? status : UserStatus.ACTIVE;  
    this.joinDate \= joinDate \!= null ? joinDate : LocalDate.now();  
    this.role \= role \!= null ? role : UserRole.USER;  
}

public static User create(String kakaoId, String name,String profileImageUrl, List\<String\> preferredGenres) {  
    return User.builder()  
            .kakaoId(kakaoId)  
            .name(name)  
            .profileImageUrl(profileImageUrl)  
            .preferredGenres(preferredGenres)  
            .build();  
}

@Override  
public String toString() {  
    return "{" \+  
            "userId=" \+ userId \+  
            ", kakaoId='" \+ kakaoId \+ '\\'' \+  
            ", name='" \+ name \+ '\\'' \+  
            ", email='" \+ email \+ '\\'' \+  
            ", status=" \+ status \+  
            ", role=" \+ role \+  
            ", preferredGenres=" \+ preferredGenres \+  
            ", joinDate=" \+ joinDate \+  
            '}';  
}

### **4.2 Movie 및 Drama Entity**

#### **4.2.1 Movie 필드 상세 명세**

| 필드명 | 데이터 타입 | 컬럼명 | 제약조건 | 설명 | 비즈니스 규칙 |
| ----- | ----- | ----- | ----- | ----- | ----- |
| id | Long | movie\_id | PK. UNIQUE, NOT NULL, | 고유식별자 | 자동생성 |
| tmdbId | BIGINT | tmdb\_id | UNIQUE, NOT NULL, | 콘텐츠 고유식별자 | AP I 호출 |
| title | String | title | NOT NULL, LENGTH(200) | 영화제목 | 필수 입력 |
| genres | List\<Genre\> | genres |  | 장르 | 필수 입력 |
| voteCount | Integer | vote\_count | NOT NULL 0이상 | 별점 투표수 | 필수입력 |
| voteAverage | Double | vote\_average | NULLABLE, 0이상 10이하하 | 평균 별 | 선택 입력 |
| posterPath | Varchar | poster\_path | NULLABLE LENGTH(255) | 포스터 이미지 | 선택 입력 |
| overview | Text | overview | NULLABLE | 개요 | 선택입력 |
| releaseDate | Date | release\_date | NULLABLE | 개봉일 | 선택 입력 |
| reviews | List\<Review\> | reviews |  | 리뷰 |   |
| ratings | List\<Rating\> | ratings |  | 평점 |  |
| combinedRating | Double | combined\_rating | 0.5이상 5이하 | 통합 평점 |  |

#### **4.2.2 Movie 구현 코드 예시**

@Entity  
@Table(name \= "movies", indexes \= {  
    @Index(name \= "idx\_title", columnList \= "title")}  
@EntityListeners(AuditingEntityListener.class)  
@NoArgsConstructor(access \= AccessLevel.PROTECTED)  
@AllArgsConstructor  
@Getter @Setter @Builder  
public class Movie{

    @Id  
    @GeneratedValue(strategy \= GenerationType.IDENTITY)  
    @Column(name \= "movie\_id" nullable \= false, unique \= true)  
    private Long id;

    @Column(name \= "tmdb\_id" unique \= true, nullable \= false)  
    private Long tmdbId;

    @Column(nullable \= false, length \= 200)  
    @NotBlank(message \= "제목은 필수입니다")  
    @Size(max \= 200, message \= "제목은 200자를 초과할 수 없습니다")  
    private String title;

    @ManyToMany  
    @JoinTable(name \= "genres", joinColumns \=        
    @JoinColumn(name="movie\_id"),   
    inverseJoinColumns \= @JoinColumn(name \= genre\_id)   
    private List\<Integer\> genres \= new ArrayList\<\>();

    @Column(name \= "vote\_count", nullable \= false)  
    @Min(value \= 0, message \= "투표 수는 0 이상이어야 합니다")  
    private Integer voteCount;

    @Column(name \= "vote\_average", nullable \= false)  
    private Double voteAverage;

    @Column(length \= 2000\)  
    private String overview;

    private String posterPath;  
    private String releaseDate;

    // OneToMany \- 리뷰, 별점, 게시글  
    @OneToMany(mappedBy \= "movies", cascade \= CascadeType.ALL, orphanRemoval \= true)  
    private List\<Review\> reviews \= new ArrayList\<\>();  
}

    @OneToMany(mappedBy \= "movie", cascade \= CascadeType.ALL, orphanRemoval \= true)       
    @JsonIgnore     @Builder.Default       
    private List\<Rating\> ratings \= new ArrayList\<\>();   
       
    @Column       
    private Double combinedRating;

#### **4.2.3 Drama 필드 상세 명세**

| 필드명 | 데이터 타입 | 컬럼명 | 제약조건 | 설명 | 비즈니스 규칙 |
| ----- | ----- | ----- | ----- | ----- | ----- |
| id | Long | drama\_id | PK. UNIQUE, NOT NULL, | 고유식별자 | 자동생성 |
| tmdbId | BIGINT | tmdb\_id | UNIQUE, NOT NULL, | 콘텐츠 고유식별자 | AP I 호출 |
| title | String | title | NOT NULL, LENGTH(200) | 드라마제목 | 필수 입력 |
| genres | List\<Genre\> | genres |  | 장르 | 필수 입력 |
| voteCount | Integer | vote\_count | NOT NULL 0이상 | 별점 투표수 | 필수입력 |
| voteAverage | Double | vote\_average | NULLABLE, 0이상 10이하 | 평균 별 | 선택 입력 |
| posterPath | Varchar | poster\_path | NULLABLE LENGTH(255) | 포스터 이미지 | 선택 입력 |
| overview | Text | overview | NULLABLE | 개요 | 선택입력 |
| firstAirDate | Date | first\_air\_date | NULLABLE | 첫 방송일 | 선택 입력 |
| reviews | List\<Review\> | reviews |  | 리뷰 |  |
| ratings | List\<Rating\> | ratings |  | 평점 |  |
| combinedRating | Double | combined\_rating | 0.5이상 5이하 | 통합 평점 |  |

#### **4.2.4 Drama 구현 코드 예시**

@Entity  
@Table(name \= "dramas", indexes \= {  
    @Index(name \= "idx\_title", columnList \= "title")}  
@EntityListeners(AuditingEntityListener.class)  
@NoArgsConstructor(access \= AccessLevel.PROTECTED)  
@AllArgsConstructor  
@Getter @Setter @Builder  
public class Drama {

    @Id  
    @GeneratedValue(strategy \= GenerationType.IDENTITY)  
    @Column(name \= "drama\_id" nullable \= false, unique \= true)  
    private Long id;

    @Column(name \= "tmdb\_id", unique \= true, nullable \= false)  
    private Long tmdbId;

    @Column(nullable \= false, length \= 200)  
    @NotBlank(message \= "제목은 필수입니다")  
    @Size(max \= 200, message \= "제목은 200자를 초과할 수 없습니다")  
    private String title;

    @ManyToMany  
    @JoinTable(name \= "genres", joinColumns \=        
    @JoinColumn(name="drama\_id"),   
    inverseJoinColumns \= @JoinColumn(name \= genre\_id)   
    private List\<Integer\> genres \= new ArrayList\<\>();

    @Column(name \= "vote\_count",nullable \= false)  
    @Min(value \= 0, message \= "투표 수는 0 이상이어야 합니다")  
    private Integer voteCount;

    @Column(name \= "vote\_average", nullable \= false)  
    private Double voteAverage;

    @Column(length \= 2000\)  
    private String overview;

    private String posterPath;  
    private String firstAirDate;  
    private String lastAirDate;  
    private Integer numberOfSeasons;  
    private Integer numberOfEpisodes;

    // OneToMany \- 리뷰, 별점, 게시글  
    @OneToMany(mappedBy \= "dramas", cascade \= CascadeType.ALL, orphanRemoval \= true)  
    private List\<Review\> reviews \= new ArrayList\<\>();  
}

    @OneToMany(mappedBy \= "drama", cascade \= CascadeType.ALL, orphanRemoval \= true)   
    @JsonIgnore @Builder.Default   
    private List\<Rating\> ratings \= new ArrayList\<\>(); 

    @Column   
    private Double combinedRating;

### 

### **4.3 Post Entity**

#### **4.3.1 필드 상세 명세**

| 필드명 | 데이터 타입 | 컬럼명 | 제약조건 | 설명 | 비즈니스 규칙 |
| ----- | ----- | ----- | ----- | ----- | ----- |
| id | Long | post\_id | PK, NOT NULL, AUTO\_INCREMENT | 게시글  내부  식별자 | 시스템 자동 생성 |
| code | String | post\_code | UNIQUE, NOTNULL | 게시글 고유 코드 | 생성 메소드(generatePostCode())를 통해 직접 생성 P+yymmdd+순서로 부여 |
| title | String | post\_title | NOT NULL, LENGTH(100) | 게시글 제목 | 필수 입력, 최대 100자 |
| body | String | post\_body | NOT NULL, LENGTH(255) | 게시글 내용 | 필수 입력, 최대 255자 |
| imageUrl | String | post\_image\_url | NULL 허용 | 게시글 이미지 url |  |
| contentType | ContentType | content\_type | NOT NULL, ENUM | 콘텐츠 타입 | MOVIE 또는 DRAMA |
| contentId | Long | content\_id | NOT NULL | 콘텐츠 ID |  |
| user | User | user\_id | NOT NULL, FK | 게시글 작성자 | FK,  User가 1,Post가 N관계 |
| comments | List\<Comment\> |  | NULL 허용 | 게시글 댓글 | Post가 1, Comment가 N관계 |
| createdAt | LocalDateTime | created\_at | NOT NULL, updatable \= false 패턴은 yyyy-MM-dd HH:mm:ss | 게시글 작성 시간 | 생성 시 자동 입력, 패턴은 yyyy-MM-dd HH:mm:ss BaseEntity상속 |
| updatedAt | LocalDateTime | updated\_at | NULL 허용, 자동 갱신 패턴은 yyyy-MM-dd HH:mm:ss | 게시글 수정 시간 | 수정 시 자동 입력, 최초 post 생성 시 null, 패턴은 yyyy-MM-dd HH:mm:ss BaseEntity상속 |

#### **4.3.2 구현 코드 예시**

@Entity  
@Table(name \= "posts")  
@NoArgsConstructor(access \= AccessLevel.*PROTECTED*)  
@AllArgsConstructor  
@Builder  
@Getter  
@Setter  
public class Post extends BaseEntity {

   @Id  
   @Column(name \= "post\_id")  
   @GeneratedValue(strategy \= GenerationType.*IDENTITY*)  
   private Long id;

   @Column(name \= "post\_code", unique \= true, nullable \= false, length \= 15)  
   private String code;

   @Column(name \= "post\_title", nullable \= false, length \= 100)  
   private String title;

   @Column(name \= "post\_body", nullable \= false, length \= 255)  
   private String body;

   @Column(name \= "post\_image\_url")  
   private String imageUrl;

   *//'MOVIE', 'DRAMA'*  
   @Enumerated(EnumType.*STRING*)  
   @Column(name \= "content\_type", length \= 10, nullable \= false)  
   private ContentType contentType;

   @Column(name \= "content\_id", nullable \= false)  
   private Long contentId;

   @ManyToOne(fetch \= FetchType.*LAZY*)  
   @JoinColumn(name \= "user\_id", nullable \= false)  
   private User user;

   @OneToMany(mappedBy \= "post", cascade \= CascadeType.*ALL*, fetch \= FetchType.*LAZY*)  
   @Builder.Default  
   private List\<Comment\> comments \= new ArrayList\<\>();  
}  
---

### **4.4 Review Entity**

#### **4.4.1 필드 상세 명세**

| 필드명 | 데이터 타입 | 컬럼명 | 제약조건 | 설명 | 비즈니스 규칙 |
| ----- | ----- | ----- | ----- | ----- | ----- |
| id | Long | review\_id | PK, NOT NULL, AUTO\_INCREMENT | 리뷰 고유 식별자 | 시스템 자동 생성 |
| likesCount | Integer | likes\_count | 0이상 | 좋아요 수  |  |
| content | String | content |  NOT NULL, LENGTH(1\~2000) | 리뷰 본문 | 1\~2000자 |
| createdAt | LocalDateTime | created\_at | NOT NULL | 리뷰 작성일 | 시스템 자동 입력 (Auditing 사용) |
| isSpoiler | boolean | is\_spoiler | default=false |  |  |
| user | user | user\_id  | NOT NULL | 리뷰 작성자 (User FK) | User와 N:1 관계 |
| drama | Drama | drama\_id |  | 리뷰 대상드라마의 ID | 드라마 테이블의 ID 또는 외부 ID |
| movie | Movie | movie\_id |  | 리뷰대상영화의ID | 영화 테이블의 ID 또는 외부 ID |
| rating | Rating | rating\_id |  | 같이 묶인 rating의 ID |  |

**4.4.2 구현 코드 예시**  
@Entity  
@Table(name \= "reviews", indexes \= {  
    @Index(name \= "idx\_user\_id", columnList \= "user\_id"),  
    @Index(name \= "idx\_is\_deleted", columnList \= "is\_deleted")  
})  
@EntityListeners(AuditingEntityListener.class)  
@NoArgsConstructor(access \= AccessLevel.PROTECTED)  
@Getter  
public class Review extends BaseEntity {

// 리뷰 고유 식별자 (PK)  
@Id  
@GeneratedValue(strategy \= GenerationType.IDENTITY)  
@Column(name \= "review\_id")  
private Long id;

// 리뷰 제목  
@Column(nullable \= false, length \= 100\)  
@NotBlank(message \= "제목은 필수입니다")  
@Size(min \= 1, max \= 100, message \= "제목은 1\~100자 사이여야 합니다")  
private String title;

// 리뷰 본문  
@Column(nullable \= false, length \= 2000\)  
@NotBlank(message \= "본문은 필수입니다")  
@Size(min \= 1, max \= 2000, message \= "본문은 1\~2000자 사이여야 합니다")  
private String content;

// 드라마 고유 식별자   
@Column(name \= "drama\_id")  
private Long dramaId;

// 영화 고유 식별자  
@Column(name \= "movie\_id")  
private Long movieId;

// 삭제 여부  
@Column(name \= "is\_deleted", nullable \= false)  
private boolean isDeleted \= false;

// 리뷰 작성일  
@CreatedDate  
@Column(name \= "created\_at", nullable \= false, updatable \= false)  
private LocalDateTime createdAt;

// 작성자  
@ManyToOne(fetch \= FetchType.LAZY)  
@JoinColumn(name \= "user\_id", nullable \= false)  
private User user;

@OneToOne(fetch \= FetchType.LAZY)   
@JoinColumn(name \= "rating\_id")   
private Rating rating;

// 좋아요 증가/감소 메서드   
public void increaseLikes() { this.likesCount++; }   
public void decreaseLikes() { if (this.likesCount \> 0\) { this.likesCount--; } }   
// 스포일러 토글   
public void toggleSpoiler() { this.isSpoiler \= \!this.isSpoiler; }   
// 유틸리티 메서드   
public boolean isMovieReview() { return movie \!= null; }   
public boolean isDramaReview() { return drama \!= null; }  
}

**4.4.3 연관관계 매핑**

#### **4.4.4 비즈니스 메서드**

/\*\*  
 \* 리뷰 soft delete 처리  
 \*/  
public void delete() {  
this.isDeleted \= true;  
}  
 /\*\*  
  \* 리뷰 수정  
  \*/  
public void update(String title, String content) {  
	if (StringUtils.hasText(title)) this.title \= title;  
      if (StringUtils.hasText(content)) this.content \= content;  
}  
}

#### **4.4.5 생성자 및 팩토리 메서드**

@Builder  
private Review(String title, String content, Long contentId, User user) {  
        this.title \= title;  
        this.content \= content;  
        this.dramaId \= dramaId;  
        this.movieId \= movieId;  
        this.user \= user;  
        this.isDeleted \= false;  
    }  
/\*\*  
 \* 리뷰 생성 팩토리 메서드  
 \*/

public static Review create(String title, String content, Long contentId, User user) {  
return Review.builder()  
.title(title)  
      .content(content)  
      .dramaId(dramaId)  
      .movieId(movieId)  
      .user(user)  
      .build();

}

### **4.5 Comment Entity**

#### **4.5.1 필드 상세 명세**

| 필드명 | 데이터 타입 | 컬럼명 | 제약조건 | 설명 | 비즈니스 규칙 |
| ----- | ----- | ----- | ----- | ----- | ----- |
| id | Long | comment\_id | PK, NOT NULL, AUTO\_INCREMENT | 댓글  내부  식별자 | 시스템 자동 생성 |
| content | String | comment\_content | NOTNULL, LENGTH(150) | 게시글 고유 코드 | 필수 입력, 최대 150자 |
| parentComment | Comment | parent\_id | NULL 허용 | 부모 댓글 | 자기 참조 관계 |
| childComments | List\<Comment\> |  | NULL 허용 | 자식댓글(대댓글) | 자기 참조 관계  |
| user | User | user\_id | NOT NULL, FK | 댓글 작성자 | FK,  User가 1,Post가 N관계 |
| post | Post | post\_id | NOT NULL, FK | 댓글을 작성한 게시글  | FK,  Post가1,Comment가 N관계 |
| createdAt | LocalDateTime | created\_at | NOT NULL, updatable \= false 패턴은 yyyy-MM-dd HH:mm:ss | 게시글 작성 시간 | 생성 시 자동 입력, 패턴은 yyyy-MM-dd HH:mm:ss BaseEntity상속 |
| updatedAt | LocalDateTime | updated\_at | NULL 허용, 자동 갱신 패턴은 yyyy-MM-dd HH:mm:ss | 게시글 수정 시간 | 수정 시 자동 입력, 최초 post 생성 시 null, 패턴은 yyyy-MM-dd HH:mm:ss BaseEntity상속 |

#### **4.5.2 구현 코드 예시**

@Entity  
@Table(name \= "comments")  
@NoArgsConstructor(access \= AccessLevel.*PROTECTED*)  
@AllArgsConstructor  
@Builder  
@Getter  
@Setter  
public class Comment extends BaseEntity {

   @Id  
   @GeneratedValue(strategy \= GenerationType.*IDENTITY*)  
   @Column(name \= "comment\_id")  
   private Long id;

   @Column(name \= "comment\_content", nullable \= false)  
   private String content;

   @ManyToOne(fetch \= FetchType.*LAZY*)  
   @JoinColumn(name \= "parent\_id")  
   @Setter  
   @JsonBackReference*//무한 순환 참조를 방지하기 위해 사용*  
   private Comment parentComment;

   @OneToMany(mappedBy \= "parentComment", fetch \= FetchType.*LAZY*, cascade \= CascadeType.*ALL*)  
   @JsonManagedReference *//무한 순환 참조를 방지하기 위해 사용*  
   private List\<Comment\> childComments \= new ArrayList\<\>();

   @ManyToOne(fetch \= FetchType.*LAZY*)  
   @JoinColumn(name \= "user\_id", nullable \= false)  
   private User user;

   @ManyToOne(fetch \= FetchType.*LAZY*)  
   @JoinColumn(name \= "post\_id", nullable \= false)  
   private Post post;  
}

---

### **4.6 Genre Entity**

#### **4.6.1  필드 상세 명세**

| 필드명 | 데이터 타입 | 컬럼명 | 제약조건 | 설명 | 비즈니스 규칙 |
| ----- | ----- | ----- | ----- | ----- | ----- |
| id | Long | genre\_id | PK. UNIQUE, NOT NULL, | 고유식별자 | API  ID 동일 |
| name | String | name | NOT NULL, LENGTH(200) | 장르명 | 필수 입력 |
| movies | List\<Movie\> | movies | NOT NULL | 영화 |  |
| dramas | List\<Drama\> | dramas | NOT NULL | 드라마마 |  |

#### **4.2.2 구현 코드 예시**

@Entity  
@Table(name \= "genres")  
@NoArgsConstructor(access \= AccessLevel.PROTECTED)  
@AllArgsConstructor  
@Getter @Setter @Builder  
public class Genre {

    @Id  
    private Long id;

    @Column(unique \= true, nullable \= false)  
    private String name;

    @ManyToMany(mappedBy \= "genres")  
    private List\<Movie\> movies \= new ArrayList\<\>();

    @ManyToMany(mappedBy \= "genres")  
    private List\<Drama\> dramas \= new ArrayList\<\>();

### **4.7 Rating**

#### **4.7.1 필드 상세 명세**

| 필드명 | 데이터 타입 | 컬럼명 | 제약조건 | 설명 | 비즈니스 규칙 |
| ----- | ----- | ----- | ----- | ----- | ----- |
| id | Long | rating\_id | PK, NOT NULL, AUTO\_INCREMENT | 별점 고유 식별자 | 시스템 자동 생성 |
| score | BigDecimal | score | NOT NULL, CHECK(0.5 ≤ score ≤ 5\) | 별점 점수 (0.5\~5점) | 0.5와 5사이의 수 |
| user | User | user\_id | NOT NULL | 별점 작성자 | User와 N:1  |
| drama | Drama | drama\_id |  | 드라마 고유 식별자 | 드라마 테이블의 ID 또는 외부 ID 참조 |
| movie | Movie | movie\_id |  | 영화 고유 식별자 | 영화 테이블의 ID 또는 외부 ID 참조 |
| review  | Review | review\_id |  | 리뷰 고유 식별자 |  |

**4.7.2 구현 코드 예시**  
@Entity  
@Table(name \= "ratings", indexes \= {  
    @Index(name \= "idx\_user\_id", columnList \= "user\_id"),  
    @Index(name \= "idx\_drama\_id", columnList \= "drama\_id"),  
    @Index(name \= "idx\_movie\_id", columnList \= "movie\_id")  
})  
@EntityListeners(AuditingEntityListener.class)  
@NoArgsConstructor(access \= AccessLevel.PROTECTED)  
@Getter  
public class Rating extends BaseEntity {

// 별점 고유 식별자 (PK)  
@Id  
@GeneratedValue(strategy \= GenerationType.IDENTITY)  
@Column(name \= "rating\_id")  
private Long id;

// 별점 점수 (1\~5)  
@Column(nullable \= false)  
@Min(1)  
@Max(5)  
private int score;

// 작성자 (User FK)  
@ManyToOne(fetch \= FetchType.LAZY)  
@JoinColumn(name \= "user\_id", nullable \= false)  
private User user;

// 드라마 고유 식별자   
@Column(name \= "drama\_id", nullable \= true)  
private Long dramaId;

// 영화 고유 식별자  
@Column(name \= "movie\_id", nullable \= true)  
private Long movieId;

@OneToOne(fetch \= FetchType.LAZY)   
@JoinColumn(name \= "review\_id") @Setter   
private Review review;

#### **4.7.3 연관관계 매핑**

// 리뷰 목록 (1:N)  
@OneToMany(mappedBy \= "user", cascade \= CascadeType.ALL, fetch \= FetchType.LAZY)  
private List\<Review\> reviews \= new ArrayList\<\>();

// 게시글 목록 (1:N)  
@OneToMany(mappedBy \= "user", cascade \= CascadeType.ALL, fetch \= FetchType.LAZY)  
private List\<Post\> posts \= new ArrayList\<\>();

// 댓글 목록 (1:N)  
@OneToMany(mappedBy \= "user", cascade \= CascadeType.ALL, fetch \= FetchType.LAZY)  
private List\<Comment\> comments \= new ArrayList\<\>();

// 별점 목록 (1:N)  
@OneToMany(mappedBy \= "user", cascade \= CascadeType.ALL, fetch \= FetchType.LAZY)  
private List\<Rating\> ratings \= new ArrayList\<\>();

// 리뷰 목록 (1:1)  
@OneToOne(fetch \= FetchType.LAZY)   
@JoinColumn(name \= "review\_id") @Setter   
private Review review;

#### **4.7.4 비즈니스 메서드**

public void  pdateScore(int newScore) {  
        if (newScore \>= 1 && newScore \<= 5\) {  
            this.score \= newScore;  
        } else {  
            throw new IllegalArgumentException("별점은 1\~5 사이여야 합니다.");  
        }  
    }  
}

#### **4.7.5 생성자 및 팩토리 메서드**

@Builder  
protected Rating(int score, User user, Long contentId) {  
    this.score \= score;  
    this.user \= user;  
    this.dramaId \= dramaId;  
    this.movieId \= movieId;  
    }

/\*\*  
 \* 별점 생성 팩토리 메서드  
 \*/  
public static Rating create(int score, User user, Long contentId) {  
    return Rating.builder()  
        .score(score)  
        .user(user)  
        .dramaId(dramaId)  
        .movieId(movieId)  
        .build();  
}  
}

## **5\. Enum 타입 정의**

### **5.1 UserStatus** 

public enum UserStatus {  
    *ACTIVE*("활성", "정상적으로 서비스 이용 가능"),  
    *SUSPENDED*("정지", "관리자에 의해 이용이 정지된 상태"),  
    *WITHDRAWN*("탈퇴", "사용자가 탈퇴한 상태");  
    *DELETED*("삭제", "관리자에 의해 삭제된 상태");

    private final String displayName;  
    private final String description;

    UserStatus(String displayName, String description) {  
        this.displayName \= displayName;  
        this.description \= description;  
    }

    public String getDisplayName() {  
        return displayName;  
    }

    public String getDescription() {  
        return description;  
    }

    public boolean isActive() {  
        return this \== ACTIVE;  
    }

    public boolean isDeleted() {  
        return this \== DELETED;  
    }  
}

### **5.2 ContentType**

public enum ContentType {  
   *MOVIE*("영화", "영화 콘텐츠"),  
   *DRAMA*("드라마", "드라마 콘텐츠");

   private final String displayName;  
   private final String description;

   ContentType(String displayName, String description) {  
       this.displayName \= displayName;  
       this.description \= description;  
   }

   public String getDisplayName() {  
       return displayName;  
   }

   public String getDescription() {  
       return description;  
   }  
}

### **5.3 UserRole**

public enum UserRole{  
    *USER*,  
    *ADMIN*  
}

---

## **6\. 연관관계 매핑 전략**

### **6.1 연관관계 매핑 규칙**

| 관계 유형 | 기본 전략 | 이유 | 예외 상황 |
| ----- | ----- | ----- | ----- |
| @ManyToOne | LAZY | 성능 최적화 | 필수 조회 데이터는 EAGER |
| @OneToMany | LAZY | N+1 문제 방지 | 소량 데이터는 EAGER |
| @OneToOne | LAZY | 일관성 유지 | 항상 함께 조회하는 경우 EAGER |
| @ManyToMany | LAZY | N+1 문제 방지 | 없음 |

### **6.2 Cascade 옵션 가이드**

// 부모-자식 관계 (강한 연결)  
@OneToMany(mappedBy \= "movies", cascade \= CascadeType.ALL, orphanRemoval \= true)  
    private List\<Review\> reviews \= new ArrayList\<\>();

@OneToMany(mappedBy \= “parentComment”, fetch \= FetchType.LAZY, cascade \= CascadeType.ALL)  
    @JsonManagedReference //무한 순환 참조를 방지하기 위해 사용  
    private List\<Comment\> childComments;

// 참조 관계 (약한 연결)  
@ManyToOne(fetch \= FetchType.LAZY)  
@JoinColumn(name \= "category\_id")  
private Category category; // cascade 없음  
---

## **7\. 감사(Auditing) 설정**

### **7.1 BaseEntity 구현**

@MappedSuperclass  
@EntityListeners(AuditingEntityListener.class)  
@Getter  
public abstract class BaseEntity {

    @CreatedDate  
    @Column(name \= "created\_at", nullable \= false, updatable \= false)  
    @JsonFormat(pattern \= "yyyy-MM-dd HH:mm:ss")  
    private LocalDateTime createdAt;

    @LastModifiedDate  
    @Column(name \= "updated\_at")  
    @JsonFormat(pattern \= "yyyy-MM-dd HH:mm:ss") // JSON 직렬화 포맷  
    private LocalDateTime updatedAt;  
}

## **10\. 테스트 전략**

### **10.1 단계별 통합 테스트**

@SpringBootTest(webEnvironment \= SpringBootTest.WebEnvironment.RANDOM\_PORT) @ActiveProfiles("test") @TestMethodOrder(MethodOrderer.OrderAnnotation.class) @TestInstance(TestInstance.Lifecycle.PER\_CLASS) // 클래스 레벨에서 인스턴스 관리 class IntegrationTest { 

@Autowired private TestRestTemplate restTemplate; 

@Autowired private GenreRepository genreRepository; 

@Autowired private MovieRepository movieRepository; 

@LocalServerPort private int port; private String baseUrl; 

@BeforeAll 

void setUpOnce() { 

baseUrl \= "http://localhost:" \+ port \+ "/api"; 

// 헬스 체크 대신 실제 API 준비 상태 확인 waitForApiReady(); System.out.println("=== 통합 테스트 시작 \==="); 

System.out.println("총 영화 수: " \+ movieRepository.count()); 

System.out.println("총 장르 수: " \+ genreRepository.count()); } 

private void waitForApiReady() { 

int maxWaitTime \= 30; int waitTime \= 0; 

while (waitTime \< maxWaitTime) { 

try { ResponseEntity\<String\> response \= restTemplate.getForEntity( baseUrl \+ "/content/genres", String.class); 

if (response.getStatusCode().is2xxSuccessful()) { 

System.out.println("API 준비 완료\!"); 

break; } } 

catch (Exception e) { 

// API가 아직 준비되지 않음 } 

try { 

Thread.sleep(1000); 

waitTime++; 

System.out.println("API 준비 대기 중... (" \+ waitTime \+ "s)"); } 

catch (InterruptedException e) { 

Thread.currentThread().interrupt(); 

break; } } } 

@BeforeEach void setUp() { 

// 각 테스트 전에 필요한 최소한의 설정만 

baseUrl \= "http://localhost:" \+ port \+ "/api"; } 

private void waitForDataInitialization() { 

// TMDB 데이터 초기화 완료까지 최대 30초 대기 

int maxWaitTime \= 30; int waitTime \= 0; 

while (movieRepository.count() \< 10 && waitTime \< maxWaitTime) { 

try { Thread.sleep(1000); 

waitTime++; 

System.out.println("데이터 초기화 대기 중... (" \+ waitTime \+ "s)"); } 

catch (InterruptedException e) { 

Thread.currentThread().interrupt(); 

break; } } 

if (movieRepository.count() \< 10\) { 

System.out.println("경고: 데이터 초기화가 완료되지 않았을 수 있습니다."); } } 

@Test @Order(1) 

@DisplayName("헬스 체크 API 테스트") 

void testHealthCheck() { 

// when \- baseUrl에서 /api 제거하고 직접 /actuator/health 호출 

String healthUrl \= "http://localhost:" \+ port \+ "/actuator/health"; ResponseEntity\<Map\> response \= restTemplate.getForEntity(healthUrl, Map.class); 

// then 

assertThat(response.getStatusCode()).isEqualTo(HttpStatus.OK); assertThat(response.getBody()).isNotNull(); 

System.out.println("헬스 체크 응답: " \+ response.getBody()); } 

@Test @Order(2) 

@DisplayName("장르 목록 조회 API 테스트") 

void testGetGenres() { 

// when 

ResponseEntity\<List\> response \= restTemplate.getForEntity( baseUrl \+ "/content/genres", List.class); 

// then 

assertThat(response.getStatusCode()).isEqualTo(HttpStatus.OK); assertThat(response.getBody()).isNotNull(); assertThat(response.getBody()).isNotEmpty(); 

System.out.println("장르 목록 크기: " \+ response.getBody().size()); } 

@Test @Order(3) 

@DisplayName("영화 목록 조회 API 테스트") 

void testGetMovies() { 

ResponseEntity\<List\> response \= restTemplate.getForEntity( baseUrl \+ "/content/movies/popular?page=0\&size=10", List.class); 

assertThat(response.getStatusCode()).isIn(HttpStatus.OK, HttpStatus.NO\_CONTENT); 

System.out.println("영화 목록 응답: " \+ response.getStatusCode()); } 

@Test @Order(4) 

@DisplayName("실제 영화 상세 조회 API 테스트") 

void testGetMovieById() { 

// given \- 실제 존재하는 영화 찾기 

List\<Movie\> movies \= movieRepository.findAll(); assertThat(movies).isNotEmpty(); 

Movie firstMovie \= movies.get(0); 

// when 

ResponseEntity\<Map\> response \= restTemplate.getForEntity( baseUrl \+ "/content/movies/" \+ firstMovie.getId(), Map.class); 

// then 

assertThat(response.getStatusCode()).isEqualTo(HttpStatus.OK); assertThat(response.getBody()).isNotNull(); assertThat(response.getBody().get("title")).isNotNull(); 

System.out.println("영화 상세 정보: " \+ response.getBody().get("title")); } 

@Test @Order(5) 

@DisplayName("영화 검색 API 테스트") 

void testSearchMovies() { 

// given \- 실제 존재하는 영화 제목으로 검색 

List\<Movie\> movies \= movieRepository.findAll(); 

if (movies.isEmpty()) { 

System.out.println("검색할 영화가 없습니다. 테스트를 건너뜁니다."); 

return; } 

String searchTerm \= movies.get(0).getTitle().substring(0, 3); 

// 첫 3글자로 검색 // when 

ResponseEntity\<List\> response \= restTemplate.getForEntity( baseUrl \+ "/content/movies/search?query=" \+ searchTerm \+ "\&useApi=false", List.class);

// then 

assertThat(response.getStatusCode()).isEqualTo(HttpStatus.OK); System.out.println("'" \+ searchTerm \+ "' 검색 결과: " \+ response.getStatusCode()); } 

@Test @Order(6) 

@DisplayName("존재하지 않는 영화 조회 테스트") 

void testGetNonExistentMovie() { 

// when 

ResponseEntity\<Map\> response \= restTemplate.getForEntity( baseUrl \+ "/content/movies/99999", Map.class); 

// then assertThat(response.getStatusCode()).isEqualTo(HttpStatus.NOT\_FOUND); System.out.println("존재하지 않는 영화 조회 결과: " \+ response.getStatusCode()); } 

@Test @Order(7) 

@DisplayName("데이터 통계 확인 테스트") 

void testDataStatistics() { 

long movieCount \= movieRepository.count(); 

long genreCount \= genreRepository.count(); assertThat(movieCount).isGreaterThan(0); assertThat(genreCount).isGreaterThan(0); 

System.out.println("=== 최종 데이터 통계 \==="); 

System.out.println("총 영화 수: " \+ movieCount); 

System.out.println("총 장르 수: " \+ genreCount); // 샘플 영화 정보 출력 List\<Movie\> sampleMovies \= movieRepository.findAll().stream().limit(5).toList(); 

System.out.println("샘플 영화들:"); 

sampleMovies.forEach(movie \-\> System.out.println("- " \+ movie.getTitle() \+ " (TMDB ID: " \+ movie.getTmdbId() \+ ")") ); } 

@AfterAll void tearDown() { System.out.println("=== 통합 테스트 완료 \==="); } }

---

## **11\. 성능 모니터링**

### **11.1 쿼리 성능 모니터링**

### **11.1.1 환경별 설정**

공통 설정 및 개발 환경 (application.properties)  
*\# Jackson JSON 설정*

spring.jackson.default-property-inclusion\=non\_null

spring.jackson.serialization.write-dates-as-timestamps\=false

spring.jackson.deserialization.fail-on-unknown-properties\=false

*\# 서블릿 인코딩 및 포트 설정*

server.servlet.context-path\=/

server.servlet.encoding.charset\=UTF-8

server.servlet.encoding.enabled\=true

server.servlet.encoding.force\=true

server.port\=8080

*\# 캐시 설정 (Caffeine)*

spring.cache.type\=caffeine

spring.cache.caffeine.spec\=initialCapacity=100,maximumSize=1000,expireAfterWrite=30m

*\# 파일 업로드 디렉토리 설정*

file.upload-dir\=src/main/resources/static/images/

*\# Multipart 업로드 제한 설정*

spring.servlet.multipart.max-file-size\=10MB

spring.servlet.multipart.max-request-size\=10MB

*\# TMDB API 관련 설정*

tmdb.api.base-url\=https://api.themoviedb.org/3

tmdb.api.image-base-url\=[https://image.tmdb.org/t/p/w500](https://image.tmdb.org/t/p/w500)

*\# 카카오 로그인 리다이렉트 URI*

kakao.redirect-uri\=http://localhost:8080/api/auth/kakao/callback

*\# 보안 설정 외부 파일 import*

*spring.config.import\=optional:application-secret.properties*

*spring.profiles.include\=secret*

*\# Swagger/OpenAPI 문서화 설정*

springdoc.api-docs.path\=/v3/api-docs

springdoc.swagger-ui.path\=/swagger-ui.html

springdoc.swagger-ui.operationsSorter\=alpha

springdoc.swagger-ui.tagsSorter\=alpha

springdoc.swagger-ui.tryItOutEnabled\=true

springdoc.default-consumes-media-type\=application/json

springdoc.default-produces-media-type\=application/json

spring.mvc.pathmatch.trailing-slash\=true

spring.web.resources.add-mappings\=true

spring.mvc.static-path-pattern\=/\*\*

springdoc.mvc.path\=true

민감 정보 및 환경별 분리 설정 환경 (application.-secret.properties)  
*\# DB 설정*

spring.datasource.url\=jdbc:mariadb://svc.sel4.cloudtype.app:31146/seenit?serverTimezone=Asia/Seoul

spring.datasource.username\=root

spring.datasource.password\=root

spring.datasource.driver-class-name\=org.mariadb.jdbc.Driver

spring.jpa.hibernate.ddl-auto\=update

spring.jpa.show-sql\=false

spring.jpa.properties.hibernate.format\_sql\=false

*\# Hibernate SQL 로그 비활성화*

logging.level.org.hibernate.SQL\=info

logging.level.org.hibernate.orm.jdbc.bind\=info

*\# JWT 설정*

jwt.secret\=7e2KeuXnr9v6teCqsNtwSMI2o78rq2/dpsGklHyY7zhQ7qO5Q0B2SkqnX/M66bvi4Nk7C0LLpTcMmj39BOzrcA==

jwt.access-token-expiration\=360000

jwt.refresh-token-expiration\=604800000

*\# Kakao API*

kakao.client-id\=70275fad0506a970e74c05bdc4575aab

*\# TMDB API*

tmdb.api.key\=fd7f735277043c7f162738d169631400

개발 환경 (application-dev.properties)

spring.config.activate.on-profile\=dev

spring.datasource.url\=  
spring.datasource.username\=  
spring.datasource.password\=  
spring.datasource.driver-class-name\=org.mariadb.jdbc.Driver

spring.jpa.hibernate.ddl-auto\=create-drop  
spring.jpa.show-sql\=true  
spring.jpa.properties.hibernate.format\_sql\=true  
spring.jpa.properties.hibernate.use\_sql\_comments\=true

tmdb.api.max-pages-per-request\=5  
tmdb.api.api-delay-ms\=100

logging.level.com.basic.miniPjt5\=INFO  
logging.level.com.basic.miniPjt5.service.TMDBApiService\=DEBUG  
logging.level.com.basic.miniPjt5.service.TMDBDataInitializationService\=INFO  
logging.level.org.springframework.web\=DEBUG  
logging.level.org.hibernate.SQL\=DEBUG  
logging.level.org.hibernate.type.descriptor.sql.BasicBinder\=TRACE  
logging.pattern.console\=%d{yyyy-MM-dd HH:mm:ss} \- %msg%n  
logging.pattern.file\=%d{yyyy-MM-dd HH:mm:ss} \[%thread\] %-5level %logger{36} \- %msg%n  
logging.file.name\=logs/movie-review-app.log

server.port\=8080

spring.task.scheduling.enabled\=true

spring.jpa.properties.hibernate.default\_batch\_fetch\_size\=100

file.upload-dir\=src/main/resources/static/images/  
---

[image1]: <data:image/png;base64,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>