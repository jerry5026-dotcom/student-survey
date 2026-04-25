# 학생 기초조사 도우미 — Claude 운영 규칙

이 파일은 Claude Code가 이 프로젝트에서 일할 때 따라야 할 규칙을 정리한 문서입니다.
프로젝트 루트의 `CLAUDE.md`는 Claude Code가 자동으로 컨텍스트에 포함해 읽습니다.

---

## 📦 프로젝트 개요

- **목적**: 담임 교사를 위한 학생 기초조사 설문 + AI 상담 질문 자동 생성 도구
- **구조**: 단일 HTML 파일(`index.html`) + 외부 CDN(Google Identity Services, ExcelJS)
- **배포**: GitHub Pages — `https://jerry5026-dotcom.github.io/student-survey/`
- **기술 스택**: 순수 HTML/CSS/JS (빌드 도구·프레임워크 없음)

---

## 🔢 버전 관리 규칙 (Semantic Versioning)

`index.html` 푸터의 `v1.x.y` 표기를 다음 규칙으로 갱신한다.

### 버전 등급 판정 기준

| 등급 | 규모 | 예시 | 버전 변화 |
|---|---|---|---|
| **메이저** | 핵심 구조 변경 | 인증 방식 교체, 데이터 저장 구조 변경, 핵심 기능 전면 개편 | `1.x.x → 2.0.0` |
| **마이너** | 새 기능·항목 추가 | 새 탭, 아코디언, 일괄 생성, 모달 등 추가 | `1.0.x → 1.1.0` |
| **패치** | 작은 수정 | 오타·색상·문구·작은 버그·코드 정리 | `1.0.0 → 1.0.1` |

### 판정 원칙

- 한 작업에 여러 변경이 섞이면 **가장 큰 등급**으로 판정
- **메타 작업**(이 파일, 주석, README, gitignore 등 사용자에게 안 보이는 파일)은 버전 미반영
- 단순 콘텐츠 갱신(질문 텍스트 일부 수정 등)은 패치
- 새 AI 모델 추가는 마이너

### 갱신 절차

1. 코드 수정 시 `index.html` 푸터의 다음 두 항목을 함께 업데이트:
   - `v1.x.y` — 새 버전 번호
   - `마지막 업데이트 YYYY.MM.DD.` — 오늘 날짜
2. 사용자에게 보고할 때 형식:
   > "이번 변경은 [메이저/마이너/패치]라 **v1.0.0 → v1.0.1**로 올렸습니다."
3. 커밋 메시지에 버전을 포함하면 추적이 쉬워짐 (선택사항)

### 현재 버전

- **v1.0.0** (2026.04.24. 시점)
- 다음 코드 수정부터 위 규칙 적용

---

## 🛠 코드 작성·수정 규칙

### 일반 원칙

- **사용자가 명시적으로 수정을 요청한 경우에만** 코드를 변경
  - "확인해줘", "어떻게 하면 돼?", "가능해?" 같은 질문엔 답변만, 수정 X
- 한국어로 답변, 톤은 친근하지만 정중하게
- 배포 전 기본 검증 수행:
  - 중괄호·괄호 균형 체크
  - 새 함수 정의 + 호출 위치 확인
  - 변경 영역 주변 영향 범위 검토

### 단일 HTML 파일 구조 유지

이 프로젝트는 의도적으로 **단일 파일**로 운영된다. 새 기능 추가 시:

- 새 `.js`, `.css` 파일을 만들지 말 것
- 모든 코드를 `index.html` 안에 (`<style>`, `<script>` 블록)
- 외부 라이브러리는 CDN 스크립트 태그만 추가
- 빌드 도구(webpack, vite 등) 도입 금지 — 단순함이 핵심 가치

### 함수·변수 네이밍

- camelCase (JS) / kebab-case (CSS class·id)
- 한국어 주석 OK, 영어 주석도 OK (혼용 가능)
- 짧고 명확한 이름 선호 (`gen`, `q`, `r` 같은 단축은 지역 변수에서만)

---

## 🎨 디자인 원칙

### 색상 변수 (`:root`)

- `--blue` 계열: 주 액션, 정보
- `--green` 계열: 성공, AI 결과
- `--orange`: 경고, 주의
- `--red`: 에러, 위험 액션
- `--gray-50 ~ 900`: 단계별 회색 (700도 정의됨)

새 색상 추가 시 가능하면 기존 변수 재사용. 꼭 새로 만들어야 한다면 `:root`에 등록.

### UI 일관성

- 카드: `.card` 클래스 사용 (`box-shadow`, `border-radius` 통일)
- 버튼: `.btn` + 변형(`.btn-primary`, `.btn-outline`, `.btn-green`, `.btn-sm`)
- 토스트: `toast(msg, type)` 사용 — `type`은 `''`, `'success'`, `'error'`
- 모달: `.modal-overlay > .modal` 구조

### 반응형

- 모바일 대응: `@media(max-width:768px)`, `@media(max-width:600px)` 활용
- 새 그리드/플렉스 영역은 `flex-wrap:wrap` 기본

---

## 🔐 보안·프라이버시

### OAuth & API 키

- `CLIENT_ID`는 코드에 노출되어도 안전 (origin 제한이 보안 역할)
- 사용자 AI API 키는 `localStorage`에만 저장, 서버 전송 X
- 학생 응답 데이터는 메모리 + Google 서버에만, 외부 서버로 보내지 않음

### XSS 방지

- 사용자 입력을 `innerHTML`에 넣을 땐 반드시 `esc()` 통과
- `onclick="...(${...})"` 패턴에서는 학생명 등 임의 문자열 직접 삽입 금지 → 인덱스 전달 후 함수 내부에서 lookup

### 민감 스코프 안내

- 이 앱은 `forms.body` + `drive.file` 민감 스코프 사용
- 검증 전까지 OAuth 동의 화면은 **Testing 모드** + 테스트 사용자 수동 추가 (최대 100명)

---

## 🚀 배포

- 배포처: GitHub Pages (`main` 브랜치)
- 커밋 후 `git push origin main` 하면 1~2분 내 자동 반영
- 커밋 메시지는 한국어, 변경 요약 + 핵심 항목 bullet 형식
- Co-Author 라인은 사용자 정책에 따라 결정 (현재 추가 중)

---

## 📋 푸터 정보

```html
<div class="footer-line-1">선생님의 소중한 퇴근 시간을 응원합니다  |  jerry5026@gmail.com</div>
<div class="footer-line-2">마지막 업데이트 YYYY.MM.DD. · v1.x.y · 계속 다듬어 나가는 중입니다</div>
```

- 1행 (12px, #8a8f98): 응원 메시지 + 연락처
- 2행 (11px, #b5bac2): 업데이트 날짜 + 버전 + 부연 메시지
- 두 줄 모두 디자인 변경 없이 **값만** 수정

---

## 🗂 코드 구조 / 파일 맵

`index.html`은 약 1,950줄 단일 파일이며 다음 순서로 구성된다.

| 영역 | 대략 라인 | 내용 |
|---|---|---|
| `<head>` | 1~233 | 메타 + 외부 CDN 2개(Google GIS, ExcelJS) + 전체 CSS |
| **CSS 블록** | 9~232 | 디자인 토큰(`:root`), 컴포넌트별 스타일 |
| 로그인 화면 | 236~307 | `#login-screen` — 사용 요청 안내 + 자체 배포 안내 2단 아코디언 |
| 앱 셸 | 310~513 | 헤더·탭바·5개 탭 패널·푸터 |
| 탭1 질문 관리 | 330~358 | 질문 테이블 + 5개 버튼(추가·기본값·다운/업로드) |
| 탭2 폼 생성 | 360~391 | Google Form 생성 버튼 + 학생/교사 링크 |
| 탭3 학생 응답 | 393~416 | 학생 드롭다운 + 카테고리별 응답 카드 |
| 탭4 AI 상담 질문 | 418~472 | 모델 선택 + 학생 선택 + 4개 액션 버튼 + 결과 영역 |
| 탭5 AI 설정 | 474~505 | 제공사 카드 + 모델 리스트 + API 키 입력 |
| 푸터 | 509~512 | 응원 문구 + 버전·날짜 |
| 모달 영역 | 516~657 | 질문 추가/수정 모달 + 사용법 모달 + 로딩 + 토스트 |
| **JS 블록** | 663~1943 | 상태·인증·UI·기능 함수 |
| 설정·상수 | 666~775 | `CLIENT_ID`, `SCOPES`, `AI_PERSONA`, `PROVIDERS`, `CAT_DEFS`, `DEFAULT_QUESTIONS` |
| 상태·초기화 | 778~810 | 전역 변수 + `window.onload` |
| 인증 | 813~839 | `handleLogin`, `handleTokenResponse`, `handleLogout`, `ensureToken` |
| Google API 래퍼 | 844~853 | `apiFetch` |
| UI 헬퍼 | 858~892 | `showApp`, `switchTab`, `toggleSetup`, `toast`, `showLoading`, `esc`, `nl2br` |
| 질문 관리 | 897~993 | `renderQuestions`, 드래그앤드롭 5함수, `saveQuestion`, `resetToDefaults` 등 |
| 폼 생성 | 998~1017 | `handleCreateForm`, `updateFormPanel` |
| 응답 로드 | 1022~1067 | `loadResponses`, `populateDropdown`, `selectStudent`, `renderResponse` |
| AI 설정 탭 | 1072~1155 | `renderSettingsTab`, `selectProvider`, `selectModel`, `renderSummary` |
| API 키 검증 | 1160~1223 | `setKeyUI`, `onKeyInput`, `onKeyPaste`, `testApiKey` |
| AI 결과 화면 | 1228~1297 | `updateAIPanel` |
| AI 생성 | 1299~1389 | `generateAIQuestions`, `callOpenAI`, `callClaude`, `callGemini`, `copyAI` |
| 에러 한글화 | 1394~1417 | `translateApiError` (10가지 패턴 매칭) |
| 도움말 모달 | 1422~1423 | `openHelpModal`, `closeHelpModal` |
| AI 일괄 생성 | 1428~1499 | `generateAllAI` |
| AI 아코디언 | 1502~1547 | `renderBulkAIResults`, `toggleAIAcc`, `expandAllAIAcc` |
| AI 삭제 | 1549~1578 | `deleteAIForStudent`, `deleteAllAI` |
| 질문 템플릿 | 1583~1699 | `downloadQuestionTemplate`, `uploadQuestionTemplate` |
| 엑셀 다운로드 | 1706~1942 | `downloadExcelWithAI`, `downloadExcel` (파라미터 `includeAI`) |

> 💡 라인 번호는 변경 시 자연스럽게 어긋난다. 큰 작업 전엔 `Grep`으로 함수명을 찾아 정확한 위치 확인 권장.

---

## 💾 상태 관리

### 전역 변수 (in-memory)

```js
let tokenClient, accessToken, tokenExpiry;       // OAuth
let questions, formId, formStudentUrl, formEditUrl;  // 질문·폼
let allResponses, currentStudent, aiMemory;      // 응답·AI 결과
let editingIdx, dragSrcIdx;                      // 임시 UI 상태
let aiProvider, aiModel, keyTestTimer;           // AI 설정
let _responsesAutoLoaded;                        // 응답 자동 로드 가드
```

### `localStorage` 키 (영속)

| 키 | 값 | 용도 |
|---|---|---|
| `questions` | JSON 배열 | 현재 질문 목록 (편집 시마다 저장) |
| `my_default_questions` | JSON 배열 | 사용자가 "내 기본값으로 저장"한 목록 |
| `formId` | string | 생성된 Google Form ID |
| `formStudentUrl` | string | 학생 배포 링크 |
| `formEditUrl` | string | 교사 수정 링크 |
| `aiMemory` | JSON `{학생명: 질문텍스트}` | 학생별 AI 상담 질문 결과 |
| `ai_provider` | `'openai'`/`'claude'`/`'gemini'` | 선택된 AI 제공사 |
| `ai_model` | string | 선택된 모델 ID |
| `openai_key` | string | OpenAI API 키 (sk-...) |
| `anthropic_key` | string | Anthropic API 키 (sk-ant-...) |
| `gemini_key` | string | Gemini API 키 (AIza...) |

### `sessionStorage` 키 (탭 단위)

| 키 | 값 | 용도 |
|---|---|---|
| `access_token` | string | 현재 OAuth 액세스 토큰 |
| `token_expiry` | number(ms) | 토큰 만료 시각 |
| `user_email` | string | 로그인된 사용자 이메일 |

> **주의**: 새 기능 추가 시 새 localStorage 키를 도입하면 이 표에도 추가.

---

## 🔌 외부 의존성

### CDN 스크립트 (둘 다 `<head>`에서 로드)

| 라이브러리 | 버전 | URL | 용도 |
|---|---|---|---|
| Google Identity Services | 최신 | `https://accounts.google.com/gsi/client` | OAuth 로그인 |
| ExcelJS | 4.4.0 | `https://cdn.jsdelivr.net/npm/exceljs@4.4.0/dist/exceljs.min.js` | 엑셀 다운로드/업로드 |

### Google API (스코프)

```
https://www.googleapis.com/auth/forms.body    — 폼 생성·수정 (민감 스코프)
https://www.googleapis.com/auth/drive.file    — 앱이 만든 파일 접근 (민감 스코프)
openid, email                                 — 사용자 정보
```

### AI 제공사 엔드포인트

| 제공사 | 엔드포인트 | 인증 헤더 |
|---|---|---|
| OpenAI | `POST https://api.openai.com/v1/chat/completions` | `Authorization: Bearer {key}` |
| Claude | `POST https://api.anthropic.com/v1/messages` | `x-api-key`, `anthropic-version`, `anthropic-dangerous-direct-browser-access: true` |
| Gemini | `POST https://generativelanguage.googleapis.com/v1beta/models/{model}:generateContent?key={key}` | URL 쿼리 |

모두 `max_tokens / maxOutputTokens: 8000` 으로 통일 (한국어 30문항 여유).

---

## 🤖 AI 제공사 추가 절차

새 제공사를 추가하려면:

1. **`PROVIDERS` 객체에 항목 추가** (line ~678):
   ```js
   newProvider: {
     name, icon, sub, keyStorage, keyPlaceholder, keyHint,
     validateKeyFormat: k => /* 형식 검증 */,
     models: [{id, label, tier, desc}, ...]
   }
   ```
2. **`callNewProvider(prompt, model, key)` 함수 추가** (callOpenAI 등 옆에)
3. **`generateAIQuestions` / `generateAllAI`의 if-else 체인에 분기 추가**
4. **`testApiKey`의 if-else 체인에 키 검증용 ping 추가**
5. **버전: 마이너 (`1.x.0 → 1.(x+1).0`)**

---

## 📊 엑셀 출력 구조

`downloadExcel(includeAI)` 가 생성하는 워크북:

### 시트1: 학생조회

```
A          B (수식)            C  D (AI 포함 시만)
A1:B1 [📋 학생 기초조사 응답 조회]      ─
A3   B3 [학생 이름 입력칸]              ─
A4   B4 [10px 빈 행]                    ─
A5   B5 [카테고리 헤더]                  D5 [🤖 AI 상담 질문 타이틀]
A6   B6 [질문]  [INDEX/MATCH 응답]      D6 [AI 질문1 수식]
...                                     D7 [AI 질문2 수식]
                                        ...
```

- B열 수식: `IFERROR(INDEX('전체응답'!{col}:{col}, MATCH($B$3, '전체응답'!{nameCol}:{nameCol}, 0)), "")`
- D열 수식 (AI): `IF($B$3="", "", IFERROR(INDEX(...), ""))` — B3 비면 D열도 비움
- 카테고리: `CAT_DEFS` 정규식으로 자동 분류, 색상도 카테고리별 다름
- D열 너비: **120 고정**, 행 높이는 글자 길이 / 120 → 줄 수 × 22

### 시트2: 전체응답

```
A           B           C           ...   AI_Q1   AI_Q2   ...
제출 시간   이름        학번        ...   (학생별 AI 질문)
[ts]        김철수      1234       ...   1.질문   2.질문  ...
[ts]        이영희      5678       ...   1.질문   2.질문  ...
```

- 시간순 정렬 (`__submitTime__` ASC)
- 행 높이는 셀 내용 길이 기반 자동 계산
- AI 컬럼은 `includeAI=true` 일 때만 추가

---

## 🧭 자주 하는 작업 가이드

### 새 기본 질문 추가/수정

`DEFAULT_QUESTIONS` 배열(line ~735) 수정. 형식:
```js
{ title: '질문', options: [], multi: false, optional: false }
```
- `options` 비우면 서술형
- `multi: true` 는 체크박스(복수선택), 객관식인 경우만 의미 있음
- `optional: true` 는 응답 선택 사항

→ 버전: 패치 (작은 텍스트 변경) 또는 마이너 (질문 다수 추가)

### 새 카테고리 추가

`CAT_DEFS` 배열(line ~726)에 추가:
```js
{ key: '🆕 새카테고리', rx: /(키워드1|키워드2)/ }
```
- `📋 기타`(catch-all) 직전에 삽입해야 함
- 엑셀 색상도 `downloadExcel` 내 `catColors`에 추가

→ 버전: 마이너

### 새 탭 추가

1. `<nav class="app-tabs">` 에 `<button class="tab-btn" data-tab="newtab" onclick="switchTab('newtab',this)">` 추가
2. `<main>` 안에 `<div id="tab-newtab" class="tab-panel">...</div>` 추가
3. `switchTab(id)`에 필요시 분기 로직
4. 탭별 렌더 함수 추가

→ 버전: 마이너

### 새 모달 추가

`<div id="myModal" class="modal-overlay" style="display:none">...</div>` 형식. 표시·숨김 함수 짧게 작성:
```js
function openMyModal(){ document.getElementById('myModal').style.display='flex'; }
function closeMyModal(){ document.getElementById('myModal').style.display='none'; }
```

### 푸터 메시지 변경

line ~510~511만 수정. 1행은 12px·#8a8f98, 2행은 11px·#b5bac2 유지.

→ 버전: 패치

---

## 🧪 변경 후 검증 체크리스트

큰 변경 후 푸시 전에 확인:

- [ ] **중괄호 균형**: PowerShell `[regex]::Matches($t,'\{').Count` 와 `\}` 카운트 일치
- [ ] **새 함수 호출 위치**: `Grep`으로 함수명 검색 → 모든 호출 지점 정상 인자
- [ ] **localStorage 키**: 새 키 도입 시 이 문서 표에도 추가
- [ ] **푸터 버전·날짜**: 코드 수정이면 둘 다 갱신했는지
- [ ] **모바일 레이아웃**: `flex-wrap:wrap` 누락 없는지 (좁은 화면 깨짐 방지)
- [ ] **에러 처리**: 새 fetch 호출엔 try/catch + `translateApiError` 통과 권장
- [ ] **사용자 입력 표시**: `innerHTML` 사용처는 `esc()` 통과 확인
- [ ] **사용법 모달**: 새 기능 추가 시 도움말 섹션도 갱신

---

## 🚧 알려진 한계 / 개선 백로그

### 한계 (구조적)

- **100명 사용자 한도** — Google OAuth Testing 모드 정책. 검증 통과 전까지 풀리지 않음
- **7일 토큰 만료** — Testing 모드 한정. 검증 후 무기한
- **토큰 만료 시 매번 로그인 클릭 필요** — 자동 silent reauth 미구현 (구현 가능, 보류 중)
- **단일 폼만 관리** — `formId` 한 개만 저장. 학년·반 별 다중 폼 미지원

### 개선 후보

- [ ] 자동 silent reauth (`prompt: ''` 사용) — 같은 브라우저 재방문 시 자동 로그인
- [ ] 학년·반별 다중 폼 관리 — `formId`를 배열로 확장
- [ ] AI 질문 결과 편집 기능 — 생성된 질문 중 일부만 골라 저장
- [ ] PDF 출력 — 상담 시 인쇄용
- [ ] 다크 모드
- [ ] 학생별 메모 기능 — AI 질문 옆에 상담 후 메모 입력
- [ ] 학기 단위 데이터 보존 — 새 학기 시작 시 기존 데이터 아카이브
- [ ] 일괄 생성 중 일시정지·재개

### 이미 처리한 주요 이슈 (참고용)

- ✅ 개별 생성 후 버튼 라벨 잘못 복원되던 문제 (v1.0.0 직전 수정)
- ✅ AI 메모리 없는 학생 선택 시 stale state
- ✅ onclick 학생명 직접 삽입 → XSS 위험 → 인덱스 전달로 변경
- ✅ 응답 탭 자동 호출을 최초 1회로 제한

---

## 🔬 디버깅 팁

### 사용자가 "안 돼요" 라고 할 때 먼저 묻기

1. 어느 탭에서? 어떤 버튼?
2. 토스트 메시지 (빨간색·초록색) 내용?
3. 브라우저 콘솔 에러? (F12 → Console)
4. 어떤 AI 모델·제공사 사용 중?

### 자주 발생하는 문제

| 증상 | 원인 | 해결 |
|---|---|---|
| 로그인 후 즉시 로그아웃 | 테스트 사용자 미등록 | Google Cloud Console → OAuth 동의 화면 → 테스트 사용자 추가 |
| `ExcelJS undefined` 에러 | CDN 로딩 실패 | 네트워크 확인, CDN URL 변경 가능성 |
| AI 응답이 도중에 잘림 | `max_tokens` 부족 | callOpenAI/Claude/Gemini 의 토큰 값 확인 (현재 8000) |
| 응답 탭에서 학생이 안 보임 | 폼 응답 0건 또는 API 권한 문제 | `loadResponses` 토스트 메시지 확인 |
| 모델 not found | Gemini 모델 ID 변경 | `PROVIDERS.gemini.models` ID 최신화 |

---

## 📝 변경 로그 (선택 운영)

향후 필요하면 이 파일 하단 또는 별도 `CHANGELOG.md`에 변경 내역을 누적 기록.

```
v1.0.0 (2026.04.24.) — 최초 배포 베이스라인
  · 기초조사 39개 질문, Google Form 자동 생성, 응답 카테고리화
  · OpenAI·Claude·Gemini 9개 모델 지원, AI 상담 질문 개별/일괄 생성
  · ExcelJS 기반 엑셀 다운로드 2종 (응답만 / AI 포함)
  · 질문 엑셀 템플릿 다운/업로드, 드래그앤드롭 순서 변경
  · 사용 요청 안내 + 사용법 모달 + 푸터
```
