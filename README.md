# Claude Code 사용 가이드

Claude Code를 효과적으로 활용하기 위한 커스텀 명령어와 스킬 모음입니다.

## 목차

- [적용 방법](#적용-방법)
- [빠른 시작](#빠른-시작)
- [코드 리뷰](#코드-리뷰)
- [자동 테스트 생성](#자동-테스트-생성)
- [개발 도구 스킬](#개발-도구-스킬)
- [프로젝트 설정](#프로젝트-설정)

---

## 코드 리뷰

커스텀 슬래시 커맨드를 통해 자동화된 코드 리뷰를 제공합니다.

### 사용 가능한 커맨드

#### 1. `/code-review` - 통합 코드 리뷰
변경된 파일을 자동 감지하여 적절한 코드 리뷰를 수행합니다.

```bash
/code-review
```

#### 2. `/review-frontend` - 프론트엔드 리뷰
프론트엔드 코드에 대한 전문적인 리뷰를 수행합니다.

```bash
/review-frontend
```

**검토 항목:**
- 코드 품질 (컴포넌트 구조, 재사용성, 네이밍 컨벤션)
- React 특화 (리렌더링 최적화, 상태 관리, useEffect)
- 성능 (번들 사이즈, lazy loading, API 최적화)
- 보안 (XSS, 민감 정보 노출, CSRF)
- 접근성 (a11y, 시맨틱 HTML, ARIA)
- 타입 안전성 (TypeScript, any 사용 최소화)

#### 3. `/review-backend` - 백엔드 리뷰
백엔드 코드에 대한 전문적인 리뷰를 수행합니다.

```bash
/review-backend
```

**검토 항목:**
- 코드 품질 (구조, 네이밍, DRY 원칙)
- 아키텍처 (계층 분리, 의존성, SOLID)
- 성능 (쿼리 최적화, 캐싱, 비동기 처리)
- 보안 (SQL Injection, 인증/인가, 민감 정보)
- 에러 처리 (예외 처리, 로깅, 복구 전략)
- 테스트 (단위/통합 테스트, 커버리지)

### 사용 팁

1. **코드 작성 후 즉시 리뷰**: 커밋 전에 리뷰 커맨드를 실행하세요
2. **특화된 리뷰 활용**: 프론트/백엔드 전문 리뷰로 더 상세한 피드백 받기
3. **자동 감지**: `/code-review`는 변경사항을 자동으로 분석합니다

---

## 자동 테스트 생성

코드 변경을 감지하고 자동으로 테스트 코드를 생성합니다.

### 사용 방법

Claude Code에서 스킬을 호출하세요:

```
auto-test-generator
```

또는 자연어로 요청:
```
"변경된 코드에 대한 테스트 생성해줘"
"UserProfile 컴포넌트 테스트 코드 만들어줘"
```

### 주요 기능

#### 1. 자동 변경사항 감지
- Git diff를 통해 변경된 파일 자동 스캔
- 프론트엔드/백엔드 파일 자동 분류
- 새로운 함수, 컴포넌트, API 엔드포인트 감지

#### 2. 프론트엔드 테스트 생성
**지원 대상:**
- React 컴포넌트 (렌더링, Props, 이벤트 핸들러)
- Custom Hooks (상태 관리, 부작용)
- 유틸리티 함수 (정상/엣지/에러 케이스)

**테스트 프레임워크:**
- Jest + React Testing Library
- @testing-library/react-hooks

**생성 예시:**
```typescript
describe('UserProfile', () => {
  it('should render user information', () => {
    render(<UserProfile user={mockUser} />);
    expect(screen.getByText('John Doe')).toBeInTheDocument();
  });

  it('should handle edit button click', () => {
    const onEdit = jest.fn();
    render(<UserProfile user={mockUser} onEdit={onEdit} />);
    fireEvent.click(screen.getByRole('button', { name: 'Edit' }));
    expect(onEdit).toHaveBeenCalledWith(mockUser.id);
  });
});
```

#### 3. 백엔드 테스트 생성
**지원 대상:**
- API 엔드포인트 (성공/실패/인증)
- Service 레이어 (비즈니스 로직, 에러 처리)
- Repository (CRUD, 쿼리, 트랜잭션)

**테스트 프레임워크:**
- Jest + Supertest (API 테스트)
- Jest (단위 테스트)

**생성 예시:**
```typescript
describe('POST /api/users', () => {
  it('should create user successfully', async () => {
    const response = await request(app)
      .post('/api/users')
      .send({ name: 'John', email: 'john@example.com' })
      .expect(201);

    expect(response.body).toHaveProperty('id');
    expect(response.body.name).toBe('John');
  });

  it('should validate required fields', async () => {
    await request(app)
      .post('/api/users')
      .send({})
      .expect(400);
  });
});
```

#### 4. 기존 테스트 업데이트
- 변경된 함수 시그니처에 맞춰 테스트 수정
- 새로운 기능에 대한 테스트 케이스 추가
- Deprecated된 테스트 자동 감지 및 제거

### 테스트 커버리지 목표

스킬이 다음 항목들을 자동으로 테스트합니다:

**프론트엔드:**
- ✅ 컴포넌트 렌더링
- ✅ Props 전달 및 검증
- ✅ 사용자 이벤트 처리
- ✅ 조건부 렌더링
- ✅ 비동기 데이터 로딩
- ✅ 에러 상태 처리

**백엔드:**
- ✅ API 엔드포인트 정상 동작
- ✅ 입력 검증 및 에러 처리
- ✅ 인증/인가 체크
- ✅ DB 작업 (CRUD)
- ✅ 비즈니스 로직
- ✅ 에러 응답 형식

### 실행 결과 예시

```
🔍 변경사항 스캔 중...

📁 감지된 변경 파일:
  - src/components/UserProfile.tsx (프론트엔드)
  - src/services/userService.ts (백엔드)

⚙️ 테스트 생성 중...

✅ 생성 완료:
  - src/components/UserProfile.test.tsx (4 test cases)
    └─ should render user information
    └─ should handle edit button click
    └─ should display loading state
    └─ should handle error state

  - src/services/userService.test.ts (6 test cases)
    └─ should fetch user by id
    └─ should create new user
    └─ should update user
    └─ should delete user
    └─ should handle database errors
    └─ should validate user data

📊 테스트 커버리지: 87%
```

### 필수 의존성

테스트 실행을 위해 다음 패키지가 필요합니다:

```json
{
  "devDependencies": {
    "@testing-library/react": "^14.0.0",
    "@testing-library/react-hooks": "^8.0.1",
    "@testing-library/jest-dom": "^6.1.5",
    "jest": "^29.7.0",
    "supertest": "^6.3.3",
    "@types/jest": "^29.5.11",
    "@types/supertest": "^6.0.2"
  }
}
```

### 테스트 실행

```bash
# 모든 테스트 실행
npm test

# 커버리지 리포트
npm test -- --coverage

# Watch 모드
npm test -- --watch

# 특정 파일만 테스트
npm test -- UserProfile.test.tsx
```

### 추가 정보

자세한 내용은 다음 문서를 참고하세요:
- [스킬 가이드](.claude/skills/auto-test-generator/README.md) - 전체 사용법
- [출력 형식](.claude/skills/auto-test-generator/FORMS.md) - 테스트 코드 생성 형식
- [테스트 템플릿](.claude/skills/auto-test-generator/templates/) - 각종 테스트 템플릿

---

## 개발 도구 스킬

개발 생산성을 높이는 다양한 스킬들이 포함되어 있습니다.

### 1. Skill Creator - 커스텀 스킬 생성

새로운 Claude Code 스킬을 만들 때 사용하는 가이드입니다.

**사용 방법:**
```
"새로운 스킬을 만들고 싶어"
"스킬 생성 방법 알려줘"
```

**주요 기능:**
- 스킬 구조 및 설계 가이드
- SKILL.md 작성 방법
- Scripts, References, Assets 활용법
- 스킬 패키징 및 배포

**자세한 내용:** [.claude/skills/skill-creator/SKILL.md](.claude/skills/skill-creator/SKILL.md)

---

### 2. MCP Builder - MCP 서버 개발

외부 서비스와 Claude를 연결하는 MCP 서버를 만들 때 사용합니다.

**사용 방법:**
```
"MCP 서버 만들고 싶어"
"GitHub API를 Claude와 연결하고 싶어"
```

**지원 언어:**
- Python (FastMCP)
- TypeScript/Node.js (MCP SDK)

**주요 기능:**
- MCP 프로토콜 가이드
- API 통합 베스트 프랙티스
- 도구(Tool) 설계 원칙
- 에러 처리 및 테스트 전략
- 실전 예제 및 템플릿

**포함 리소스:**
- [Python 구현 가이드](.claude/skills/mcp-builder/reference/python_mcp_server.md)
- [TypeScript 구현 가이드](.claude/skills/mcp-builder/reference/node_mcp_server.md)
- [MCP 베스트 프랙티스](.claude/skills/mcp-builder/reference/mcp_best_practices.md)
- [평가(Evaluation) 가이드](.claude/skills/mcp-builder/reference/evaluation.md)

**자세한 내용:** [.claude/skills/mcp-builder/SKILL.md](.claude/skills/mcp-builder/SKILL.md)

---

### 3. Webapp Testing - Playwright 테스팅

로컬 웹 애플리케이션을 Playwright로 테스트할 때 사용합니다.

**사용 방법:**
```
"웹앱 테스트해줘"
"로그인 플로우 테스트 만들어줘"
```

**주요 기능:**
- UI 동작 검증
- 브라우저 스크린샷 캡처
- 브라우저 로그 확인
- E2E 테스트 시나리오 작성

**자세한 내용:** [.claude/skills/webapp-testing/SKILL.md](.claude/skills/webapp-testing/SKILL.md)

---

### 4. Theme Factory - 테마 및 스타일링

아티팩트(슬라이드, 문서, 웹페이지 등)에 테마를 적용할 때 사용합니다.

**사용 방법:**
```
"이 문서에 테마 적용해줘"
"프레젠테이션에 프로페셔널한 스타일 추가해줘"
```

**주요 기능:**
- 10가지 프리셋 테마
- 커스텀 테마 생성
- 색상 및 타이포그래피 적용
- 다양한 아티팩트 타입 지원

**자세한 내용:** [.claude/skills/theme-factory/SKILL.md](.claude/skills/theme-factory/SKILL.md)

---

### 5. Design System Storybook - 자동 문서화

디자인 시스템 컴포넌트를 만들 때 Storybook stories와 문서를 자동 생성합니다.

**사용 방법:**
```
"Button 컴포넌트에 Storybook 추가해줘"
"이 컴포넌트 문서화해줘"
```

**주요 기능:**
- Storybook stories 파일 자동 생성 (*.stories.tsx)
- 컴포넌트 문서 자동 생성 (*.md)
- 모든 variants와 props 예제 포함
- 접근성(a11y) 정보 포함
- Interactive controls 설정

**생성되는 Stories:**
- Default, Variants, Sizes
- State Stories (Disabled, Loading)
- Interactive Story (모든 controls)
- AllVariants Story (한눈에 보기)
- Accessibility Story

**지원 컴포넌트:**
- 기본: Button, Input, Checkbox, Card, Badge
- 복잡: Modal, Dropdown, Tabs, DataTable
- 레이아웃: Container, Grid, Stack

**자세한 내용:** [.claude/skills/design-system-storybook/SKILL.md](.claude/skills/design-system-storybook/SKILL.md)

---

### 6. React Native Feature Builder - 체계적 기능 개발

React Native 앱 기능을 4단계 워크플로우로 체계적으로 개발합니다.

**사용 방법:**
```
"사용자 로그인 기능 만들어줘"
"게시글 목록 화면 구현해줘"
"프로필 편집 기능 추가해줘"
```

**4단계 워크플로우:**

**Phase 1: 요구사항 정리**
- 사용자 스토리 작성
- 기능 목록 및 우선순위
- 제약사항 파악

**Phase 2: 상세 기능 분석**
- 화면 흐름 정의
- Entity 및 데이터 모델 정의
- API 엔드포인트 설계
- 필요한 라이브러리 파악

**Phase 3: Clean Architecture 설계**
- Domain Layer (Entities, Use Cases, Repository Interfaces)
- Data Layer (Repository 구현, Data Sources)
- Presentation Layer (Screens, Hooks, Components)
- 파일 구조 설계

**Phase 4: 구현 및 컨벤션 적용**
- 프로젝트 컨벤션 자동 적용
- 레이어별 코드 생성
- 테스트 코드 작성
- 문서화

**Clean Architecture 레이어:**
```
Presentation → Domain ← Data → Infrastructure
```

**포함 리소스:**
- [Clean Architecture 가이드](.claude/skills/react-native-feature-builder/reference/clean-architecture.md)
- [React Native 컨벤션](.claude/skills/react-native-feature-builder/reference/react-native-conventions.md)
- [Use Case 템플릿](.claude/skills/react-native-feature-builder/templates/usecase-template.md)
- [Screen 템플릿](.claude/skills/react-native-feature-builder/templates/screen-template.md)

**자세한 내용:** [.claude/skills/react-native-feature-builder/SKILL.md](.claude/skills/react-native-feature-builder/SKILL.md)

---

### 7. Spring Boot Feature Builder - 백엔드 API 체계적 개발

Spring Boot 백엔드 API를 4단계 워크플로우로 체계적으로 개발합니다.

**사용 방법:**
```
"사용자 관리 API 만들어줘"
"게시글 CRUD API 구현해줘"
"주문 생성 API 추가해줘"
```

**4단계 워크플로우:**

**Phase 1: 요구사항 정리**
- RESTful API 엔드포인트 목록
- HTTP 메서드 및 상태 코드
- 우선순위 설정

**Phase 2: API 및 데이터 설계**
- JPA Entity 설계
- Request/Response DTO 설계
- API 명세서 작성
- Entity 관계 정의 (OneToMany, ManyToMany)

**Phase 3: Layered Architecture 설계**
- Controller Layer (REST API)
- Service Layer (비즈니스 로직, 트랜잭션)
- Repository Layer (JPA, 커스텀 쿼리)
- Entity Layer (도메인 모델)

**Phase 4: 구현 및 컨벤션 적용**
- Spring Boot 컨벤션 자동 적용
- 레이어별 코드 생성
- GlobalExceptionHandler
- 테스트 코드 (@DataJpaTest, @WebMvcTest)

**Layered Architecture:**
```
Controller → Service → Repository → Entity
```

**포함 리소스:**
- [Spring Boot 컨벤션](.claude/skills/spring-boot-feature-builder/reference/spring-boot-conventions.md)
- [Service 템플릿](.claude/skills/spring-boot-feature-builder/templates/service-template.md)

**자세한 내용:** [.claude/skills/spring-boot-feature-builder/SKILL.md](.claude/skills/spring-boot-feature-builder/SKILL.md)

---

## 프로젝트 설정

### 설치 및 설정

1. **플러그인 충돌 방지**
   - Claude Code 설정: `/settings`
   - **Plugins** 섹션에서 `anthropic-agent-skills` 또는 `example-skills` 비활성화
   - 프로젝트 스킬만 사용되며 팀원들과 Git 공유 가능

2. **스킬 커스터마이징**
   - 모든 스킬은 `.claude/skills/` 디렉토리에 있음
   - 자유롭게 수정 가능
   - Git 커밋으로 팀 전체 공유

### 프로젝트 구조

```
.claude/
├── commands/                       # 슬래시 커맨드
│   ├── code-review.md             # /code-review
│   ├── review-frontend.md         # /review-frontend
│   └── review-backend.md          # /review-backend
├── skills/                         # AI 스킬
│   ├── auto-test-generator/       # 테스트 자동 생성
│   ├── skill-creator/             # 스킬 생성 가이드
│   ├── mcp-builder/               # MCP 서버 개발
│   ├── webapp-testing/            # Playwright 테스팅
│   ├── theme-factory/             # 테마 적용
│   ├── design-system-storybook/   # Storybook 자동 생성
│   ├── react-native-feature-builder/  # React Native 개발
│   ├── spring-boot-feature-builder/   # Spring Boot 개발
│   └── project-guide.md           # 프로젝트 컨벤션
└── README.md                       # 이 문서
```

---

## 적용 방법

### 사전 요구사항

- **Claude Pro 이상 요금제** (필수)
- Claude Code CLI 설치

### 설치 방법

기존 프로젝트에 이 템플릿을 적용하는 방법:

1. **`.claude` 폴더 복사**
   ```bash
   # 이 저장소를 클론하거나 다운로드
   git clone <repository-url>

   # 기존 프로젝트 루트에 .claude 폴더 복사
   cp -r AI_TEMPLATE/.claude /path/to/your/project/
   ```

2. **플러그인 비활성화 (선택사항)**
   - Claude Code에서 `/settings` 실행
   - **Plugins** 섹션에서 `anthropic-agent-skills` 비활성화
   - 프로젝트 스킬이 우선 적용됨

3. **바로 사용 가능**
   ```
   /code-review
   auto-test-generator
   ```

> **💡 Tip**: `.claude` 폴더를 Git에 커밋하면 팀 전체가 동일한 스킬과 커맨드를 공유할 수 있습니다.

---

## 빠른 시작

1. **코드 작성 후 리뷰하기**
   ```
   /code-review
   ```

2. **테스트 코드 생성하기**
   ```
   auto-test-generator
   ```

3. **새로운 기능 개발하기**
   ```
   "사용자 로그인 기능 만들어줘"  (React Native)
   "게시글 CRUD API 구현해줘"      (Spring Boot)
   ```

4. **도움말 보기**
   ```
   /help
   ```