# **React 컴포넌트 설계서** 

# **문서 정보**

* **작성자**: 서XX, 안XX  
* **작성일**: 2025-0X-0X  
* **버전**: v1.0  
* **검토자**: 서XX, 안XX

---

# **1\. comment**

## **1.1 React 컴포넌트 개요**

### **컴포넌트명**

**CommentSection**

### **목적 및 역할**

* **주요 목적**:  
   게시글 상세 페이지에서 댓글 목록을 보여주고, 댓글 및 대댓글을 작성하거나 수정/삭제할 수 있는 기능을 제공한다.

### **기능 요구사항**

* 댓글 리스트 표시  
* 새 댓글 작성  
* 댓글 수정 및 삭제  
* 대댓글 입력창 상태 제어  
* 모달을 통한 피드백 제공

**1.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입**: \[ \] 페이지 컴포넌트 \[ \] 레이아웃 컴포넌트 \[✔\] 기능 컴포넌트 \[ \] 공통 컴포넌트  
* **복잡도**: \[ \] 단순 (50줄 이하) \[ \] 보통 (50-100줄) \[✔\] 복합 (100줄 이상)  
* **상태 관리**: \[✔\] 로컬 상태만 \[✔\] Redux 연결 \[ \] 상태 없음

### **파일 구조**

`src/`  
`└── components/`  
    `└── comment/`  
        `└── Comment.jsx`

## **1.3 React 컴포넌트 Props 설계**

### **Props 인터페이스**

| Props명 | 타입 | 필수여부 | 기본값 | 설명 |
| ----- | ----- | ----- | ----- | ----- |
| `comments` | array | 필수 | \- | 댓글 목록 배열 |
| `postCode` | string | 필수 | \- | 댓글 대상이 되는 게시글 고유 코드 |
| `showReplyInput` | object | 필수 | \- | 대댓글 입력창 상태 객체 (`{ [id]: true/false }`) |
| `setShowReplyInput` | function | 필수 | \- | 대댓글 입력창 토글 함수 |

## **1.4 상태 관리 설계**

### **로컬 상태 (useState)**

| 상태명 | 타입 | 초기값 | 용도 |
| ----- | ----- | ----- | ----- |
| `editId` | number | null | 수정 중인 댓글 ID |
| `editContent` | string | "" | 수정 중인 댓글 내용 |
| `newComment` | string | "" | 새 댓글 입력 내용 |
| `showSuccessModal` | boolean | false | 댓글 작성 완료 피드백 모달 |
| `showDeleteModal` | boolean | false | 삭제 경고 모달 |
| `showDeleteConfirmModal` | boolean | false | 삭제 완료 모달 |

### **Redux 상태 (해당시)**

| 액션 타입 | Payload | 설명 |
| ----- | ----- | ----- |
| `createComment` | { content, postCode } | 댓글 생성 |
| `updateComment` | { id, content } | 댓글 수정 |
| `deleteComment` | commentId | 댓글 삭제 |
| `fetchCommentsByPost` | postCode | 댓글 목록 불러오기 |
| `state.user.user` | object | 현재 로그인한 사용자 정보 |

## **1.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| CommentSection | components/comment | 댓글 기능 구현 | 중간 |
| WarningModal | components/modal | 작성/입력 경고 | 높음 |
| DeleteModal | components/modal | 삭제 확인 모달 | 높음 |

### **API 연동**

| API 엔드포인트 | 메서드 | 용도 | 호출 시점 |
| ----- | ----- | ----- | ----- |
| `/api/posts/{postCode}/comments` | GET | 댓글 목록 조회 | 마운트 시 |
| `/api/comments` | POST | 댓글 작성 | 작성 시 |
| `/api/comments/{id}` | PUT | 댓글 수정 | 수정 버튼 클릭 시 |
| `/api/comments/{id}` | DELETE | 댓글 삭제 | 삭제 확인 시 |

### **성능 최적화**

* Redux를 통한 상태 일관성 확보  
* `React.memo` 미사용  
* `useCallback`, `useMemo` 미활용 (적용 여지 있음)

### **외부 의존성**

* **라이브러리**: `react-redux`, `@tippyjs/react`  
* **API**: 댓글 관련 액션들 (`commentAction.js`)  
* **스타일**: `PostDetailPage.module.css`  
* **모달**: `WarningModal`, `DeleteModal`

# **2-1. common (CommonHeader)**

## **2.1 React 컴포넌트 개요**

### **컴포넌트명**

**CommonHeader**

### **목적 및 역할**

* **주요 목적**:  
   각 페이지 상단에 고정되는 공통 헤더로, 페이지 제목을 애니메이션과 함께 보여주고 홈 버튼 클릭 시 홈으로 이동한다.

### **기능 요구사항**

* 페이지 타이틀 표시  
* 홈 버튼 클릭 시 `/home`으로 이동  
* 진입 시 페이드/슬라이드 애니메이션 처리

## **2.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입**: \[ \] 페이지 컴포넌트 \[ \] 레이아웃 컴포넌트 \[ \] 기능 컴포넌트 \[✔\] 공통 컴포넌트  
* **복잡도**: \[✔\] 단순 (50줄 이하) \[ \] 보통 (50-100줄) \[ \] 복합 (100줄 이상)  
* **상태 관리**: \[ \] 로컬 상태만 \[ \] Redux 연결 \[✔\] 상태 없음

### **파일 구조**

`src/`  
`└── components/`  
    `└── common/`  
        `├── CommonHeader.jsx`  
        `└── CommonHeader.module.css`

## **2.3 React 컴포넌트 Props 설계**

### **Props 인터페이스**

| Props명 | 타입 | 필수여부 | 기본값 | 설명 |
| ----- | ----- | ----- | ----- | ----- |
| `title` | string | ✔ | 없음 | 페이지 제목 텍스트 값 |

## **2.4 상태 관리 설계**

### **로컬 상태 (useState)**

* ❌ 사용하지 않음

### **Redux 상태 (해당시)**

* ❌ 사용하지 않음

## **2.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| CommonHeader | components/common | 공통 헤더 표시용 UI | 높음 |

### **API 연동**

* ❌ API 연동 없음

### **성능 최적화**

* 최소 렌더링 구조  
* 애니메이션 최적화 (`framer-motion`)  
* React.memo 미사용 (적용 가능)

### **외부 의존성**

* **라이브러리**: `framer-motion`, `react-router-dom`  
* **스타일**: `CommonHeader.module.css`  
* **아이콘**: Font Awesome (홈 아이콘 사용)

# **2-2. common (CommonMovieInfo)**

## **2.1 React 컴포넌트 개요**

### **컴포넌트명**

**CommonMovieInfo**

### **목적 및 역할**

* **주요 목적**:  
   영화 제목, 감독, 포스터 이미지를 간단하고 예쁜 UI로 보여주는 공통 정보 컴포넌트로,  
   게시글 페이지나 리뷰 페이지 같은 곳에서 사용된다.

### **기능 요구사항**

* 영화 포스터 이미지 출력  
* 영화 제목 출력  
* 감독 정보 출력  
* 애니메이션을 통해 부드러운 진입 효과 제공

## **2.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입**: \[ \] 페이지 컴포넌트 \[ \] 레이아웃 컴포넌트 \[ \] 기능 컴포넌트 \[✔\] 공통 컴포넌트  
* **복잡도**: \[✔\] 단순 (50줄 이하)  
* **상태 관리**: \[✔\] 상태 없음

### **파일 구조**

`src/`  
`└── components/`  
    `└── common/`  
        `├── CommonMovieInfo.jsx`  
        `└── CommonMovieInfo.module.css`

## **2.3 React 컴포넌트 Props 설계**

### **Props 인터페이스**

| Props명 | 타입 | 필수여부 | 설명 |
| ----- | ----- | ----- | ----- |
| `title` | string | ✔ | 영화 제목 |
| `director` | string | ✔ | 감독 이름 |
| `poster` | string | ✔ | 영화 포스터 이미지 URL |

## **2.4 상태 관리 설계**

* ❌ 상태 없음 (완전한 presentational 컴포넌트)

## **2.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| CommonMovieInfo | components/common | 영화 정보 UI | 높음 |

### **API 연동**

* ❌ API 없음

### **성능 최적화**

* 매우 가벼운 컴포넌트  
* React.memo 미적용 (필요 시 가능)

### **외부 의존성**

* **라이브러리**: `framer-motion`, `font-awesome`  
* **스타일**: `CommonMovieInfo.module.css`

# **2-3. common (ScrollToTop)**

## **2.1 React 컴포넌트 개요**

### **컴포넌트명**

**ScrollToTop**

### **목적 및 역할**

* **주요 목적**:  
   React Router로 페이지 이동 시 자동으로 화면을 최상단으로 스크롤시키는 역할을 하는 유틸리티형 공통 컴포넌트이다.

### **기능 요구사항**

* 페이지 이동 시 `window.scrollTo(0, 0)` 수행  
* 경로 변경 감지를 위해 `useLocation` 훅 사용

## **2.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입**: \[ \] 페이지 컴포넌트 \[ \] 레이아웃 컴포넌트 \[✔\] 기능 컴포넌트 \[ \] 공통 컴포넌트  
* **복잡도**: \[✔\] 단순 (50줄 이하)  
* **상태 관리**: \[✔\] 상태 없음

### **파일 구조**

`src/`  
`└── components/`  
    `└── common/`  
        `└── ScrollToTop.jsx`

## **2.3 React 컴포넌트 Props 설계**

* ❌ Props 없음

**2.4 상태 관리 설계**

* ❌ 상태 없음

## **2.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| ScrollToTop | components/common | 페이지 최상단 이동 기능 | 높음 |

### **API 연동**

* ❌ API 없음

### **성능 최적화**

* useEffect 최적 적용 (의존성: pathname)

### **외부 의존성**

* 라이브러리: `react-router-dom`  
* API/스타일 없음

# **3-1. home (Header)**

## **3.1 React 컴포넌트 개요**

### **컴포넌트명**

**Header**

### **목적 및 역할**

* **주요 목적:**  
   홈 화면 상단에 사용자 인사말을 보여주고, 클릭 시 프로필 페이지로 이동할 수 있도록 한다.

### **기능 요구사항**

* 사용자 이름 표시  
* 프로필 페이지(`/profile`)로 이동

## **3.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입: \[ \] 페이지 컴포넌트 \[ \] 레이아웃 컴포넌트 \[✔\] 기능 컴포넌트 \[ \] 공통 컴포넌트**  
* **복잡도: \[✔\] 단순 (50줄 이하)**  
* **상태 관리: \[ \] 로컬 상태만 \[✔\] Redux 연결 \[ \] 상태 없음**

### **파일 구조**

**`components/`**

**`└── home/`**

    **`├── Header.jsx`**

    **`└── Header.module.css`**

## **3.3 React 컴포넌트 Props 설계**

* **❌ Props 없음**

## **3.4 상태 관리 설계**

### **로컬 상태 (useState)**

* **❌ 사용하지 않음**

### **Redux 상태 (해당시)**

| 액션/값 | 설명 |
| ----- | ----- |
| **`state.user.user`** | **로그인한 사용자 정보** |

## **3.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| **Header** | **components/home** | **사용자 이름 및 이동 UI** | **보통** |

### **API 연동**

* **❌ API 연동 없음**

### **성능 최적화**

* React.memo  
* useCallback, useMemo 등

### **외부 의존성**

* 라이브러리: `react-router-dom`, `react-redux`  
* 스타일: `Header.module.css`  
* 유틸리티: 없음

**3-2. home (MovieCard)**

## **3.1 React 컴포넌트 개요**

### **컴포넌트명**

**MovieCard**

### **목적 및 역할**

* **주요 목적:**  
   영화 정보를 카드 형태로 표시하고, 클릭 시 상세 페이지로 이동하는 기능을 제공한다.

### **기능 요구사항**

* 영화 포스터 출력  
* 제목, 평점, 리뷰 수 출력  
* 클릭 시 상세 페이지 이동  
* hover 시 콜백 처리 (선택)

## **3.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입: \[ \] 페이지 컴포넌트 \[ \] 레이아웃 컴포넌트 \[✔\] 기능 컴포넌트 \[✔\] 공통 컴포넌트**  
* **복잡도: \[ \] 단순 \[✔\] 보통 (50-100줄)**  
* **상태 관리: \[✔\] props 기반 \[ \] 로컬 상태 \[ \] Redux 연결**

### **파일 구조**

**`components/`**

**`└── home/`**

    **`├── MovieCard.jsx`**

    **`└── MovieCard.css`**

## **3.3 React 컴포넌트 Props 설계**

| Props명 | 타입 | 필수 | 기본값 | 설명 |
| ----- | ----- | ----- | ----- | ----- |
| **`title`** | **string** | **✔** | **\-** | **영화 제목** |
| **`combinedRating`** | **number** | **✔** | **\-** | **통합 평점** |
| **`score`** | **number** | **✔** | **\-** | **사용자 평점** |
| **`reviewCount`** | **number** | **✔** | **\-** | **리뷰 수** |
| **`posterPath`** | **string** | **✔** | **\-** | **포스터 이미지 경로** |
| **`tmdbId`** | **number** | **✔** | **\-** | **영화 고유 ID** |
| **`isUserRated`** | **boolean** | **✘** | **false** | **사용자 평가 여부** |
| **`onHoverStart`** | **function** | **✘** | **\-** | **hover 시작 이벤트** |
| **`onHoverEnd`** | **function** | **✘** | **\-** | **hover 종료 이벤트** |

## **3.4 상태 관리 설계**

### **로컬 상태 (useState)**

* **❌ 사용하지 않음**

### **Redux 상태 (해당시)**

* **❌ 사용하지 않음**

## **3.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| **MovieCard** | **components/home** | **영화 카드 표시** | **높음** |

### **API 연동**

* **❌ API 연동 없음**

### **성능 최적화**

* **React.memo**  
* **useCallback, useMemo 등**

### **외부 의존성**

* **라이브러리: `react-router-dom`**  
* **스타일: `MovieCard.css`**  
* **유틸리티: 없음**

# **3-3. home (MovieRow)**

## **3.1 React 컴포넌트 개요**

### **컴포넌트명**

**MovieRow**

### **목적 및 역할**

* **주요 목적:**  
   **영화 카드들을 가로로 스크롤 배치하고 중심 카드 추적 기능을 제공한다.**

### **기능 요구사항**

* **영화 리스트 가로 스크롤**  
* **중심 카드 자동 감지 및 상태 관리**

**3.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입: \[ \] 페이지 컴포넌트 \[ \] 레이아웃 컴포넌트 \[✔\] 기능 컴포넌트**  
* **복잡도: \[✔\] 보통 (50\~100줄)**  
* **상태 관리: \[✔\] 로컬 상태만 \[ \] Redux 연결**

### **파일 구조**

**`components/`**

**`└── home/`**

    **`└── MovieRow.jsx`**

## **3.3 React 컴포넌트 Props 설계**

| Props명 | 타입 | 필수 | 기본값 | 설명 |
| ----- | ----- | ----- | ----- | ----- |
| **`movies`** | **array** | **✔** | **\-** | **영화 데이터 리스트** |

## **3.4 상태 관리 설계**

### **로컬 상태 (useState)**

| 상태명 | 타입 | 초기값 | 용도 |
| ----- | ----- | ----- | ----- |
| **`centerIndex`** | **number** | **0** | **현재 중심에 위치한 카드 인덱스** |

### **Redux 상태 (해당시)**

* **❌ 사용하지 않음**

## **3.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| **MovieRow** | **components/home** | **영화 리스트 스크롤 뷰** | **보통** |

### **API 연동**

* **❌ API 연동 없음**

### **성능 최적화**

* **React.memo**  
* **useCallback 등**

### **외부 의존성**

* **라이브러리: 없음**  
* **스타일: 없음**  
* **유틸리티: 없음**

# **3-4. home (SearchBar)**

## **3.1 React 컴포넌트 개요**

### **컴포넌트명**

**SearchBar**

### **목적 및 역할**

* **주요 목적:**  
   **클릭하면 검색 기능으로 진입할 수 있는 읽기 전용 형태의 검색 바 역할을 한다.**

### **기능 요구사항**

* **검색창 UI 표시**  
* **입력 비활성화 (`readOnly`)**  
* **클릭 이벤트 처리**

## **3.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입: \[ \] 페이지 컴포넌트 \[ \] 레이아웃 컴포넌트 \[✔\] 기능 컴포넌트**  
* **복잡도: \[✔\] 단순 (50줄 이하)**  
* **상태 관리: \[✔\] props 기반**

### **파일 구조**

**`components/`**

**`└── home/`**

    **`├── SearchBar.jsx`**

    **`└── SearchBar.css`**

## **3.3 React 컴포넌트 Props 설계**

| Props명 | 타입 | 필수 | 기본값 | 설명 |
| ----- | ----- | ----- | ----- | ----- |
| **`onClick`** | **function** | **✔** | **\-** | **검색창 클릭 시 처리 함수** |

## **3.4 상태 관리 설계**

### **로컬 상태 (useState)**

* **❌ 사용하지 않음**

### **Redux 상태 (해당시)**

* **❌ 사용하지 않음**

## **3.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| **SearchBar** | **components/home** | **검색창 트리거 역할** | **보통** |

### **API 연동**

* **❌ API 연동 없음**

### **성능 최적화**

* **React.memo**  
* **useCallback 등**

### **외부 의존성**

* **라이브러리: 없음**  
* **스타일: `SearchBar.css`**  
* **유틸리티: 없음**

# **4-1. modal (DeleteModal)**

## **4.1 React 컴포넌트 개요**

### **컴포넌트명**

**DeleteModal**

### **목적 및 역할**

* **주요 목적:**  
   **삭제를 확인하는 모달창으로, 사용자의 삭제 행동을 중단하거나 확정하게끔 선택지를 제공한다.**

### **기능 요구사항**

* **메시지 표시**  
* **확인(Confirm) 버튼**  
* **취소(Cancel) 버튼**  
* **닫기(X) 버튼**

## **4.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입: \[ \] 페이지 컴포넌트 \[ \] 레이아웃 컴포넌트 \[✔\] 기능 컴포넌트 \[✔\] 공통 컴포넌트**  
* **복잡도: \[✔\] 단순 (50줄 이하)**  
* **상태 관리: \[✔\] props 기반**

### **파일 구조**

**`components/`**

**`└── modal/`**

    **`├── DeleteModal.jsx`**

    **`└── DeleteModal.module.css`**

## **4.3 React 컴포넌트 Props 설계**

| Props명 | 타입 | 필수 | 기본값 | 설명 |
| ----- | ----- | ----- | ----- | ----- |
| **`message`** | **string** | **✔** | **\-** | **모달에 표시할 텍스트** |
| **`onConfirm`** | **function** | **✔** | **\-** | **확인(삭제) 버튼 콜백** |
| **`onCancel`** | **function** | **✔** | **\-** | **취소/닫기 버튼 콜백** |

## **4.4 상태 관리 설계**

### **로컬 상태 (useState)**

* **❌ 사용하지 않음**

### **Redux 상태 (해당시)**

* **❌ 사용하지 않음**

## **4.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| **DeleteModal** | **components/modal** | **삭제 확인 모달창** | **높음** |

### **API 연동**

* **❌ API 연동 없음**

### **성능 최적화**

* **React.memo**  
* **useCallback, useMemo 등**

### **외부 의존성**

* **라이브러리: `framer-motion`**  
* **스타일: `DeleteModal.module.css`**  
  **유틸리티: 없음**

# **4-2. modal (WarningModal)**

## **4.1 React 컴포넌트 개요**

### **컴포넌트명**

**WarningModal**

### **목적 및 역할**

* **주요 목적:**  
   **경고 또는 필수 조치를 안내하는 모달로, 단일 닫기 버튼만 제공해 사용자에게 경고 메시지를 보여준다.**

### **기능 요구사항**

* **경고 메시지 출력**  
* **닫기 버튼 제공**

## **4.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입: \[ \] 페이지 컴포넌트 \[ \] 레이아웃 컴포넌트 \[✔\] 기능 컴포넌트 \[✔\] 공통 컴포넌트**  
* **복잡도: \[✔\] 단순 (50줄 이하)**  
* **상태 관리: \[✔\] props 기반**

### **파일 구조**

**`components/`**

**`└── modal/`**

    **`├── WarningModal.jsx`**

    **`└── WarningModal.module.css`**

## **4.3 React 컴포넌트 Props 설계**

| Props명 | 타입 | 필수 | 기본값 | 설명 |
| ----- | ----- | ----- | ----- | ----- |
| **`message`** | **string** | **✔** | **\-** | **모달에 표시할 경고 텍스트** |
| **`onClose`** | **function** | **✔** | **\-** | **닫기 버튼 클릭 핸들러** |

## **4.4 상태 관리 설계**

### **로컬 상태 (useState)**

* **❌ 사용하지 않음**

### **Redux 상태 (해당시)**

* **❌ 사용하지 않음**

## **4.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| **WarningModal** | **components/modal** | **단순 경고용 모달창** | **높음** |

### **API 연동**

* **❌ API 연동 없음**

### **성능 최적화**

* **React.memo**  
* **useCallback 등**

### **외부 의존성**

* **라이브러리: `framer-motion`**  
* **스타일: `WarningModal.module.css`**  
* **유틸리티: 없음**

# **5-1. search (SearchPopup)**

## **5.1 React 컴포넌트 개요**

### **컴포넌트명**

**SearchPopup**

### **목적 및 역할**

* **주요 목적**:  
   검색창을 클릭했을 때 등장하는 영화 추천 팝업으로, 검색 결과 또는 추천 결과를 `Swiper`로 슬라이드하면서 보여준다.

### **기능 요구사항**

* 검색어에 따라 영화 목록 조회  
* 영화 카드 슬라이드(Swiper)로 출력  
* 영화 클릭 시 상세 페이지 이동  
* 페이지 진입 시 애니메이션 등장 효과

## **5.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입**: \[ \] 페이지 컴포넌트 \[ \] 레이아웃 컴포넌트 \[✔\] 기능 컴포넌트  
* **복잡도**: \[ \] 단순 \[ \] 보통 \[✔\] 복합 (100줄 이상)  
* **상태 관리**: \[✔\] 로컬 상태만 \[✔\] Redux 연결

### **파일 구조**

`components/`  
`└── search/`  
    `├── SearchPopup.jsx`  
    `└── SearchPopup.module.css`

## **5.3 React 컴포넌트 Props 설계**

* ❌ Props 없음 (내부 상태 및 Redux로 처리)

## **5.4 상태 관리 설계**

### **로컬 상태 (useState)**

| 상태명 | 타입 | 초기값 | 용도 |
| ----- | ----- | ----- | ----- |
| `query` | string | "" | 검색어 문자열 저장 |

### **Redux 상태 (해당시)**

| 액션/값 | 설명 |
| ----- | ----- |
| `fetchSearchMovies` | 검색어 기반 영화 목록 요청 |
| `state.search` | 검색된 영화 결과 상태 |

## **5.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| SearchPopup | components/search | 팝업 UI \+ 검색 결과 출력 | 보통 |
| MovieCard | components/home | 영화 카드 UI | 높음 |

### **API 연동**

| API 엔드포인트 | 메서드 | 용도 | 호출 시점 |
| ----- | ----- | ----- | ----- |
| `/api/movies/search?query=` | GET | 영화 검색 결과 요청 | 검색어 입력 시 |

### **성능 최적화**

* React.memo  
* \[✔\] 검색어 변경에 따른 `useEffect` 최적 호출  
* useCallback, useMemo 등 미사용

### **외부 의존성**

* **라이브러리**: `framer-motion`, `swiper`, `react-redux`, `react-router-dom`  
* **스타일**: `SearchPopup.module.css`  
* **유틸리티**: 없음

# **6-1. splash (SplashScreen)**

## **6.1 React 컴포넌트 개요**

### **컴포넌트명**

**SplashScreen**

### **목적 및 역할**

* **주요 목적**:  
   앱 최초 진입 시 브랜드명을 보여주는 인트로 스플래시 화면을 담당한다.

### **기능 요구사항**

* 프로젝트 타이틀 출력  
* 서브 메시지 출력

## **6.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입**: \[✔\] 페이지 컴포넌트 \[ \] 레이아웃 컴포넌트 \[ \] 기능 컴포넌트  
* **복잡도**: \[✔\] 단순 (50줄 이하)  
* **상태 관리**: \[ \] 로컬 상태만 \[ \] Redux 연결 \[✔\] 상태 없음

### **파일 구조**

`components/`  
`└── splash/`  
    `├── SplashScreen.jsx`  
    `└── SplashScreen.css`

## **6.3 React 컴포넌트 Props 설계**

* ❌ Props 없음

## **6.4 상태 관리 설계**

### **로컬 상태 (useState)**

* ❌ 사용하지 않음

### **Redux 상태 (해당시)**

* ❌ 사용하지 않음

## **6.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| SplashScreen | components/splash | 초기 스플래시 문구 출력용 | 낮음 |

### **API 연동**

* ❌ API 연동 없음

### **성능 최적화**

* React.memo  
* useCallback 등

### **외부 의존성**

* **라이브러리**: 없음  
* **스타일**: `SplashScreen.css`  
* **유틸리티**: 없음

# **6-2. splash (SignupSplashScreen)**

## **6.1 React 컴포넌트 개요**

### **컴포넌트명**

**SignupSplashScreen**

### **목적 및 역할**

* **주요 목적**:  
   회원가입 시작 전 "환영합니다" 메시지를 보여주고, 2.5초 후 자동으로 회원가입 ID 입력 페이지로 이동한다.

### **기능 요구사항**

* 환영 메시지 출력  
* 2.5초 후 `/signup/id`로 자동 이동  
* 타이머 클린업 처리

## **6.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입**: \[✔\] 페이지 컴포넌트 \[ \] 레이아웃 컴포넌트 \[ \] 기능 컴포넌트  
* **복잡도**: \[✔\] 단순 (50줄 이하)  
* **상태 관리**: \[✔\] 로컬 상태만 \[ \] Redux 연결

### **파일 구조**

`components/`  
`└── splash/`  
    `├── SignupSplashScreen.jsx`  
    `└── SignupSplashScreen.css`

## **6.3 React 컴포넌트 Props 설계**

* ❌ Props 없음

## **6.4 상태 관리 설계**

### **로컬 상태 (useState)**

* ❌ 직접적인 상태는 없지만 `useEffect`로 타이머 제어

### **Redux 상태 (해당시)**

* ❌ 사용하지 않음

## **6.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| SignupSplashScreen | components/splash | 회원가입 진입 전 스플래시 화면 | 낮음 |

### **API 연동**

* ❌ API 연동 없음

### **성능 최적화**

* React.memo  
* useEffect \+ setTimeout \+ 클린업 적용

### **외부 의존성**

* **라이브러리**: `react-router-dom`  
* **스타일**: `SignupSplashScreen.css`  
* **유틸리티**: 없음

# **7-1. pages (AllMoviesPage)**

## **7.1. React 컴포넌트 개요**

### **컴포넌트명**

**AllMoviesPage**

### **목적 및 역할**

* **주요 목적**:  
   모든 영화 데이터를 필터링해서 목록으로 보여주는 전체 영화 페이지로,  
   인기순 / 최신순 / 추천순 정렬 기능과 페이지네이션까지 지원한다.

### **기능 요구사항**

* 영화 리스트 필터링: 인기, 최신, 추천  
* 각 필터에 맞는 API 호출  
* 영화 카드(MovieCard)로 리스트 표시  
* 페이지네이션 버튼 (다음 페이지 로드)

**7.2. 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입**: \[✔\] 페이지 컴포넌트 \[ \] 레이아웃 컴포넌트 \[ \] 기능 컴포넌트  
* **복잡도**: \[ \] 단순 \[✔\] 보통 \[ \] 복합  
* **상태 관리**: \[✔\] 로컬 상태만 \[✔\] Redux 연결

### **파일 구조**

pages/  
└── AllMoviesPage.jsx  
    AllMoviesPage.module.css  
components/  
└── home/  
    └── MovieCard.jsx

## **7.3. React 컴포넌트 Props 설계**

* ❌ Props 없음

## **7.4. 상태 관리 설계**

### **로컬 상태 (useState)**

| 상태명 | 타입 | 초기값 | 용도 |
| ----- | ----- | ----- | ----- |
| `filter` | string | "popular" | 현재 선택된 필터 |
| `currentPage` | number | 1 | 현재 페이지 번호 |

### **Redux 상태 (useSelector)**

| 상태명 | 설명 |
| ----- | ----- |
| `state.movies.popular` | 인기 영화 목록 |
| `state.movies.latest` | 최신 영화 목록 |
| `state.movies.recommended` | 추천 영화 목록 |

## **7.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| MovieCard | components/home | 영화 카드 | 높음 |
| AllMoviesPage | pages/ | 전체 영화 리스트 페이지 | 중간 |

### **API 연동**

| API 엔드포인트 | 메서드 | 용도 | 호출 시점 |
| ----- | ----- | ----- | ----- |
| `/api/movies/popular` | GET | 인기 영화 | 필터가 popular일 때 |
| `/api/movies/latest` | GET | 최신 영화 | 필터가 latest일 때 |
| `/api/movies/recommended` | GET | 추천 영화 | 필터가 recommended일 때 |

### **성능 최적화**

* React.memo  
* \[✔\] useEffect 의존성 최적화  
* useCallback 등

### **외부 의존성**

* **라이브러리**: `react-redux`, `react-router-dom`  
* **스타일**: `AllMoviesPage.module.css`  
* **유틸리티**: 없음

# **7-2. pages (AuthCallback)**

## **7.2.1 React 컴포넌트 개요**

### **컴포넌트명**

**AuthCallback**

### **목적 및 역할**

* **주요 목적**:  
   소셜 로그인 후 리디렉션된 페이지에서 토큰과 사용자 정보를 처리하고, 로그인 상태를 저장하며 메인으로 자동 이동

### **기능 요구사항**

* URL에서 `token` 및 `isNew` 파라미터 추출  
* Redux에 인증 토큰 저장  
* 신규 유저일 경우 회원가입 이동  
* 기존 유저일 경우 메인 이동  
* 예외 처리 시 에러 상태 저장

## **7.2.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입**: \[✔\] 페이지 컴포넌트  
* **복잡도**: \[✔\] 단순  
* **상태 관리**: \[✔\] Redux 연결

### **파일 구조**

pages/

└── AuthCallback.jsx

reducers/

└── authSlice.js

**7.2.3 React 컴포넌트 Props 설계**

* ❌ Props 없음

## **7.2.4 상태 관리 설계**

### **Redux 상태 (useDispatch)**

| 액션/상태 | 설명 |
| ----- | ----- |
| `setToken(token)` | Redux에 로그인 토큰 저장 |
| `setAuthError(err)` | 인증 실패 시 에러 저장 |

## **7.2.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| AuthCallback | pages/ | 인증 후 처리 화면 | 낮음 |

### **API 연동**

* ❌ 직접적인 API 호출 없음 (파라미터 기반 처리)

### **성능 최적화**

* React.memo  
* \[✔\] useEffect 내 조건 분기 최적화

### **외부 의존성**

* **라이브러리**: `react-router-dom`, `react-redux`  
* **유틸리티**: 없음

# **7-3. pages (DetailPage)**

## **7.3.1 React 컴포넌트 개요**

### **컴포넌트명**

**DetailPage**

### **목적 및 역할**

* **주요 목적**:  
   영화 포스터, 제목, 개요 등 TMDB 기반 영화 정보를 출력하고, 해당 영화에 대한 게시글 및 리뷰 수를 보여주는 상세 페이지

### **기능 요구사항**

* `:id`를 통해 영화 TMDB ID 받아오기

* TMDB API에서 영화 정보 요청  
* Redux 통해 게시글 수 조회  
* 영화 정보 UI 구성  
* 리뷰/게시글 이동 버튼

## **7.3.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입**: \[✔\] 페이지 컴포넌트  
* **복잡도**: \[✔\] 보통  
* **상태 관리**: \[✔\] 로컬 상태만 \[✔\] Redux 연결

### **파일 구조**

pages/

└── DetailPage.jsx

components/

└── common/

    └── CommonHeader.jsx

## **7.3.3 React 컴포넌트 Props 설계**

* ❌ Props 없음

## **7.3.4 상태 관리 설계**

### **로컬 상태 (useState)**

| 상태명 | 타입 | 초기값 | 용도 |
| ----- | ----- | ----- | ----- |
| `movie` | object | null | TMDB 영화 정보 저장 |

### 

### **Redux 상태 (useDispatch)**

| 액션 | 설명 |
| ----- | ----- |
| `fetchPostCountByContent` | 게시글 수 조회 요청 |

## **7.3.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| CommonHeader | components/common | 상단 공통 헤더 | 높음 |
| DetailPage | pages/ | 영화 상세 보기 페이지 | 낮음 |

### **API 연동**

| API 엔드포인트 | 메서드 | 용도 | 호출 시점 |
| ----- | ----- | ----- | ----- |
| `https://api.themoviedb.org/3/movie/:id` | GET | 영화 정보 조회 | 페이지 진입 시 |
| `/api/posts/content` | GET | 게시글 수 요청 | 영화 정보 렌더링 후 |

### **성능 최적화**

* \[✔\] useEffect \+ 의존성 제어  
* React.memo 등 미사용

### **외부 의존성**

* **라이브러리**: `axios`, `react-redux`, `react-router-dom`, `framer-motion`  
* **스타일**: `DetailPage.module.css`  
* **유틸리티**: 없음

# **7-4. pages (EditPostPage)**

## **7.4.1 React 컴포넌트 개요**

### **컴포넌트명**

**EditPostPage**

### **목적 및 역할**

* **주요 목적**:  
   기존 게시글을 수정할 수 있도록 게시글 데이터를 미리 채워서 폼을 렌더링하고, 수정 완료 시 저장 요청을 처리

### **기능 요구사항**

* location state로 기존 데이터 전달 받기  
* 제목, 내용 입력 필드 렌더링  
* 작성 완료 시 PUT 요청  
* 유효성 검증 및 모달 알림 처리

## **7.4.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입**: \[✔\] 페이지 컴포넌트  
* **복잡도**: \[✔\] 보통  
* **상태 관리**: \[✔\] 로컬 상태만

### **파일 구조**

pages/

└── EditPostPage.jsx

    WritePostPage.module.css

components/

└── common/

    └── CommonMovieInfo.jsx

    └── CommonHeader.jsx

modal/

└── WarningModal.jsx

## **7.4.3 React 컴포넌트 Props 설계**

* ❌ Props 없음

## **7.4.4 상태 관리 설계**

### **로컬 상태 (useState)**

| 상태명 | 타입 | 초기값 | 용도 |
| ----- | ----- | ----- | ----- |
| `title` | string | "" | 게시글 제목 |
| `content` | string | "" | 게시글 내용 |
| `showWarningModal` | bool | false | 유효성 실패 모달 표시 여부 |

## **7.4.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| CommonHeader | components/common | 헤더 | 높음 |
| CommonMovieInfo | components/common | 영화 정보 표시 | 보통 |
| WarningModal | components/modal | 유효성 경고 모달 | 보통 |

### **API 연동**

| API 엔드포인트 | 메서드 | 용도 | 호출 시점 |
| ----- | ----- | ----- | ----- |
| `/api/posts/{postId}` | PUT | 게시글 수정 | 저장 버튼 클릭 시 |

### **성능 최적화**

* React.memo  
* \[✔\] useEffect로 초기값 채우기

### **외부 의존성**

* **라이브러리**: `react-router-dom`, `axios`, `framer-motion`  
* **스타일**: `WritePostPage.module.css`  
* **유틸리티**: 없음

# **7-5. pages (HomePage)**

## **7.5.1 React 컴포넌트 개요**

### **컴포넌트명**

**HomePage**

### **목적 및 역할**

* **주요 목적**:  
   사용자 맞춤 인기 영화와 최근 시청 영화 데이터를 보여주고, 검색 팝업도 함께 제어하는 메인 홈 화면

### **기능 요구사항**

* 유저 정보 조회  
* 인기 영화, 시청 영화 API 호출  
* 카드 리스트로 렌더링  
* 검색창 클릭 시 팝업 활성화

## **7.5.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입**: \[✔\] 페이지 컴포넌트  
* **복잡도**: \[✔\] 보통  
* **상태 관리**: \[✔\] 로컬 상태만 \[✔\] Redux 연결

### **파일 구조**

pages/

└── HomePage.jsx

    HomePage.css

components/

└── home/

    ├── Header.jsx

    ├── SearchBar.jsx

    ├── MovieCard.jsx

└── search/

    └── SearchPopup.jsx

## **7.5.3 React 컴포넌트 Props 설계**

* ❌ Props 없음

## **7.5.4 상태 관리 설계**

### **로컬 상태 (useState)**

| 상태명 | 타입 | 초기값 | 용도 |
| ----- | ----- | ----- | ----- |
| `isPopupOpen` | boolean | false | 검색창 열림 여부 |

### **Redux 상태 (useSelector)**

| 상태명 | 설명 |
| ----- | ----- |
| `state.user.userInfo` | 사용자 정보 |
| `state.movies.popular` | 인기 영화 목록 |
| `state.movies.ratedMovies` | 시청한 영화 목록 |

## **7.5.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| Header | components/home | 홈 상단 헤더 | 높음 |
| MovieCard | components/home | 영화 카드 | 높음 |
| SearchPopup | components/search | 검색 팝업 | 보통 |

### **API 연동**

| API 엔드포인트 | 메서드 | 용도 | 호출 시점 |
| ----- | ----- | ----- | ----- |
| `/api/users/me` | GET | 유저 정보 | 페이지 로드 시 |
| `/api/movies/popular` | GET | 인기 영화 목록 | 페이지 로드 시 |
| `/api/movies/rated` | GET | 시청한 영화 목록 | 페이지 로드 시 |

### **성능 최적화**

* \[✔\] useEffect 최소화  
* React.memo

### **외부 의존성**

* **라이브러리**: `react-redux`, `axios`, `react-router-dom`  
* **스타일**: `HomePage.css`  
* **유틸리티**: 없음

# **7-6. pages (MySeenMoviePage)**

## **7.6.1 React 컴포넌트 개요**

### **컴포넌트명**

**MySeenMoviePage**

### **목적 및 역할**

* **주요 목적**:  
   사용자가 시청한 영화 목록을 페이지네이션 방식으로 렌더링해서 보여주는 개인화된 페이지

### **기능 요구사항**

* Redux에서 시청 영화 리스트 조회  
* 페이지네이션 처리  
* MovieCard로 목록 출력

## **7.6.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입**: \[✔\] 페이지 컴포넌트  
* **복잡도**: \[✔\] 보통  
* **상태 관리**: \[✔\] 로컬 상태만 \[✔\] Redux 연결

### **파일 구조**

pages/

└── MySeenMoviePage.jsx

    MySeenMoviePage.module.css

components/

└── home/

    └── MovieCard.jsx

## **7.6.3 React 컴포넌트 Props 설계**

* ❌ Props 없음

## **7.6.4 상태 관리 설계**

### **로컬 상태 (useState)**

| 상태명 | 타입 | 초기값 | 용도 |
| ----- | ----- | ----- | ----- |
| `currentPage` | number | 1 | 현재 페이지 |

### **Redux 상태 (useSelector)**

| 상태명 | 설명 |
| ----- | ----- |
| `state.movies.ratedMovies` | 시청한 영화 목록 |

## **7.6.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| MovieCard | components/home | 영화 카드 | 높음 |
| MySeenMoviePage | pages/ | 본 영화 목록 페이지 | 낮음 |

### **API 연동**

| API 엔드포인트 | 메서드 | 용도 | 호출 시점 |
| ----- | ----- | ----- | ----- |
| `/api/movies/rated` | GET | 시청 영화 목록 조회 | 페이지 로드 시 |

### **성능 최적화**

* \[✔\] useEffect \+ deps 최적화  
* React.memo 등 미사용

### **외부 의존성**

* **라이브러리**: `react-redux`  
* **스타일**: `MySeenMoviePage.module.css`  
* **유틸리티**: 없음

# **7-7. pages (PostDetailPage)**

## **7.7.1 React 컴포넌트 개요**

### **컴포넌트명**

**PostDetailPage**

### **목적 및 역할**

* **주요 목적**:  
   특정 게시글의 상세 내용을 보여주고, 댓글 조회 및 작성, 수정, 삭제를 할 수 있는 뷰를 제공

### **기능 요구사항**

* 게시글 ID로 상세 데이터 요청  
* 댓글 리스트 불러오기  
* 댓글 작성, 수정, 삭제  
* 게시글 수정, 삭제 모달 제어

## **7.7.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입**: \[✔\] 페이지 컴포넌트  
  **복잡도**: \[✔\] 복합  
* **상태 관리**: \[✔\] 로컬 상태 \[✔\] Redux

### **파일 구조**

pages/

└── PostDetailPage.jsx

components/

  └── comment/

      └── Comment.jsx

modal/

  ├── DeleteModal.jsx

  └── WarningModal.jsx

## **7.7.3 React 컴포넌트 Props 설계**

* ❌ Props 없음

## **7.7.4 상태 관리 설계**

### **로컬 상태 (useState)**

| 상태명 | 타입 | 초기값 | 용도 |
| ----- | ----- | ----- | ----- |
| `selectedCommentId` | number | null | 수정 대상 댓글 ID |
| `editingContent` | string | "" | 수정 중 댓글 내용 |
| `showDeleteModal` | boolean | false | 삭제 모달 표시 여부 |

### **Redux 상태 (useDispatch \+ useSelector)**

| 액션/상태 | 설명 |
| ----- | ----- |
| `fetchPostByCode(code)` | 게시글 상세 불러오기 |
| `fetchCommentsByPost(code)` | 댓글 리스트 불러오기 |

## **7.7.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| Comment | components/comment | 댓글 출력 | 보통 |
| DeleteModal | components/modal | 삭제 확인 모달 | 보통 |
| WarningModal | components/modal | 경고 모달 | 보통 |

### **API 연동**

| API 엔드포인트 | 메서드 | 용도 | 호출 시점 |
| ----- | ----- | ----- | ----- |
| `/api/posts/{postCode}` | GET | 게시글 상세 조회 | 페이지 진입 시 |
| `/api/posts/{postCode}/comments` | GET | 댓글 리스트 조회 | 페이지 진입 시 |
| `/api/comments` | POST | 댓글 작성 | 작성 버튼 클릭 시 |
| `/api/comments/{id}` | PUT | 댓글 수정 | 수정 버튼 클릭 시 |
| `/api/comments/{id}` | DELETE | 댓글 삭제 | 삭제 버튼 클릭 시 |

### **성능 최적화**

* \[✔\] useEffect deps 관리  
* React.memo 등은 미사용

### **외부 의존성**

* **라이브러리**: `react-router-dom`, `axios`, `redux`, `framer-motion`  
* **스타일**: `PostDetailPage.module.css`

# **7-8. pages (PostPage)**

## **7.8.1 React 컴포넌트 개요**

### **컴포넌트명**

**PostPage**

### **목적 및 역할**

* **주요 목적**:  
   특정 영화에 대한 유저 게시글을 리스트로 보여주는 페이지  
   해당 영화와 관련된 게시글만 필터링해서 보여줌.

### **기능 요구사항**

* 영화 ID 기준 게시글 리스트 조회  
* 게시글 클릭 시 상세 이동  
* 게시글 개수 표시

## **7.8.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입**: \[✔\] 페이지 컴포넌트  
* **복잡도**: \[✔\] 보통  
* **상태 관리**: \[✔\] Redux \[✔\] 로컬 상태

### **파일 구조**

pages/

└── PostPage.jsx

components/

  └── common/

      └── CommonMovieInfo.jsx

## **7.8.3 React 컴포넌트 Props 설계**

* ❌ Props 없음

## **7.8.4 상태 관리 설계**

### **로컬 상태 (useState)**

| 상태명 | 타입 | 초기값 | 용도 |
| ----- | ----- | ----- | ----- |
| `loading` | bool | true | 로딩 여부 |

### **Redux 상태 (useDispatch \+ useSelector)**

| 액션/상태 | 설명 |
| ----- | ----- |
| `fetchPostsByMovieId` | 게시글 리스트 조회 |

## **7.8.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| CommonMovieInfo | components/common | 영화 정보 표시 | 높음 |
| PostPage | pages/ | 게시글 리스트 페이지 | 낮음 |

### **API 연동**

| API 엔드포인트 | 메서드 | 용도 | 호출 시점 |
| ----- | ----- | ----- | ----- |
| `/api/posts/movie/{id}` | GET | 영화별 게시글 조회 | 페이지 진입 시 |

### **성능 최적화**

* \[✔\] useEffect 최적화  
* React.memo 등은 없음

### **외부 의존성**

* **라이브러리**: `axios`, `redux`, `react-router-dom`  
* **스타일**: `PostPage.module.css`

# **7-9. pages (ProfilePage)**

## **7.9.1 React 컴포넌트 개요**

### **컴포넌트명**

**ProfilePage**

### **목적 및 역할**

* **주요 목적**:  
   사용자 프로필 정보를 보여주고, 닉네임 변경이나 회원 탈퇴를 할 수 있는 설정 페이지

### **기능 요구사항**

* Redux에서 유저 정보 가져오기  
* 닉네임 수정 후 저장 요청  
* 회원 탈퇴 요청  
* 수정/삭제 후 리디렉션

## **7.9.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입**: \[✔\] 페이지 컴포넌트  
* **복잡도**: \[✔\] 보통  
* **상태 관리**: \[✔\] 로컬 상태만 \[✔\] Redux

### **파일 구조**

pages/

└── ProfilePage.jsx

    ProfilePage.module.css

## **7.9.3 React 컴포넌트 Props 설계**

* ❌ Props 없음

## **7.9.4 상태 관리 설계**

### **로컬 상태 (useState)**

| 상태명 | 타입 | 초기값 | 용도 |
| ----- | ----- | ----- | ----- |
| `nickname` | string | "" | 사용자 닉네임 입력 |

### **Redux 상태 (useSelector \+ useDispatch)**

| 액션/상태 | 설명 |
| ----- | ----- |
| `state.user.user` | 사용자 정보 |
| `updateUserInfo` | 닉네임 수정 |
| `deleteUserAccount` | 회원 탈퇴 요청 |

## **7.9.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| ProfilePage | pages/ | 프로필 설정 페이지 | 낮음 |

### **API 연동**

| API 엔드포인트 | 메서드 | 용도 | 호출 시점 |
| ----- | ----- | ----- | ----- |
| `/api/users` | PUT | 닉네임 수정 | 저장 버튼 클릭 시 |
| `/api/users` | DELETE | 회원 탈퇴 | 삭제 버튼 클릭 시 |

### **성능 최적화**

* \[✔\] 최소 useEffect  
* memo 미사용

### **외부 의존성**

* **라이브러리**: `react-redux`, `axios`, `react-router-dom`  
* **스타일**: `ProfilePage.module.css`

# **7-10. pages (ReviewPage)**

## **7.10.1 React 컴포넌트 개요**

### **컴포넌트명**

**ReviewPage**

### **목적 및 역할**

* **주요 목적**:  
   사용자가 선택한 영화에 대한 리뷰를 작성하고, 다른 리뷰를 확인할 수 있는 인터페이스를 제공

### **기능 요구사항**

* 영화 ID 기반 리뷰 불러오기  
* 리뷰 작성, 수정, 삭제  
* 별점(스코어) 부여  
* 유효성 체크 및 모달 알림

## **7.10.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입**: \[✔\] 페이지 컴포넌트  
* **복잡도**: \[✔\] 복합  
* **상태 관리**: \[✔\] 로컬 상태 \[✔\] Redux 연결

### **파일 구조**

pages/

└── ReviewPage.jsx

components/

  └── common/

    ├── CommonHeader.jsx

    ├── CommonMovieInfo.jsx

modal/

  └── WarningModal.jsx

## **7.10.3 React 컴포넌트 Props 설계**

* ❌ Props 없음

## **7.10.4 상태 관리 설계**

### **로컬 상태**

| 상태명 | 타입 | 초기값 | 용도 |
| ----- | ----- | ----- | ----- |
| `showReviewBox` | boolean | false | 리뷰 작성 창 여부 |
| `reviewText` | string | "" | 입력 중인 리뷰 내용 |
| `editingReviewId` | number | null | 수정 중인 리뷰 ID |
| `showWarningModal` | boolean | false | 유효성 알림 모달 |

### **Redux 상태**

| 액션/상태 | 설명 |
| ----- | ----- |
| `fetchReviews` | 리뷰 전체 조회 |
| `addReview` | 리뷰 추가 |
| `updateReview` | 리뷰 수정 |
| `deleteReview` | 리뷰 삭제 |

## **7.10.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| CommonMovieInfo | components/common | 영화 정보 | 높음 |
| WarningModal | components/modal | 유효성 경고 | 보통 |

### **API 연동**

| API 엔드포인트 | 메서드 | 용도 | 호출 시점 |
| ----- | ----- | ----- | ----- |
| `/api/reviews` | GET | 리뷰 목록 조회 | 페이지 진입 시 |
| `/api/reviews` | POST | 리뷰 작성 | 작성 시 |
| `/api/reviews/{id}` | PUT | 리뷰 수정 | 수정 시 |
| `/api/reviews/{id}` | DELETE | 리뷰 삭제 | 삭제 시 |

### **성능 최적화**

* \[✔\] useMemo로 리뷰 필터링  
* \[✔\] useEffect deps 최적화

### **외부 의존성**

* `axios`, `react-redux`, `react-router-dom`, `framer-motion`

# **7-11. pages (SignupCompletePage)**

## **7.11.1 React 컴포넌트 개요**

### **컴포넌트명**

**SignupCompletePage**

### **목적 및 역할**

* **주요 목적**:  
   회원가입 완료 메시지를 보여주고, 유저 정보를 Redux에 저장한 후 홈으로 이동

### **기능 요구사항**

* 닉네임, 장르 받아오기  
* Redux 업데이트  
* 홈 리디렉션


## **7.11.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입**: \[✔\] 페이지 컴포넌트  
* **복잡도**: \[ \] 단순  
* **상태 관리**: \[✔\] Redux

### **파일 구조**

pages/

└── SignupCompletePage.jsx

    SignupCompletePage.css

## **7.11.3 React 컴포넌트 Props 설계**

* ❌ Props 없음

## **7.11.4 상태 관리 설계**

| 상태 | 타입 | 설명 |
| ----- | ----- | ----- |
| `location.state` | object | 닉네임, 장르 포함 |
| `updateUserInfo` | action | Redux에 유저 정보 저장 |

## **7.11.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| SignupCompletePage | pages | 가입 완료 | 낮음 |

### **API 연동**

* 없음

### **성능 최적화**

* 특별한 최적화 없음

### **외부 의존성**

* `react-router-dom`, `react-redux`

# **7-12. pages (SignupGenrePage)**

## **7.12.1 React 컴포넌트 개요**

### **컴포넌트명**

**SignupGenrePage**

### **목적 및 역할**

* **주요 목적**:  
   유저가 선호하는 장르를 선택할 수 있게 하는 회원가입 단계

### **기능 요구사항**

* 장르 선택 토글  
* 이전 페이지에서 닉네임 유지  
* 다음 단계로 이동

## **7.12.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입**: \[✔\] 페이지 컴포넌트  
* **복잡도**: \[ \] 단순  
* **상태 관리**: \[✔\] 로컬 상태

### **파일 구조**

pages/

└── SignupGenrePage.jsx

    SignupGenrePage.css

## **7.12.3 React 컴포넌트 Props 설계**

* ❌ Props 없음

## **7.12.4 상태 관리 설계**

| 상태명 | 타입 | 초기값 | 용도 |
| ----- | ----- | ----- | ----- |
| `selectedGenres` | string\[\] | \[\] | 선택된 장르 배열 |

## **7.12.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| SignupGenrePage | pages | 장르 선택 단계 | 낮음 |

### **API 연동**

* 없음

### **성능 최적화**

* 필요 없음

### **외부 의존성**

* `react-router-dom`

# **7-13. pages (SignupIdPage)**

## **7.13.1 React 컴포넌트 개요**

### **컴포넌트명**

**SignupIdPage**

### **목적 및 역할**

* **주요 목적**:  
   유저가 닉네임을 입력하고 다음 단계로 넘어가도록 하는 회원가입의 첫 단계

### **기능 요구사항**

* 닉네임 입력  
* 입력값 확인  
* 다음 단계로 이동

## **7.13.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입**: \[✔\] 페이지 컴포넌트  
* **복잡도**: \[ \] 단순  
* **상태 관리**: \[✔\] 로컬 상태

### **파일 구조**

pages/

└── SignupIdPage.jsx

    SignupIdPage.css

## **7.13.3 React 컴포넌트 Props 설계**

* ❌ Props 없음

## **7.13.4 상태 관리 설계**

| 상태명 | 타입 | 초기값 | 용도 |
| ----- | ----- | ----- | ----- |
| `nickname` | string | "" | 입력 닉네임 |

## **7.13.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| SignupIdPage | pages | 닉네임 입력 | 낮음 |

### **API 연동**

* 없음

### **성능 최적화**

* 없음

### **외부 의존성**

* `react-router-dom`

# **7-14. pages (SocialLoginPage)**

## **7.14.1 React 컴포넌트 개요**

### **컴포넌트명**

**SocialLoginPage**

### **목적 및 역할**

* **주요 목적**:  
   사용자가 소셜 로그인을 선택할 수 있도록 카카오 로그인 진입 URL로 리디렉트 시키는 로그인 진입 페이지

### **기능 요구사항**

* 카카오 로그인 버튼 클릭 시 인증 URL로 이동  
* 회원가입 페이지로 이동 버튼 제공

## **7.14.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입**: \[✔\] 페이지 컴포넌트  
* **복잡도**: \[ \] 단순  
* **상태 관리**: \[ \] 없음

### **파일 구조**

pages/

└── SocialLoginPage.jsx

    SocialLoginPage.css

**7.14.3 React 컴포넌트 Props 설계**

* ❌ Props 없음

## **7.14.4 상태 관리 설계**

* 상태 없음

## **7.14.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| SocialLoginPage | pages | 소셜 로그인 진입 | 낮음 |

### **API 연동**

* ❌ 없음  
   (카카오 인증 URL로 이동만 수행)

### **성능 최적화**

* 필요 없음

### **외부 의존성**

* `react-router-dom`

# **7-15. pages (WritePostPage)**

## **7.15.1 React 컴포넌트 개요**

### **컴포넌트명**

**WritePostPage**

### **목적 및 역할**

* **주요 목적**:  
   특정 영화에 대한 자유로운 게시글을 작성하는 페이지  
*  글 제목과 내용 입력 후 게시글을 저장하고 PostPage로 이동

### **기능 요구사항**

* 제목, 내용 입력 필드  
* 영화 정보 출력(CommonMovieInfo 사용)  
* 저장 시 API 호출 및 리디렉션

## **7.15.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입**: \[✔\] 페이지 컴포넌트  
* **복잡도**: \[✔\] 보통  
* **상태 관리**: \[✔\] 로컬 상태 \[✔\] Redux

### **파일 구조**

pages/

└── WritePostPage.jsx

    WritePostPage.module.css

components/

└── common/

    ├── CommonMovieInfo.jsx

    └── CommonHeader.jsx

## **7.15.3 React 컴포넌트 Props 설계**

* ❌ Props 없음

## **7.15.4 상태 관리 설계**

### **로컬 상태**

| 상태명 | 타입 | 초기값 | 용도 |
| ----- | ----- | ----- | ----- |
| `title` | string | "" | 게시글 제목 입력 |
| `content` | string | "" | 게시글 본문 입력 |

### **Redux 상태**

| 액션/상태 | 설명 |
| ----- | ----- |
| `fetchUserInfo` | 사용자 정보 가져오기 |

## **7.15.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| CommonMovieInfo | components/common | 영화 정보 표시 | 높음 |
| CommonHeader | components/common | 상단 제목 헤더 | 높음 |

### **API 연동**

| API 엔드포인트 | 메서드 | 용도 | 호출 시점 |
| ----- | ----- | ----- | ----- |
| `/api/posts` | POST | 게시글 작성 | 저장 버튼 클릭 시 |

### **성능 최적화**

* \[✔\] useEffect 최소화  
* memo 등은 없음

### **외부 의존성**

* `axios`, `react-redux`, `react-router-dom`, `framer-motion`

# **8\. App.jsx**

## **8.1 React 컴포넌트 개요**

### **컴포넌트명**

**App**

### **목적 및 역할**

* **주요 목적**:  
   SeenIt의 전역 라우팅 구조를 관리하고, 앱 시작 시 스플래시 스크린을 보여준 후 라우터 구성을 담당하는 최상위 컴포넌트  
   페이지 전환 시 애니메이션 효과도 함께 적용되어 UX를 향상시켜주는 핵심

### **기능 요구사항**

* 첫 진입 시 SplashScreen 2.5초간 표시  
* 로그인/회원가입/홈/상세/리뷰/포스트/마이페이지 등 라우팅 처리  
* 페이지 전환 애니메이션 적용  
* ScrollToTop 전역 적용  
* 잘못된 경로는 404 페이지로 안내

## **8.2 컴포넌트 구조 설계**

### **컴포넌트 분류**

* **타입**: \[✔\] 레이아웃 컴포넌트 (최상위 라우팅 관리)  
* **복잡도**: \[✔\] 복합  
* **상태 관리**: \[✔\] 로컬 상태 (SplashScreen 표시 여부)

### **파일 구조**

src/

├── App.jsx

├── App.css

## **8.3 React 컴포넌트 Props 설계**

* ❌ 외부로부터 전달받는 Props 없음

## **8.4 상태 관리 설계**

### **로컬 상태**

| 상태명 | 타입 | 초기값 | 용도 |
| ----- | ----- | ----- | ----- |
| showSplash | boolean | true/false | 세션 스토리지를 기준으로 스플래시 표시 여부 판단 |

### **Redux 상태**

* ❌ 사용되지 않음

## **8.5 구현 세부사항**

### **주요 컴포넌트 목록**

| 컴포넌트명 | 위치 | 역할 | 재사용성 |
| ----- | ----- | ----- | ----- |
| SplashScreen | components/splash | 앱 시작 시 보여주는 화면 | 낮음 |
| ScrollToTop | components/common | 페이지 진입 시 스크롤 최상단 | 높음 |
| `motion.div` | framer-motion | 페이지 전환 애니메이션 효과 | 보통 |
| Routes, Route | react-router-dom | 라우터 구성 요소 | 필수 |

### **API 연동**

* ❌ 직접적인 API 연동은 없음  
   (각 페이지 내부에서 처리됨)

### **성능 최적화**

* \[✔\] `AnimatePresence` \+ `motion.div`로 페이지 전환 최적화  
* \[✔\] `sessionStorage` 사용으로 splash 중복 방지

### **외부 의존성**

| 라이브러리 | 설명 |
| ----- | ----- |
| `react-router-dom` | SPA 라우팅 관리 |
| `framer-motion` | 페이지 전환 애니메이션 적용 |
| `swiper/css` | 영화 리스트 등에 사용된 슬라이드 |

