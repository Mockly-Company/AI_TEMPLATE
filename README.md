# Claude Code 사용 가이드

Claude Code를 효과적으로 활용하기 위한 커스텀 명령어와 스킬 모음입니다.

## 목차

- [적용 방법](#적용-방법)
- [빠른 시작](#빠른-시작)
- [코드 리뷰](#코드-리뷰)
- [테스트 생성 스킬](#테스트-생성-스킬)
- [개발 도구 스킬](#개발-도구-스킬)
- [프로젝트 설정](#프로젝트-설정)

---

## 코드 리뷰

Git 변경사항을 자동으로 감지하여 프론트엔드/백엔드에 맞는 전문적인 코드 리뷰를 제공합니다.

### Code Review 스킬

변경된 파일의 타입을 자동으로 분석하여 적절한 리뷰 가이드를 참조합니다.

**사용 방법:**
```
code-review
```

또는 자연어로 요청:
```
"코드 리뷰해줘"
"변경사항 검토해줘"
```

### 자동 분류 시스템

스킬이 파일을 자동으로 분류하여 적절한 검토를 수행합니다:

#### 프론트엔드 파일
- React/React Native 컴포넌트: `*.tsx`, `*.jsx`
- 프론트엔드 디렉토리: `src/components/`, `src/screens/`, `src/hooks/`
- 스타일: `*.css`, `*.scss`

**검토 항목:**
- React 특화 (리렌더링 최적화, 상태 관리, useEffect)
- 성능 (번들 사이즈, lazy loading, API 최적화)
- 보안 (XSS, 민감 정보 노출)
- 접근성 (a11y, 시맨틱 HTML, ARIA)
- TypeScript 타입 안전성

#### 백엔드 파일
- Spring Boot: `*Controller.java`, `*Service.java`, `*Repository.java`
- Node.js/Express: `*route.ts`, `*controller.ts`, `*service.ts`
- 백엔드 디렉토리: `src/api/`, `src/services/`, `src/main/java/`

**검토 항목:**
- 아키텍처 (계층 분리, 의존성, SOLID)
- 보안 (SQL Injection, 인증/인가, 입력 검증)
- 성능 (쿼리 최적화, 캐싱, 비동기 처리)
- 에러 처리 (예외 처리, 로깅)
- 데이터베이스 (N+1 문제, 트랜잭션)

#### 풀스택 변경사항
프론트엔드와 백엔드 파일이 모두 포함된 경우, 두 가지 관점을 모두 적용하여 종합적인 리뷰를 제공합니다.

### 리뷰 결과 형식

```markdown
# 코드 리뷰 결과

## 📊 변경사항 요약
- 총 변경 파일: 5개
- 프론트엔드: 3개
- 백엔드: 2개

## 🔍 상세 리뷰

### UserProfile.tsx - 프론트엔드

#### 🔴 Critical Issues
- [XSS 취약점] dangerouslySetInnerHTML 사용 시 sanitize 누락

#### 🟡 Warnings
- [성능] useCallback 누락으로 불필요한 리렌더링 발생

#### ✅ Good Practices
- TypeScript 타입 정의가 명확함
```

### 참고 자료

자세한 리뷰 기준:
- [프론트엔드 리뷰 가이드](.claude/skills/code-review/references/frontend-review-guide.md)
- [백엔드 리뷰 가이드](.claude/skills/code-review/references/backend-review-guide.md)

---

## 테스트 생성 스킬

프로젝트의 프론트엔드와 백엔드 코드에 대한 테스트를 자동으로 생성합니다.

### 1. Backend Test Generator - Spring Boot 테스트 생성

Spring Boot 애플리케이션의 단위 테스트와 통합 테스트를 자동으로 생성합니다.

**사용 방법:**
```
backend-test-generator
```

또는 자연어로 요청:
```
"UserController에 대한 테스트 생성해줘"
"백엔드 변경사항에 대한 테스트 작성해줘"
```

**지원 스택:**
- Spring Boot 2.x/3.x
- JUnit 5 + Mockito
- MockMvc, TestRestTemplate
- @DataJpaTest, @WebMvcTest, @SpringBootTest

**생성되는 테스트 타입:**
- **Controller 테스트**: MockMvc를 사용한 REST API 테스트
- **Service 테스트**: Mockito를 사용한 비즈니스 로직 테스트
- **Repository 테스트**: @DataJpaTest를 사용한 DB 테스트
- **통합 테스트**: 전체 API 플로우 E2E 테스트

**생성 예시 (Controller):**
```java
@WebMvcTest(UserController.class)
class UserControllerTest {
    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private UserService userService;

    @Test
    void testGetUser_Success() throws Exception {
        given(userService.getUserById(1L))
            .willReturn(userDto);

        mockMvc.perform(get("/api/users/1"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.name", is("John Doe")));
    }
}
```

**자세한 내용:** [.claude/skills/backend-test-generator/SKILL.md](.claude/skills/backend-test-generator/SKILL.md)

---

### 2. Frontend Test Generator - React Native 테스트 생성

React Native 애플리케이션의 컴포넌트, 훅, 유틸리티 테스트를 자동으로 생성합니다.

**사용 방법:**
```
frontend-test-generator
```

또는 자연어로 요청:
```
"UserProfile 컴포넌트 테스트 생성해줘"
"useAuth 훅 테스트 업데이트해줘"
```

**지원 스택:**
- React Native + Expo
- Jest + React Native Testing Library
- @testing-library/react-hooks

**생성되는 테스트 타입:**
- **컴포넌트 테스트**: 렌더링, 상호작용, 접근성 테스트
- **훅 테스트**: 커스텀 훅 로직 및 상태 변화 테스트
- **유틸리티 테스트**: 순수 함수 및 헬퍼 함수 테스트
- **통합 테스트**: 네비게이션 및 전체 플로우 테스트

**생성 예시 (Component):**
```typescript
describe('UserProfile', () => {
  it('should render user information correctly', () => {
    render(<UserProfile user={mockUser} />);

    expect(screen.getByText('John Doe')).toBeTruthy();
    expect(screen.getByText('john@example.com')).toBeTruthy();
  });

  it('should handle button press', () => {
    const mockOnPress = jest.fn();
    render(<UserProfile user={mockUser} onEdit={mockOnPress} />);

    fireEvent.press(screen.getByRole('button', { name: 'Edit' }));

    expect(mockOnPress).toHaveBeenCalledTimes(1);
  });
});
```

**자세한 내용:** [.claude/skills/frontend-test-generator/SKILL.md](.claude/skills/frontend-test-generator/SKILL.md)

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

### 2. Prompt Enhancer - 프롬프트 강화

간단한 개발 요청을 프로젝트 특화 정보를 반영하여 더 정확하고 상세한 요구사항으로 변환합니다.

**사용 방법:**
```
"로그인 화면 만들어줘"
"사용자 목록 API 구현해줘"
```

**주요 기능:**
- 프로젝트 컨텍스트 자동 수집 (프레임워크, 아키텍처, 코딩 스타일)
- 프레임워크별 특화 정보 반영 (React Native, Spring Boot, Express.js 등)
- 기존 코드 패턴 분석 및 적용
- 구체적이고 실행 가능한 요구사항으로 변환

**지원 프레임워크:**
- **Frontend**: React (Vite/CRA), Next.js
- **Backend**: Express.js, Spring Boot
- **Mobile**: React Native

**변환 예시:**

사용자 입력:
```
"로그인 화면 만들어줘"
```

강화된 프롬프트:
```
React Native로 로그인 화면을 구현하되, 프로젝트의 Clean Architecture를 따라:
- Presentation Layer에 LoginScreen.tsx 생성
- Domain Layer에 LoginUseCase 생성
- React Navigation 사용하여 화면 전환
- 이메일/비밀번호 입력, 유효성 검증 포함
- 기존 프로젝트의 Button, Input 컴포넌트 재사용
- AsyncStorage를 사용한 토큰 저장
```

**포함 리소스:**
- [프레임워크 가이드](.claude/skills/prompt-enhancer/references/framework-guides.md) - React, Spring Boot 등
- [검색 전략](.claude/skills/prompt-enhancer/references/search-strategies.md) - 코드베이스 탐색 방법

**자세한 내용:** [.claude/skills/prompt-enhancer/SKILL.md](.claude/skills/prompt-enhancer/SKILL.md)

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
│   └── code-review.md             # /code-review (deprecated, skill 사용 권장)
├── skills/                         # AI 스킬
│   ├── backend-test-generator/    # Spring Boot 테스트 생성
│   ├── frontend-test-generator/   # React Native 테스트 생성
│   ├── code-review/               # 코드 리뷰 (자동 분류)
│   ├── skill-creator/             # 스킬 생성 가이드
│   └── prompt-enhancer/           # 프롬프트 강화
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
   code-review  # 자동으로 프론트/백엔드 분류하여 리뷰
   ```
   또는:
   ```
   "코드 리뷰해줘"
   "변경사항 검토해줘"
   ```

2. **테스트 코드 생성하기**
   ```
   backend-test-generator   # Spring Boot 테스트
   frontend-test-generator  # React Native 테스트
   ```

3. **프롬프트 강화하여 개발하기**
   ```
   "로그인 화면 만들어줘"
   "사용자 관리 API 구현해줘"
   ```
   → 자동으로 프로젝트 컨텍스트를 반영하여 상세한 요구사항으로 변환

4. **커스텀 스킬 만들기**
   ```
   "새로운 스킬 만들고 싶어"
   ```

5. **도움말 보기**
   ```
   /help
   ```