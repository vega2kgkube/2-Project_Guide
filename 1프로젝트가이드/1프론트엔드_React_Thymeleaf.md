# 미니 프로젝트 가이드 (상세버전)

## 목차
1. [BackEnd 설계 가이드]
2. [FrontEnd 설계 가이드]
3. [프로젝트 작성 순서 및 9일 타임테이블]
4. [프로젝트 평가 및 배점표]

---

## 2. FrontEnd 설계 가이드

### 2.1 화면 설계서 가이드

#### 2.1.1 화면 설계 도구 비교

**Figma**

```
장점:
 완전 무료 (개인/팀 사용)
 웹 브라우저에서 실행 (설치 불필요)
 실시간 협업 (구글 독스처럼 동시 편집)
 개발자 친화적 (CSS 코드 자동 생성)
 풍부한 무료 템플릿 및 UI 키트
 컴포넌트 시스템 (디자인 시스템 구축 가능)
 프로토타이핑 기능
 한국어 지원

단점:
 인터넷 연결 필요
 복잡한 기능은 학습 곡선 존재

사용법:
1. figma.com 접속 후 무료 계정 생성
2. "Design file" 생성
3. 좌측 도구 모음으로 도형, 텍스트 추가
4. 컴포넌트 생성으로 재사용 가능한 UI 요소 제작
5. 프로토타이핑으로 화면 간 연결
```

**Chakra UI**
```
**장점:**
- **간단한 API**: 직관적이고 배우기 쉬운 prop 기반 스타일링
- **빠른 개발**: 최소한의 설정으로 바로 사용 가능
- **좋은 기본값**: 접근성과 반응형을 기본으로 지원
- **작은 번들 크기**: 필요한 컴포넌트만 import 가능
- **훌륭한 문서**: 명확하고 이해하기 쉬운 예제

**설치 방법:**
```bash
npm install @chakra-ui/react @emotion/react @emotion/styled framer-motion
```

**대안 도구들**
```
Adobe XD:
- 장점: Adobe 생태계 연동, 강력한 프로토타이핑
- 단점: 유료, 학습비용 높음

Sketch:
- 장점: Mac에서 뛰어난 성능
- 단점: Mac 전용, 유료

Balsamiq:
- 장점: 빠른 와이어프레임 제작
- 단점: 기능 제한적, 디자인보다는 구조 설계용

draw.io (현재 diagrams.net):
- 장점: 완전 무료, 간단한 와이어프레임
- 단점: 디자인 기능 부족
```

#### 2.1.2 화면 설계 단계별 가이드

**STEP 1: 사이트맵 작성**
```
도서관 시스템 사이트맵 예시:

1. 사용자 영역 (React)
   ├── 메인 페이지 (/)
   ├── 로그인/회원가입 (/auth)
   │   ├── 로그인 (/auth/login)
   │   └── 회원가입 (/auth/register)
   ├── 도서 검색/목록 (/books)
   │   ├── 도서 목록 (/books)
   │   ├── 도서 상세 (/books/{id})
   │   └── 도서 검색 결과 (/books/search)
   ├── 내 정보 (/my)
   │   ├── 내 정보 (/my/profile)
   │   ├── 대출 현황 (/my/loans)
   │   ├── 대출 이력 (/my/history)
   │   └── 예약 현황 (/my/reservations)
   └── 공지사항 (/notices)

2. 관리자 영역 (Thymeleaf)
   ├── 관리자 로그인 (/admin/login)
   ├── 대시보드 (/admin/dashboard)
   ├── 회원 관리 (/admin/members)
   │   ├── 회원 목록 (/admin/members)
   │   ├── 회원 상세 (/admin/members/{id})
   │   └── 회원 등록 (/admin/members/new)
   ├── 도서 관리 (/admin/books)
   │   ├── 도서 목록 (/admin/books)
   │   ├── 도서 등록 (/admin/books/new)
   │   └── 도서 수정 (/admin/books/{id}/edit)
   ├── 대출 관리 (/admin/loans)
   │   ├── 대출 승인 대기 (/admin/loans/pending)
   │   ├── 대출 현황 (/admin/loans/current)
   │   └── 연체 관리 (/admin/loans/overdue)
   └── 통계 (/admin/statistics)
```

**STEP 2: 와이어프레임 작성**

각 페이지의 기본 레이아웃을 간단한 도형으로 표현합니다.

```
도서 목록 페이지 와이어프레임 예시:

┌─────────────────────────────────────────────┐
│ Header (로고, 네비게이션, 사용자 정보)          │
├─────────────────────────────────────────────┤
│ ┌─────────────┐ ┌─────────────────────────┐ │
│ │             │ │                         │ │
│ │   검색 박스  │ │    카테고리 필터          │ │
│ │             │ │                         │ │
│ └─────────────┘ └─────────────────────────┘ │
├─────────────────────────────────────────────┤
│ 도서 목록 (카드 형태로 배치)                   │
│ ┌───────┐ ┌───────┐  ┌───────┐ ┌───────┐    │
│ │ 도서1  │ │ 도서2 │  │ 도서3  │ │ 도서4  │    │
│ │ 이미지 │ │ 이미지 │  │ 이미지 │ │ 이미지 │   │
│ │ 제목   │ │ 제목   │ │ 제목   │ │ 제목   │   │
│ │ 저자   │ │ 저자   │ │ 저자   │ │ 저자   │   │
│ │ 상태   │ │ 상태   │ │ 상태   │ │ 상태   │   │
│ └───────┘ └───────┘  └───────┘ └───────┘   │
├─────────────────────────────────────────────┤
│ 페이지네이션 (< 1 2 3 4 5 >)                  │
├─────────────────────────────────────────────┤
│ Footer (연락처, 운영시간 등)                   │
└─────────────────────────────────────────────┘
```

**STEP 3: UI 플로우 설계**

사용자의 주요 동선을 시각화합니다.

```
도서 대출 플로우 예시:

메인 페이지 → 도서 검색 → 도서 목록 → 도서 상세
    ↓            ↓          ↓         ↓
로그인 확인 ← 로그인 페이지 ← 대출 신청 버튼 클릭
    ↓
대출 신청 확인 모달
    ↓
대출 신청 완료 → 내 대출 현황 페이지
```

#### 2.1.3 Figma를 활용한 실제 화면 설계

**1. 디자인 시스템 구축**
```
Color Palette:
- Primary: #1976d2 (파란색 - 신뢰성)
- Secondary: #388e3c (초록색 - 성공)
- Error: #d32f2f (빨간색 - 에러)
- Warning: #f57c00 (주황색 - 경고)
- Background: #f5f5f5 (연한 회색)
- Surface: #ffffff (흰색)
- Text Primary: #212121 (진한 회색)
- Text Secondary: #757575 (회색)

Typography:
- Heading 1: 32px, Bold
- Heading 2: 24px, Bold
- Heading 3: 20px, Semi-Bold
- Body 1: 16px, Regular
- Body 2: 14px, Regular
- Caption: 12px, Regular

Spacing:
- Micro: 4px
- Small: 8px
- Medium: 16px
- Large: 24px
- XL: 32px
- XXL: 48px

Border Radius:
- Small: 4px
- Medium: 8px
- Large: 12px
- Round: 50%
```

**2. 컴포넌트 라이브러리 설계**
```
기본 컴포넌트:
- Button (Primary, Secondary, Text)
- Input (Text, Email, Password, Search)
- Card (기본, 이미지 포함)
- Modal (확인, 알림, 폼)
- Navigation (Header, Sidebar)
- Badge (상태 표시)
- Pagination
- Table
- Loading Spinner

복합 컴포넌트:
- BookCard (도서 카드)
- MemberInfo (회원 정보)
- LoanStatus (대출 상태)
- SearchBar (검색 바)
- FilterPanel (필터 패널)
```

### 2.2 React 컴포넌트 설계 가이드

#### 2.2.1 React UI 라이브러리 상세 비교

**Material-UI (MUI)**
```
장점:
 가장 인기 있는 라이브러리 (GitHub 90k+ 스타)
 Google Material Design 기반의 일관된 디자인
 100개 이상의 풍부한 컴포넌트
 TypeScript 완벽 지원
 커뮤니티가 활발하여 문제 해결 용이
 테마 시스템으로 전체 디자인 일관성 유지
 접근성(Accessibility) 준수
 상세한 문서 및 예제

단점:
 번들 크기가 큰 편
 Material Design 스타일에 제약

설치 및 사용:
npm install @mui/material @emotion/react @emotion/styled
npm install @mui/icons-material  # 아이콘 사용시

기본 사용 예시:
import { Button, TextField, Card } from '@mui/material';
```

**대안 UI 라이브러리들**
```
Chakra UI:
- 장점: 간단하고 모듈형, 빠른 개발
- 단점: 컴포넌트 수 제한적
- 적합한 프로젝트: 빠른 프로토타이핑

Ant Design:
- 장점: 엔터프라이즈급 컴포넌트, 풍부한 기능
- 단점: 중국 스타일, 커스터마이징 어려움
- 적합한 프로젝트: 관리자 대시보드, 백오피스

React Bootstrap:
- 장점: Bootstrap 친숙함, 가벼움
- 단점: 구식 디자인, 제한적 기능
- 적합한 프로젝트: 기존 Bootstrap 경험자

Mantine:
- 장점: 현대적 디자인, 풍부한 기능
- 단점: 상대적으로 새로운 라이브러리
- 적합한 프로젝트: 모던 웹 애플리케이션
```

#### 2.2.2 컴포넌트 아키텍처 설계

**1. 컴포넌트 분류 체계**
```
src/
├── components/
│   ├── common/              # 재사용 가능한 공통 컴포넌트
│   │   ├── Button/
│   │   │   ├── index.js
│   │   │   ├── Button.jsx
│   │   │   └── Button.styles.js
│   │   ├── Input/
│   │   ├── Modal/
│   │   ├── Loading/
│   │   └── ErrorBoundary/
│   ├── layout/              # 레이아웃 관련 컴포넌트
│   │   ├── Header/
│   │   ├── Footer/
│   │   ├── Sidebar/
│   │   └── Layout/
│   ├── features/            # 기능별 컴포넌트
│   │   ├── auth/
│   │   │   ├── LoginForm/
│   │   │   ├── RegisterForm/
│   │   │   └── AuthGuard/
│   │   ├── books/
│   │   │   ├── BookCard/
│   │   │   ├── BookList/
│   │   │   ├── BookDetail/
│   │   │   └── BookSearch/
│   │   ├── loans/
│   │   │   ├── LoanCard/
│   │   │   ├── LoanList/
│   │   │   └── LoanStatus/
│   │   └── members/
│   └── pages/               # 페이지 컴포넌트
│       ├── HomePage/
│       ├── BooksPage/
│       ├── LoginPage/
│       └── MyPage/
```

**2. 컴포넌트 설계 원칙**

```javascript
//  책임 원칙 - 하나의 컴포넌트는 하나의 역할만
// 나쁜 예: 여러 기능이 섞인 컴포넌트
const BadBookComponent = () => {
  // 도서 데이터 fetch, 검색, 필터링, 렌더링 모두 처리
  return (
    <div>
      {/* 검색 폼 */}
      {/* 필터 옵션 */}
      {/* 도서 목록 */}
      {/* 페이지네이션 */}
    </div>
  );
};

// 좋은 예: 각 기능을 별도 컴포넌트로 분리
con BooksPage = () => {
  return (
    <div>
      <BookSearch />
      <BookFilter />
      <BookList />
      <Pagination />
    </div>
  );
};
```

**3. Props 설계 가이드**

```javascript
// PropTypes 정의 (또는 TypeScript 사용)
import PropTypes from 'prop-types';

const BookCard = ({ 
  book, 
  onLoanRequest, 
  onReservation, 
  showActions = true,
  size = 'medium',
  className = '' 
}) => {
  return (
    <Card className={`book-card book-card--${size} ${className}`}>
      <CardMedia
        component="img"
        height="200"
        image={book.imageUrl || '/images/no-image.png'}
        alt={book.title}
      />
      <CardContent>
        <Typography variant="h6" component="h3">
          {book.title}
        </Typography>
        <Typography variant="body2" color="text.secondary">
          {book.author}
        </Typography>
        <Chip 
          label={book.isAvailable ? '대출가능' : '대출중'} 
          color={book.isAvailable ? 'success' : 'default'}
          size="small"
        />
      </CardContent>
      {showActions && (
        <CardActions>
          <Button 
            size="small" 
            onClick={() => onLoanRequest(book.id)}
            disabled={!book.isAvailable}
          >
            대출신청
          </Button>
          <Button 
            size="small" 
            onClick={() => onReservation(book.id)}
            disabled={book.isAvailable}
          >
            예약
          </Button>
        </CardActions>
      )}
    </Card>
  );
};

BookCard.propTypes = {
  book: PropTypes.shape({
    id: PropTypes.number.isRequired,
    title: PropTypes.string.isRequired,
    author: PropTypes.string.isRequired,
    imageUrl: PropTypes.string,
    isAvailable: PropTypes.bool.isRequired
  }).isRequired,
  onLoanRequest: PropTypes.func,
  onReservation: PropTypes.func,
  showActions: PropTypes.bool,
  size: PropTypes.oneOf(['small', 'medium', 'large']),
  className: PropTypes.string
};
```

**4. 상태 관리 전략**

```javascript
// 로컬 상태 vs 전역 상태 구분
// 로컬 상태 사용 예시 (컴포넌트 내부에서만 사용)
consBookSearch = () => {
  const [searchTerm, setSearchTerm] = useState('');
  const [isLoading, setIsLoading] = useState(false);
  
  const handleSearch = async () => {
    setIsLoading(true);
    try {
      const results = await searchBooks(searchTerm);
      onSearchResults(results);
    } finally {
      setIsLoading(false);
    }
  };

  return (
    <Box sx={{ display: 'flex', gap: 1 }}>
      <TextField
        value={searchTerm}
        onChange={(e) => setSearchTerm(e.target.value)}
        placeholder="도서명, 저자명을 입력하세요"
        fullWidth
      />
      <Button 
        onClick={handleSearch}
        disabled={isLoading}
        variant="contained"
      >
        {isLoading ? <CircularProgress size={20} /> : '검색'}
      </Button>
    </Box>
  );
};
//  전역 상태 사용 예시 (여러 컴포넌트에서 공유)
// Redux toolkit 사용
import { useSelector, useDispatch } from 'react-redux';
import { fetchUserProfile, selectUser } from '../store/slices/authSlice';

const UserProfile = () => {
  const dispatch = useDispatch();
  const { user, isLoading, error } = useSelector(selectUser);

  useEffect(() => {
    dispatch(fetchUserProfile());
  }, [dispatch]);

  if (isLoading) return <Loading />;
  if (error) return <ErrorMessage message={error} />;

  return (
    <Card>
      <CardContent>
        <Typography variant="h5">{user.name}</Typography>
        <Typography color="text.secondary">
          회원번호: {user.memberNumber}
        </Typography>
        <Typography color="text.secondary">
          대출 가능: {user.maxLoanCount - user.currentLoanCount}권
        </Typography>
      </CardContent>
    </Card>
  );
};
```

#### 2.2.3 MUI 컴포넌트 활용 예시

**1. 기본 설정 및 테마**

```javascript
// theme.js - 전체 애플리케이션 테마 설정
import { createTheme } from '@mui/material/styles';

const theme = createTheme({
  palette: {
    primary: {
      main: '#1976d2',
      light: '#42a5f5',
      dark: '#1565c0',
    },
    secondary: {
      main: '#388e3c',
      light: '#66bb6a',
      dark: '#2e7d32',
    },
    error: {
      main: '#d32f2f',
    },
    warning: {
      main: '#f57c00',
    },
    background: {
      default: '#f5f5f5',
      paper: '#ffffff',
    },
  },
  typography: {
    fontFamily: [
      '-apple-system',
      'BlinkMacSystemFont',
      '"Noto Sans KR"',
      '"Segoe UI"',
      'Roboto',
      'sans-serif'
    ].join(','),
    h1: {
      fontSize: '2rem',
      fontWeight: 700,
    },
    h2: {
      fontSize: '1.5rem',
      fontWeight: 600,
    },
    body1: {
      fontSize: '1rem',
      lineHeight: 1.6,
    },
  },
  components: {
    MuiButton: {
      styleOverrides: {
        root: {
          borderRadius: 8,
          textTransform: 'none',
          fontWeight: 600,
        },
      },
    },
    MuiCard: {
      styleOverrides: {
        root: {
          borderRadius: 12,
          boxShadow: '0 2px 8px rgba(0,0,0,0.1)',
        },
      },
    },
  },
});

export default theme;

// App.js
import { ThemeProvider } from '@mui/material/styles';
import CssBaseline from '@mui/material/CssBaseline';
import theme from './theme';

function App() {
  return (
    <ThemeProvider theme={theme}>
      <CssBaseline />
      {/* 애플리케이션 컴포넌트들 */}
    </ThemeProvider>
  );
}
```

**2. 도서 목록 컴포넌트 구현**

```javascript
// BookList.jsx
import React, { useState, useEffect } from 'react';
import {
  Grid,
  Card,
  CardMedia,
  CardContent,
  CardActions,
  Typography,
  Button,
  Chip,
  Box,
  Skeleton,
  Alert
} from '@mui/material';
import { useQuery, useMutation } from '@tanstack/react-query';
import { bookService } from '../services/bookService';
import { useAuth } from '../hooks/useAuth';

const BookList = ({ searchParams, onBookSelect }) => {
  const { user } = useAuth();
  
  const {
    data: books,
    isLoading,
    error,
    refetch
  } = useQuery({
    queryKey: ['books', searchParams],
    queryFn: () => bookService.getBooks(searchParams),
    keepPreviousData: true
  });

  const loanMutation = useMutation({
    mutationFn: bookService.requestLoan,
    onSuccess: () => {
      refetch();
      // 성공 메시지 표시
    },
    onError: (error) => {
      // 에러 메시지 표시
    }
  });

  const handleLoanRequest = (bookId) => {
    if (!user) {
      // 로그인 유도
      return;
    }
    loanMutation.mutate({ bookId, memberId: user.id });
  };

  if (isLoading) {
    return (
      <Grid container spacing={3}>
        {[...Array(8)].map((_, index) => (
          <Grid item xs={12} sm={6} md={4} lg={3} key={index}>
            <Card>
              <Skeleton variant="rectangular" height={200} />
              <CardContent>
                <Skeleton variant="text" height={28} />
                <Skeleton variant="text" height={20} />
                <Skeleton variant="rectangular" width={80} height={24} />
              </CardContent>
            </Card>
          </Grid>
        ))}
      </Grid>
    );
  }

  if (error) {
    return (
      <Alert severity="error" sx={{ mb: 2 }}>
        도서 목록을 불러오는 중 오류가 발생했습니다.
        <Button onClick={() => refetch()}>다시 시도</Button>
      </Alert>
    );
  }

  if (!books?.content?.length) {
    return (
      <Box textAlign="center" py={4}>
        <Typography variant="h6" color="text.secondary">
          검색 결과가 없습니다.
        </Typography>
      </Box>
    );
  }

  return (
    <Grid container spacing={3}>
      {books.content.map((book) => (
        <Grid item xs={12} sm={6} md={4} lg={3} key={book.id}>
          <Card 
            sx={{ 
              height: '100%', 
              display: 'flex', 
              flexDirection: 'column',
              cursor: 'pointer',
              '&:hover': {
                boxShadow: (theme) => theme.shadows[8],
                transform: 'translateY(-2px)',
              },
              transition: 'all 0.2s ease-in-out'
            }}
            onClick={() => onBookSelect(book.id)}
          >
            <CardMedia
              component="img"
              height="200"
              image={book.imageUrl || '/images/no-book-image.png'}
              alt={book.title}
              sx={{ objectFit: 'cover' }}
            />
            <CardContent sx={{ flexGrow: 1 }}>
              <Typography 
                variant="h6" 
                component="h3" 
                noWrap
                sx={{ mb: 1 }}
              >
                {book.title}
              </Typography>
              <Typography 
                variant="body2" 
                color="text.secondary"
                noWrap
                sx={{ mb: 1 }}
              >
                {book.author}
              </Typography>
              <Typography 
                variant="caption" 
                color="text.secondary"
                sx={{ mb: 2, display: 'block' }}
              >
                {book.publisher} • {book.publicationDate}
              </Typography>
              <Box sx={{ mb: 1 }}>
                <Chip 
                  label={book.isAvailable ? '대출가능' : '대출중'} 
                  color={book.isAvailable ? 'success' : 'default'}
                  size="small"
                />
                {book.category && (
                  <Chip 
                    label={book.category.name}
                    variant="outlined"
                    size="small"
                    sx={{ ml: 1 }}
                  />
                )}
              </Box>
            </CardContent>
            <CardActions sx={{ pt: 0 }}>
              <Button 
                size="small" 
                variant="contained"
                onClick={(e) => {
                  e.stopPropagation();
                  handleLoanRequest(book.id);
                }}
                disabled={!book.isAvailable || loanMutation.isLoading}
                fullWidth
              >
                {book.isAvailable ? '대출신청' : '예약하기'}
              </Button>
            </CardActions>
          </Card>
        </Grid>
      ))}
    </Grid>
  );
};

export default BookList;
```

**3. 검색 및 필터 컴포넌트**

```javascript
// BookSearchFilter.jsx
import React, { useState } from 'react';
import {
  Box,
  TextField,
  Button,
  Accordion,
  AccordionSummary,
  AccordionDetails,
  FormGroup,
  FormControlLabel,
  Checkbox,
  Select,
  MenuItem,
  FormControl,
  InputLabel,
  Chip,
  IconButton
} from '@mui/material';
import {
  Search as SearchIcon,
  ExpandMore as ExpandMoreIcon,
  Clear as ClearIcon
} from '@mui/icons-material';

const BookSearchFilter = ({ onSearch, onFilterChange, categories }) => {
  const [searchTerm, setSearchTerm] = useState('');
  const [filters, setFilters] = useState({
    categories: [],
    availability: 'all',
    sortBy: 'title',
    sortOrder: 'asc'
  });

  const handleSearch = () => {
    onSearch({
      search: searchTerm,
      ...filters
    });
  };

  const handleFilterChange = (filterType, value) => {
    const newFilters = { ...filters, [filterType]: value };
    setFilters(newFilters);
    onFilterChange(newFilters);
  };

  const handleCategoryToggle = (categoryId) => {
    const newCategories = filters.categories.includes(categoryId)
      ? filters.categories.filter(id => id !== categoryId)
      : [...filters.categories, categoryId];
    
    handleFilterChange('categories', newCategories);
  };

  const clearFilters = () => {
    const clearedFilters = {
      categories: [],
      availability: 'all',
      sortBy: 'title',
      sortOrder: 'asc'
    };
    setFilters(clearedFilters);
    onFilterChange(clearedFilters);
  };

  return (
    <Box sx={{ mb: 3 }}>
      {/* 검색 바 */}
      <Box sx={{ display: 'flex', gap: 1, mb: 2 }}>
        <TextField
          value={searchTerm}
          onChange={(e) => setSearchTerm(e.target.value)}
          placeholder="도서명, 저자명, ISBN을 입력하세요"
          fullWidth
          InputProps={{
            endAdornment: (
              <IconButton onClick={handleSearch}>
                <SearchIcon />
              </IconButton>
            )
          }}
          onKeyPress={(e) => e.key === 'Enter' && handleSearch()}
        />
        <Button 
          onClick={handleSearch}
          variant="contained"
          sx={{ minWidth: 100 }}
        >
          검색
        </Button>
      </Box>

      {/* 필터 아코디언 */}
      <Accordion>
        <AccordionSummary expandIcon={<ExpandMoreIcon />}>
          <Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
            상세 필터
            {(filters.categories.length > 0 || filters.availability !== 'all') && (
              <Chip 
                label={`${filters.categories.length + (filters.availability !== 'all' ? 1 : 0)}개 필터 적용`}
                size="small"
                color="primary"
              />
            )}
          </Box>
        </AccordionSummary>
        <AccordionDetails>
          <Box sx={{ display: 'grid', gridTemplateColumns: { xs: '1fr', md: '1fr 1fr 1fr' }, gap: 3 }}>
            {/* 카테고리 필터 */}
            <Box>
              <Typography variant="subtitle2" sx={{ mb: 1 }}>
                카테고리
              </Typography>
              <FormGroup>
                {categories.map((category) => (
                  <FormControlLabel
                    key={category.id}
                    control={
                      <Checkbox
                        checked={filters.categories.includes(category.id)}
                        onChange={() => handleCategoryToggle(category.id)}
                      />
                    }
                    label={category.name}
                  />
                ))}
              </FormGroup>
            </Box>

            {/* 대출 가능 여부 필터 */}
            <Box>
              <FormControl fullWidth>
                <InputLabel>대출 상태</InputLabel>
                <Select
                  value={filters.availability}
                  onChange={(e) => handleFilterChange('availability', e.target.value)}
                  label="대출 상태"
                >
                  <MenuItem value="all">전체</MenuItem>
                  <MenuItem value="available">대출가능</MenuItem>
                  <MenuItem value="unavailable">대출중</MenuItem>
                </Select>
              </FormControl>
            </Box>

            {/* 정렬 옵션 */}
            <Box>
              <FormControl fullWidth sx={{ mb: 1 }}>
                <InputLabel>정렬 기준</InputLabel>
                <Select
                  value={filters.sortBy}
                  onChange={(e) => handleFilterChange('sortBy', e.target.value)}
                  label="정렬 기준"
                >
                  <MenuItem value="title">제목</MenuItem>
                  <MenuItem value="author">저자</MenuItem>
                  <MenuItem value="publicationDate">출간일</MenuItem>
                  <MenuItem value="popularity">인기도</MenuItem>
                </Select>
              </FormControl>
              
              <FormControl fullWidth>
                <InputLabel>정렬 순서</InputLabel>
                <Select
                  value={filters.sortOrder}
                  onChange={(e) => handleFilterChange('sortOrder', e.target.value)}
                  label="정렬 순서"
                >
                  <MenuItem value="asc">오름차순</MenuItem>
                  <MenuItem value="desc">내림차순</MenuItem>
                </Select>
              </FormControl>
            </Box>
          </Box>

          {/* 필터 초기화 버튼 */}
          <Box sx={{ mt: 2, textAlign: 'right' }}>
            <Button 
              onClick={clearFilters}
              variant="outlined"
              startIcon={<ClearIcon />}
            >
              필터 초기화
            </Button>
          </Box>
        </AccordionDetails>
      </Accordion>

      {/* 적용된 필터 태그 */}
      {(filters.categories.length > 0 || filters.availability !== 'all') && (
        <Box sx={{ mt: 2, display: 'flex', gap: 1, flexWrap: 'wrap' }}>
          {filters.categories.map(categoryId => {
            const category = categories.find(c => c.id === categoryId);
            return (
              <Chip
                key={categoryId}
                label={category?.name}
                onDelete={() => handleCategoryToggle(categoryId)}
                color="primary"
                variant="outlined"
              />
            );
          })}
          {filters.availability !== 'all' && (
            <Chip
              label={filters.availability === 'available' ? '대출가능' : '대출중'}
              onDelete={() => handleFilterChange('availability', 'all')}
              color="primary"
              variant="outlined"
            />
          )}
        </Box>
      )}
    </Box>
  );
};

export default BookSearchFilter;
```
### 2.3 Thymeleaf 화면 설계 가이드

#### 2.3.1 Thymeleaf 프로젝트 구조

```
src/main/resources/
├── templates/
│   ├── layout/
│   │   ├── base.html           # 기본 레이아웃
│   │   ├── admin.html          # 관리자 레이아웃
│   │   └── error.html          # 에러 페이지 레이아웃
│   ├── fragments/
│   │   ├── head.html           # HTML head 영역
│   │   ├── header.html         # 헤더 네비게이션
│   │   ├── sidebar.html        # 사이드바 메뉴
│   │   ├── footer.html         # 푸터
│   │   └── scripts.html        # JavaScript 영역
│   ├── admin/
│   │   ├── login.html          # 관리자 로그인
│   │   ├── dashboard.html      # 관리자 대시보드
│   │   ├── members/
│   │   │   ├── list.html       # 회원 목록
│   │   │   ├── detail.html     # 회원 상세
│   │   │   └── form.html       # 회원 등록/수정
│   │   ├── books/
│   │   │   ├── list.html       # 도서 목록
│   │   │   ├── detail.html     # 도서 상세
│   │   │   └── form.html       # 도서 등록/수정
│   │   └── loans/
│   │       ├── list.html       # 대출 목록
│   │       ├── pending.html    # 대출 승인 대기
│   │       └── overdue.html    # 연체 관리
│   └── error/
│       ├── 404.html            # 404 에러
│       ├── 500.html            # 500 에러
│       └── access-denied.html   # 접근 거부
├── static/
│   ├── css/
│   │   ├── admin.css           # 관리자 페이지 스타일
│   │   ├── bootstrap.min.css   # Bootstrap CSS
│   │   └── custom.css          # 커스텀 스타일
│   ├── js/
│   │   ├── admin.js            # 관리자 페이지 스크립트
│   │   ├── bootstrap.min.js    # Bootstrap JS
│   │   └── common.js           # 공통 스크립트
│   └── images/
│       ├── logo.png
│       └── no-image.png
└── messages/
    ├── messages.properties     # 기본 메시지
    └── messages_ko.properties  # 한국어 메시지
```

#### 2.3.2 기본 레이아웃 설계

**1. 기본 레이아웃 (base.html)**

```html
<!DOCTYPE html>
<html lang="ko" xmlns:th="http://www.thymeleaf.org">
<head th:replace="fragments/head :: head">
</head>
<body>
    <div class="wrapper">
        <!-- 헤더 -->
        <header th:replace="fragments/header :: header">
        </header>

        <!-- 메인 컨텐츠 -->
        <main class="main-content">
            <div class="container-fluid">
                <!-- 알림 메시지 -->
                <div th:if="${message}" class="alert alert-info alert-dismissible fade show" role="alert">
                    <span th:text="${message}"></span>
                    <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
                </div>

                <div th:if="${error}" class="alert alert-danger alert-dismissible fade show" role="alert">
                    <span th:text="${error}"></span>
                    <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
                </div>

                <!-- 페이지별 컨텐츠 -->
                <div layout:fragment="content">
                    <!-- 각 페이지의 내용이 여기에 들어감 -->
                </div>
            </div>
        </main>

        <!-- 푸터 -->
        <footer th:replace="fragments/footer :: footer">
        </footer>
    </div>

    <!-- 스크립트 -->
    <div th:replace="fragments/scripts :: scripts">
    </div>
</body>
</html>
```

**2. 관리자 레이아웃 (admin.html)**

```html
<!DOCTYPE html>
<html lang="ko" xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/extras/spring-security">
<head th:replace="fragments/head :: head">
</head>
<body class="admin-layout">
    <div class="admin-wrapper">
        <!-- 관리자 헤더 -->
        <header class="admin-header">
            <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
                <div class="container-fluid">
                    <a class="navbar-brand" href="/admin/dashboard">
                        <img src="/images/logo.png" alt="Library Admin" height="30">
                        도서관 관리시스템
                    </a>
                    
                    <div class="navbar-nav ms-auto">
                        <div class="nav-item dropdown">
                            <a class="nav-link dropdown-toggle" href="#" role="button" 
                               data-bs-toggle="dropdown">
                                <i class="fas fa-user"></i>
                                <span sec:authentication="name">관리자</span>
                            </a>
                            <ul class="dropdown-menu">
                                <li><a class="dropdown-item" href="/admin/profile">내 정보</a></li>
                                <li><hr class="dropdown-divider"></li>
                                <li>
                                    <form th:action="@{/admin/logout}" method="post" class="d-inline">
                                        <button type="submit" class="dropdown-item">로그아웃</button>
                                    </form>
                                </li>
                            </ul>
                        </div>
                    </div>
                </div>
            </nav>
        </header>

        <div class="admin-container">
            <!-- 사이드바 -->
            <aside th:replace="fragments/sidebar :: sidebar" class="admin-sidebar">
            </aside>

            <!-- 메인 컨텐츠 -->
            <main class="admin-content">
                <div class="container-fluid py-4">
                    <!-- 브레드크럼 -->
                    <nav aria-label="breadcrumb" th:if="${breadcrumbs}">
                        <ol class="breadcrumb">
                            <li class="breadcrumb-item">
                                <a href="/admin/dashboard">
                                    <i class="fas fa-home"></i> 대시보드
                                </a>
                            </li>
                            <li th:each="crumb, iterStat : ${breadcrumbs}" 
                                th:class="${iterStat.last} ? 'breadcrumb-item active' : 'breadcrumb-item'">
                                <a th:if="${!iterStat.last}" th:href="${crumb.url}" th:text="${crumb.name}"></a>
                                <span th:if="${iterStat.last}" th:text="${crumb.name}"></span>
                            </li>
                        </ol>
                    </nav>

                    <!-- 페이지 제목 -->
                    <div class="d-flex justify-content-between align-items-center mb-4">
                        <h1 class="h3 mb-0" th:text="${pageTitle}">페이지 제목</h1>
                        <div layout:fragment="page-actions">
                            <!-- 페이지별 액션 버튼들 -->
                        </div>
                    </div>

                    <!-- 알림 메시지 -->
                    <div th:if="${successMessage}" class="alert alert-success alert-dismissible fade show">
                        <i class="fas fa-check-circle"></i>
                        <span th:text="${successMessage}"></span>
                        <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
                    </div>

                    <div th:if="${errorMessage}" class="alert alert-danger alert-dismissible fade show">
                        <i class="fas fa-exclamation-circle"></i>
                        <span th:text="${errorMessage}"></span>
                        <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
                    </div>

                    <!-- 페이지 컨텐츠 -->
                    <div layout:fragment="content">
                        <!-- 각 관리자 페이지의 내용이 여기에 들어감 -->
                    </div>
                </div>
            </main>
        </div>
    </div>

    <!-- 관리자 전용 스크립트 -->
    <div th:replace="fragments/scripts :: admin-scripts">
    </div>
</body>
</html>
```

#### 2.3.3 프래그먼트 설계

**1. 헤드 프래그먼트 (fragments/head.html)**

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head th:fragment="head">
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title th:text="${pageTitle != null ? pageTitle + ' - 도서관 관리시스템' : '도서관 관리시스템'}">도서관 관리시스템</title>
    
    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    
    <!-- Font Awesome -->
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    
    <!-- Custom CSS -->
    <link th:href="@{/css/admin.css}" rel="stylesheet">
    
    <!-- Page specific CSS -->
    <th:block layout:fragment="css">
        <!-- 페이지별 CSS가 여기에 추가됩니다 -->
    </th:block>
</head>
</html>
```

**2. 사이드바 프래그먼트 (fragments/sidebar.html)**

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/extras/spring-security">
<div th:fragment="sidebar" class="admin-sidebar">
    <div class="sidebar-content">
        <ul class="nav nav-pills flex-column">
            <!-- 대시보드 -->
            <li class="nav-item">
                <a class="nav-link" th:href="@{/admin/dashboard}" 
                   th:classappend="${#request.requestURI == '/admin/dashboard'} ? 'active' : ''">
                    <i class="fas fa-tachometer-alt"></i>
                    <span>대시보드</span>
                </a>
            </li>

            <!-- 회원 관리 -->
            <li class="nav-item" sec:authorize="hasAnyRole('SUPER_ADMIN', 'ADMIN')">
                <a class="nav-link" th:href="@{/admin/members}"
                   th:classappend="${#strings.startsWith(#request.requestURI, '/admin/members')} ? 'active' : ''">
                    <i class="fas fa-users"></i>
                    <span>회원 관리</span>
                </a>
            </li>

            <!-- 도서 관리 -->
            <li class="nav-item">
                <a class="nav-link" th:href="@{/admin/books}"
                   th:classappend="${#strings.startsWith(#request.requestURI, '/admin/books')} ? 'active' : ''">
                    <i class="fas fa-book"></i>
                    <span>도서 관리</span>
                </a>
            </li>

            <!-- 대출 관리 -->
            <li class="nav-item">
                <a class="nav-link dropdown-toggle" data-bs-toggle="collapse" 
                   href="#loanSubmenu" role="button">
                    <i class="fas fa-hand-holding"></i>
                    <span>대출 관리</span>
                </a>
                <div class="collapse" id="loanSubmenu">
                    <ul class="nav nav-pills flex-column ms-3">
                        <li class="nav-item">
                            <a class="nav-link" th:href="@{/admin/loans/pending}">
                                <i class="fas fa-clock"></i>
                                승인 대기
                                <span class="badge bg-warning ms-auto" th:text="${pendingLoansCount}">0</span>
                            </a>
                        </li>
                        <li class="nav-item">
                            <a class="nav-link" th:href="@{/admin/loans/current}">
                                <i class="fas fa-book-open"></i>
                                대출 현황
                            </a>
                        </li>
                        <li class="nav-item">
                            <a class="nav-link" th:href="@{/admin/loans/overdue}">
                                <i class="fas fa-exclamation-triangle"></i>
                                연체 관리
                                <span class="badge bg-danger ms-auto" th:text="${overdueLoansCount}">0</span>
                            </a>
                        </li>
                    </ul>
                </div>
            </li>

            <!-- 예약 관리 -->
            <li class="nav-item">
                <a class="nav-link" th:href="@{/admin/reservations}"
                   th:classappend="${#strings.startsWith(#request.requestURI, '/admin/reservations')} ? 'active' : ''">
                    <i class="fas fa-calendar-check"></i>
                    <span>예약 관리</span>
                </a>
            </li>

            <!-- 카테고리 관리 -->
            <li class="nav-item" sec:authorize="hasAnyRole('SUPER_ADMIN', 'ADMIN')">
                <a class="nav-link" th:href="@{/admin/categories}"
                   th:classappend="${#strings.startsWith(#request.requestURI, '/admin/categories')} ? 'active' : ''">
                    <i class="fas fa-tags"></i>
                    <span>카테고리 관리</span>
                </a>
            </li>

            <!-- 통계 -->
            <li class="nav-item">
                <a class="nav-link" th:href="@{/admin/statistics}"
                   th:classappend="${#strings.startsWith(#request.requestURI, '/admin/statistics')} ? 'active' : ''">
                    <i class="fas fa-chart-bar"></i>
                    <span>통계</span>
                </a>
            </li>

            <!-- 관리자 관리 (슈퍼 관리자만) -->
            <li class="nav-item" sec:authorize="hasRole('SUPER_ADMIN')">
                <a class="nav-link" th:href="@{/admin/admins}"
                   th:classappend="${#strings.startsWith(#request.requestURI, '/admin/admins')} ? 'active' : ''">
                    <i class="fas fa-user-shield"></i>
                    <span>관리자 관리</span>
                </a>
            </li>
        </ul>
    </div>
</div>
</html>
```

#### 2.3.4 실제 관리자 페이지 구현 예시

**1. 회원 목록 페이지 (admin/members/list.html)**

```html
<!DOCTYPE html>
<html layout:decorate="~{layout/admin}" xmlns:th="http://www.thymeleaf.org">
<head>
    <title>회원 관리</title>
</head>

<div layout:fragment="content">
    <!-- 페이지 액션 버튼 -->
    <div layout:fragment="page-actions">
        <a th:href="@{/admin/members/new}" class="btn btn-primary">
            <i class="fas fa-plus"></i> 회원 등록
        </a>
        <button type="button" class="btn btn-outline-secondary" data-bs-toggle="modal" data-bs-target="#importModal">
            <i class="fas fa-file-import"></i> 일괄 등록
        </button>
    </div>

    <!-- 검색 및 필터 -->
    <div class="card mb-4">
        <div class="card-body">
            <form th:action="@{/admin/members}" method="get" class="row g-3">
                <div class="col-md-3">
                    <label for="search" class="form-label">검색</label>
                    <input type="text" class="form-control" id="search" name="search" 
                           th:value="${param.search}" placeholder="이름, 이메일, 회원번호">
                </div>
                <div class="col-md-2">
                    <label for="status" class="form-label">상태</label>
                    <select class="form-select" id="status" name="status">
                        <option value="">전체</option>
                        <option value="ACTIVE" th:selected="${param.status == 'ACTIVE'}">활성</option>
                        <option value="SUSPENDED" th:selected="${param.status == 'SUSPENDED'}">정지</option>
                        <option value="WITHDRAWN" th:selected="${param.status == 'WITHDRAWN'}">탈퇴</option>
                    </select>
                </div>
                <div class="col-md-2">
                    <label for="dateFrom" class="form-label">가입일 (시작)</label>
                    <input type="date" class="form-control" id="dateFrom" name="dateFrom" 
                           th:value="${param.dateFrom}">
                </div>
                <div class="col-md-2">
                    <label for="dateTo" class="form-label">가입일 (종료)</label>
                    <input type="date" class="form-control" id="dateTo" name="dateTo" 
                           th:value="${param.dateTo}">
                </div>
                <div class="col-md-2">
                    <label for="size" class="form-label">페이지 크기</label>
                    <select class="form-select" id="size" name="size">
                        <option value="10" th:selected="${members.size == 10}">10</option>
                        <option value="20" th:selected="${members.size == 20}">20</option>
                        <option value="50" th:selected="${members.size == 50}">50</option>
                    </select>
                </div>
                <div class="col-md-1 d-flex align-items-end">
                    <button type="submit" class="btn btn-outline-primary w-100">
                        <i class="fas fa-search"></i>
                    </button>
                </div>
            </form>
        </div>
    </div>

    <!-- 회원 목록 테이블 -->
    <div class="card">
        <div class="card-header d-flex justify-content-between align-items-center">
            <h5 class="mb-0">
                회원 목록 
                <span class="badge bg-secondary" th:text="${members.totalElements}">0</span>
            </h5>
            <div class="btn-group" role="group">
                <button type="button" class="btn btn-outline-secondary btn-sm" onclick="selectAll()">
                    전체 선택
                </button>
                <button type="button" class="btn btn-outline-danger btn-sm" onclick="suspendSelected()">
                    선택 정지
                </button>
                <button type="button" class="btn btn-outline-success btn-sm" onclick="exportSelected()">
                    선택 내보내기
                </button>
            </div>
        </div>
        <div class="card-body p-0">
            <div class="table-responsive">
                <table class="table table-hover mb-0">
                    <thead class="table-light">
                        <tr>
                            <th width="50">
                                <input type="checkbox" id="selectAllCheckbox" onchange="toggleSelectAll()">
                            </th>
                            <th>회원번호</th>
                            <th>이름</th>
                            <th>이메일</th>
                            <th>전화번호</th>
                            <th>상태</th>
                            <th>대출현황</th>
                            <th>가입일</th>
                            <th width="100">작업</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr th:each="member : ${members.content}" th:id="'member-' + ${member.id}">
                            <td>
                                <input type="checkbox" class="member-checkbox" th:value="${member.id}">
                            </td>
                            <td>
                                <a th:href="@{/admin/members/{id}(id=${member.id})}" 
                                   th:text="${member.memberNumber}">M000000001</a>
                            </td>
                            <td th:text="${member.name}">김철수</td>
                            <td th:text="${member.email}">kim@email.com</td>
                            <td th:text="${member.phoneNumber}">010-1234-5678</td>
                            <td>
                                <span class="badge" 
                                      th:classappend="${member.status.name() == 'ACTIVE'} ? 'bg-success' : 
                                                     (${member.status.name() == 'SUSPENDED'} ? 'bg-warning' : 'bg-secondary')"
                                      th:text="${member.status.description}">활성</span>
                            </td>
                            <td>
                                <span th:text="${member.currentLoanCount} + '/' + ${member.maxLoanCount}">2/5</span>
                                <span th:if="${member.hasOverdueLoans}" class="text-danger ms-1">
                                    <i class="fas fa-exclamation-triangle" title="연체 중"></i>
                                </span>
                            </td>
                            <td th:text="${#temporals.format(member.createdAt, 'yyyy-MM-dd')}">2025-05-01</td>
                            <td>
                                <div class="btn-group btn-group-sm" role="group">
                                    <a th:href="@{/admin/members/{id}(id=${member.id})}" 
                                       class="btn btn-outline-primary" title="상세보기">
                                        <i class="fas fa-eye"></i>
                                    </a>
                                    <a th:href="@{/admin/members/{id}/edit(id=${member.id})}" 
                                       class="btn btn-outline-secondary" title="수정">
                                        <i class="fas fa-edit"></i>
                                    </a>
                                    <button type="button" class="btn btn-outline-danger" 
                                            th:onclick="'suspendMember(' + ${member.id} + ')'" title="정지">
                                        <i class="fas fa-ban"></i>
                                    </button>
                                </div>
                            </td>
                        </tr>
                        <tr th:if="${#lists.isEmpty(members.content)}">
                            <td colspan="9" class="text-center py-4 text-muted">
                                검색 결과가 없습니다.
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
        
        <!-- 페이지네이션 -->
        <div class="card-footer" th:if="${members.totalPages > 1}">
            <nav aria-label="회원 목록 페이지네이션">
                <ul class="pagination justify-content-center mb-0">
                    <!-- 이전 페이지 -->
                    <li class="page-item" th:classappend="${members.first} ? 'disabled'">
                        <a class="page-link" th:href="@{/admin/members(page=${members.number - 1}, size=${members.size})}">
                            <i class="fas fa-chevron-left"></i>
                        </a>
                    </li>
                    
                    <!-- 페이지 번호들 -->
                    <li th:each="pageNum : ${#numbers.sequence(0, members.totalPages - 1)}" 
                        class="page-item" th:classappend="${pageNum == members.number} ? 'active'">
                        <a class="page-link" th:href="@{/admin/members(page=${pageNum}, size=${members.size})}" 
                           th:text="${pageNum + 1}">1</a>
                    </li>
                    
                    <!-- 다음 페이지 -->
                    <li class="page-item" th:classappend="${members.last} ? 'disabled'">
                        <a class="page-link" th:href="@{/admin/members(page=${members.number + 1}, size=${members.size})}">
                            <i class="fas fa-chevron-right"></i>
                        </a>
                    </li>
                </ul>
            </nav>
        </div>
    </div>
</div>

<!-- 페이지별 JavaScript -->
<th:block layout:fragment="javascript">
<script>
    // 전체 선택/해제
    function toggleSelectAll() {
        const checkboxes = document.querySelectorAll('.member-checkbox');
        const selectAllCheckbox = document.getElementById('selectAllCheckbox');
        
        checkboxes.forEach(checkbox => {
            checkbox.checked = selectAllCheckbox.checked;
        });
    }

    // 회원 정지
    function suspendMember(memberId) {
        if (confirm('해당 회원을 정지하시겠습니까?')) {
            fetch(`/admin/members/${memberId}/suspend`, {
                method: 'PATCH',
                headers: {
                    'Content-Type': 'application/json',
                    'X-Requested-With': 'XMLHttpRequest'
                }
            })
            .then(response => {
                if (response.ok) {
                    location.reload();
                } else {
                    alert('정지 처리 중 오류가 발생했습니다.');
                }
            })
            .catch(error => {
                console.error('Error:', error);
                alert('정지 처리 중 오류가 발생했습니다.');
            });
        }
    }

    // 선택된 회원들 정지
    function suspendSelected() {
        const checkedBoxes = document.querySelectorAll('.member-checkbox:checked');
        if (checkedBoxes.length === 0) {
            alert('정지할 회원을 선택해주세요.');
            return;
        }

        if (confirm(`선택된 ${checkedBoxes.length}명의 회원을 정지하시겠습니까?`)) {
            const memberIds = Array.from(checkedBoxes).map(cb => cb.value);
            
            fetch('/admin/members/suspend-bulk', {
                method: 'PATCH',
                headers: {
                    'Content-Type': 'application/json',
                    'X-Requested-With': 'XMLHttpRequest'
                },
                body: JSON.stringify({ memberIds: memberIds })
            })
            .then(response => {
                if (response.ok) {
                    location.reload();
                } else {
                    alert('일괄 정지 처리 중 오류가 발생했습니다.');
                }
            });
        }
    }
</script>
</th:block>
</html>
```

**2. 도서 등록/수정 폼 (admin/books/form.html)**

```html
<!DOCTYPE html>
<html layout:decorate="~{layout/admin}" xmlns:th="http://www.thymeleaf.org">
<head>
    <title th:text="${book.id != null ? '도서 수정' : '도서 등록'}">도서 관리</title>
</head>

<div layout:fragment="content">
    <div class="row">
        <div class="col-lg-8">
            <div class="card">
                <div class="card-header">
                    <h5 class="mb-0" th:text="${book.id != null ? '도서 정보 수정' : '새 도서 등록'}">도서 등록</h5>
                </div>
                <div class="card-body">
                    <form th:action="${book.id != null ? '/admin/books/' + book.id : '/admin/books'}" 
                          th:method="${book.id != null ? 'put' : 'post'}" 
                          th:object="${book}" 
                          enctype="multipart/form-data">
                        
                        <!-- ISBN -->
                        <div class="mb-3">
                            <label for="isbn" class="form-label">
                                ISBN <span class="text-danger">*</span>
                            </label>
                            <div class="input-group">
                                <input type="text" class="form-control" id="isbn" th:field="*{isbn}" 
                                       th:classappend="${#fields.hasErrors('isbn')} ? 'is-invalid'"
                                       placeholder="13자리 ISBN 입력">
                                <button type="button" class="btn btn-outline-secondary" onclick="validateISBN()">
                                    <i class="fas fa-check"></i> 확인
                                </button>
                            </div>
                            <div class="invalid-feedback" th:if="${#fields.hasErrors('isbn')}" 
                                 th:errors="*{isbn}">ISBN 오류</div>
                            <div class="form-text">하이픈(-) 없이 13자리 숫자만 입력해주세요.</div>
                        </div>

                        <!-- 제목 -->
                        <div class="mb-3">
                            <label for="title" class="form-label">
                                도서명 <span class="text-danger">*</span>
                            </label>
                            <input type="text" class="form-control" id="title" th:field="*{title}"
                                   th:classappend="${#fields.hasErrors('title')} ? 'is-invalid'"
                                   placeholder="도서명을 입력하세요">
                            <div class="invalid-feedback" th:if="${#fields.hasErrors('title')}" 
                                 th:errors="*{title}">제목 오류</div>
                        </div>

                        <!-- 저자 -->
                        <div class="mb-3">
                            <label for="author" class="form-label">
                                저자 <span class="text-danger">*</span>
                            </label>
                            <input type="text" class="form-control" id="author" th:field="*{author}"
                                   th:classappend="${#fields.hasErrors('author')} ? 'is-invalid'"
                                   placeholder="저자명을 입력하세요">
                            <div class="invalid-feedback" th:if="${#fields.hasErrors('author')}" 
                                 th:errors="*{author}">저자 오류</div>
                        </div>

                        <!-- 출판사와 출간일 -->
                        <div class="row">
                            <div class="col-md-6">
                                <div class="mb-3">
                                    <label for="publisher" class="form-label">출판사</label>
                                    <input type="text" class="form-control" id="publisher" th:field="*{publisher}"
                                           placeholder="출판사명">
                                </div>
                            </div>
                            <div class="col-md-6">
                                <div class="mb-3">
                                    <label for="publicationDate" class="form-label">출간일</label>
                                    <input type="date" class="form-control" id="publicationDate" 
                                           th:field="*{publicationDate}">
                                </div>
                            </div>
                        </div>

                        <!-- 카테고리 -->
                        <div class="mb-3">
                            <label for="categoryId" class="form-label">
                                카테고리 <span class="text-danger">*</span>
                            </label>
                            <select class="form-select" id="categoryId" th:field="*{categoryId}"
                                    th:classappend="${#fields.hasErrors('categoryId')} ? 'is-invalid'">
                                <option value="">카테고리를 선택하세요</option>
                                <optgroup th:each="parentCategory : ${categories}" 
                                          th:label="${parentCategory.name}">
                                    <option th:each="category : ${parentCategory.children}" 
                                            th:value="${category.id}" 
                                            th:text="${category.name}">카테고리명</option>
                                </optgroup>
                            </select>
                            <div class="invalid-feedback" th:if="${#fields.hasErrors('categoryId')}" 
                                 th:errors="*{categoryId}">카테고리 오류</div>
                        </div>

                        <!-- 보유 수량과 가격 -->
                        <div class="row">
                            <div class="col-md-4">
                                <div class="mb-3">
                                    <label for="totalCopies" class="form-label">
                                        총 보유 수량 <span class="text-danger">*</span>
                                    </label>
                                    <input type="number" class="form-control" id="totalCopies" 
                                           th:field="*{totalCopies}" min="1" max="999"
                                           th:classappend="${#fields.hasErrors('totalCopies')} ? 'is-invalid'">
                                    <div class="invalid-feedback" th:if="${#fields.hasErrors('totalCopies')}" 
                                         th:errors="*{totalCopies}">수량 오류</div>
                                </div>
                            </div>
                            <div class="col-md-4">
                                <div class="mb-3">
                                    <label for="availableCopies" class="form-label">대출 가능 수량</label>
                                    <input type="number" class="form-control" id="availableCopies" 
                                           th:field="*{availableCopies}" min="0"
                                           readonly th:if="${book.id != null}">
                                    <div class="form-text" th:if="${book.id != null}">
                                        수정 시에는 대출 현황에 따라 자동 계산됩니다.
                                    </div>
                                </div>
                            </div>
                            <div class="col-md-4">
                                <div class="mb-3">
                                    <label for="price" class="form-label">가격</label>
                                    <div class="input-group">
                                        <input type="number" class="form-control" id="price" 
                                               th:field="*{price}" min="0" step="100"
                                               placeholder="0">
                                        <span class="input-group-text">원</span>
                                    </div>
                                </div>
                            </div>
                        </div>

                        <!-- 페이지 수와 언어 -->
                        <div class="row">
                            <div class="col-md-6">
                                <div class="mb-3">
                                    <label for="pageCount" class="form-label">페이지 수</label>
                                    <input type="number" class="form-control" id="pageCount" 
                                           th:field="*{pageCount}" min="1">
                                </div>
                            </div>
                            <div class="col-md-6">
                                <div class="mb-3">
                                    <label for="language" class="form-label">언어</label>
                                    <select class="form-select" id="language" th:field="*{language}">
                                        <option value="KO">한국어</option>
                                        <option value="EN">영어</option>
                                        <option value="JA">일본어</option>
                                        <option value="CN">중국어</option>
                                        <option value="OTHER">기타</option>
                                        <option value="OTHER">기타</option>
                                    </select>
                                </div>
                            </div>
                        </div>

                        <!-- 도서 설명 -->
                        <div class="mb-3">
                            <label for="description" class="form-label">도서 설명</label>
                            <textarea class="form-control" id="description" th:field="*{description}" 
                                      rows="4" placeholder="도서에 대한 간단한 설명을 입력하세요"></textarea>
                        </div>

                        <!-- 표지 이미지 업로드 -->
                        <div class="mb-4">
                            <label for="imageFile" class="form-label">표지 이미지</label>
                            <input type="file" class="form-control" id="imageFile" name="imageFile" 
                                   accept="image/*" onchange="previewImage(this)">
                            <div class="form-text">JPG, PNG 파일만 업로드 가능합니다. (최대 5MB)</div>
                            
                            <!-- 이미지 미리보기 -->
                            <div class="mt-3" id="imagePreview">
                                <img th:if="${book.imageUrl}" th:src="${book.imageUrl}" 
                                     class="img-thumbnail" style="max-width: 200px; max-height: 300px;" 
                                     alt="현재 표지 이미지">
                            </div>
                        </div>

                        <!-- 버튼 그룹 -->
                        <div class="d-flex justify-content-between">
                            <a th:href="@{/admin/books}" class="btn btn-secondary">
                                <i class="fas fa-arrow-left"></i> 목록으로
                            </a>
                            <div>
                                <button type="button" class="btn btn-outline-primary me-2" onclick="validateForm()">
                                    <i class="fas fa-check"></i> 검증
                                </button>
                                <button type="submit" class="btn btn-primary">
                                    <i class="fas fa-save"></i> 
                                    <span th:text="${book.id != null ? '수정' : '등록'}">등록</span>
                                </button>
                            </div>
                        </div>
                    </form>
                </div>
            </div>
        </div>

        <!-- 사이드 정보 패널 -->
        <div class="col-lg-4">
            <!-- 도서 정보 요약 (수정 시에만) -->
            <div th:if="${book.id != null}" class="card mb-4">
                <div class="card-header">
                    <h6 class="mb-0">현재 대출 정보</h6>
                </div>
                <div class="card-body">
                    <div class="row text-center">
                        <div class="col-4">
                            <div class="h4 mb-0" th:text="${book.totalCopies}">3</div>
                            <small class="text-muted">총 권수</small>
                        </div>
                        <div class="col-4">
                            <div class="h4 mb-0" th:text="${book.availableCopies}">1</div>
                            <small class="text-muted">대출가능</small>
                        </div>
                        <div class="col-4">
                            <div class="h4 mb-0" th:text="${book.totalCopies - book.availableCopies}">2</div>
                            <small class="text-muted">대출중</small>
                        </div>
                    </div>
                </div>
            </div>

            <!-- 도움말 -->
            <div class="card">
                <div class="card-header">
                    <h6 class="mb-0">
                        <i class="fas fa-question-circle"></i> 도움말
                    </h6>
                </div>
                <div class="card-body">
                    <div class="accordion accordion-flush" id="helpAccordion">
                        <div class="accordion-item">
                            <h2 class="accordion-header">
                                <button class="accordion-button collapsed" type="button" 
                                        data-bs-toggle="collapse" data-bs-target="#isbnHelp">
                                    ISBN 입력 방법
                                </button>
                            </h2>
                            <div id="isbnHelp" class="accordion-collapse collapse" 
                                 data-bs-parent="#helpAccordion">
                                <div class="accordion-body">
                                    <ul class="list-unstyled mb-0">
                                        <li>• 13자리 숫자만 입력</li>
                                        <li>• 하이픈(-) 제거</li>
                                        <li>• 예: 9788932917245</li>
                                    </ul>
                                </div>
                            </div>
                        </div>
                        
                        <div class="accordion-item">
                            <h2 class="accordion-header">
                                <button class="accordion-button collapsed" type="button" 
                                        data-bs-toggle="collapse" data-bs-target="#categoryHelp">
                                    카테고리 선택
                                </button>
                            </h2>
                            <div id="categoryHelp" class="accordion-collapse collapse" 
                                 data-bs-parent="#helpAccordion">
                                <div class="accordion-body">
                                    적절한 하위 카테고리를 선택해주세요. 
                                    새로운 카테고리가 필요한 경우 
                                    <a href="/admin/categories">카테고리 관리</a>에서 추가할 수 있습니다.
                                </div>
                            </div>
                        </div>

                        <div class="accordion-item">
                            <h2 class="accordion-header">
                                <button class="accordion-button collapsed" type="button" 
                                        data-bs-toggle="collapse" data-bs-target="#imageHelp">
                                    이미지 업로드
                                </button>
                            </h2>
                            <div id="imageHelp" class="accordion-collapse collapse" 
                                 data-bs-parent="#helpAccordion">
                                <div class="accordion-body">
                                    <ul class="list-unstyled mb-0">
                                        <li>• 권장 크기: 300x400px</li>
                                        <li>• 지원 형식: JPG, PNG</li>
                                        <li>• 최대 크기: 5MB</li>
                                        <li>• 비율: 3:4 (세로형)</li>
                                    </ul>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

<!-- 페이지별 JavaScript -->
<th:block layout:fragment="javascript">
<script>
    // 이미지 미리보기
    function previewImage(input) {
        const preview = document.getElementById('imagePreview');
        
        if (input.files && input.files[0]) {
            const reader = new FileReader();
            
            reader.onload = function(e) {
                preview.innerHTML = `
                    <img src="${e.target.result}" class="img-thumbnail" 
                         style="max-width: 200px; max-height: 300px;" alt="미리보기">
                `;
            };
            
            reader.readAsDataURL(input.files[0]);
        }
    }

    // ISBN 유효성 검사
    function validateISBN() {
        const isbn = document.getElementById('isbn').value.replace(/[^0-9]/g, '');
        
        if (isbn.length !== 13) {
            alert('ISBN은 13자리 숫자여야 합니다.');
            return false;
        }

        // ISBN-13 체크섬 검증
        let sum = 0;
        for (let i = 0; i < 12; i++) {
            sum += parseInt(isbn[i]) * (i % 2 === 0 ? 1 : 3);
        }
        const checkDigit = (10 - (sum % 10)) % 10;
        
        if (parseInt(isbn[12]) !== checkDigit) {
            alert('유효하지 않은 ISBN입니다.');
            return false;
        }

        // 중복 체크 (AJAX)
        fetch(`/admin/books/check-isbn?isbn=${isbn}`)
            .then(response => response.json())
            .then(data => {
                if (data.exists) {
                    alert('이미 등록된 ISBN입니다.');
                } else {
                    alert('사용 가능한 ISBN입니다.');
                }
            })
            .catch(error => {
                console.error('Error:', error);
                alert('ISBN 확인 중 오류가 발생했습니다.');
            });
    }

    // 폼 검증
    function validateForm() {
        const form = document.querySelector('form');
        const formData = new FormData(form);
        
        // 필수 필드 검사
        const requiredFields = ['isbn', 'title', 'author', 'categoryId', 'totalCopies'];
        const missingFields = [];
        
        requiredFields.forEach(field => {
            if (!formData.get(field) || formData.get(field).trim() === '') {
                missingFields.push(field);
            }
        });
        
        if (missingFields.length > 0) {
            alert('필수 항목을 모두 입력해주세요.');
            return false;
        }
        
        alert('모든 검증이 완료되었습니다.');
        return true;
    }

    // 총 보유 수량 변경 시 대출 가능 수량 자동 설정 (신규 등록 시)
    document.getElementById('totalCopies').addEventListener('change', function() {
        const availableCopiesField = document.getElementById('availableCopies');
        if (!availableCopiesField.readOnly) {
            availableCopiesField.value = this.value;
        }
    });
</script>
</th:block>
</html>
```
- 실무에서 사용하는 대출 승인 관리 시스템
- 일괄 처리 기능 (선택된 항목들을 한번에 승인/거절)
- 실시간 상태 업데이트
- 모달을 통한 사용자 친화적 인터페이스
- 우선순위 및 재고 상태 표시

### 3. **CSS 스타일 시트 (admin.css)**
- 전문적인 관리자 페이지 디자인
- 반응형 웹 디자인 (모바일, 태블릿, 데스크톱)
- 다크 모드 지원
- 애니메이션 효과 및 호버 스타일
- 접근성을 고려한 색상 대비