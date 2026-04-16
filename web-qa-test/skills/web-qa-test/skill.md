---
name: web-qa-test
description: "웹사이트 풀 QA 테스트 오케스트레이터. cheliped-browser 기반으로 사이트 크롤링, UI/UX 테스트, 접근성, 성능, SEO, 콘텐츠 품질을 종합 검증하고 이슈 리포트를 생성한다. '웹사이트 테스트', 'QA 테스트', '사이트 검증', '웹 풀테스트', 'full test', 'web QA' 등을 요청하면 이 스킬을 사용할 것."
---

# Web QA Test Orchestrator

웹사이트를 종합적으로 테스트하여 문제점을 발견하고 구조화된 이슈 리포트를 생성하는 에이전트 팀 오케스트레이터.

## 실행 모드: 에이전트 팀

## 에이전트 구성

| 팀원 | 에이전트 정의 | 역할 | 출력 |
|------|-------------|------|------|
| site-scanner | `.claude/agents/site-scanner.md` | 사이트 크롤링, 링크 수집, 리소스 매핑 | `_workspace/01_scanner_*.md` |
| browser-tester | `.claude/agents/browser-tester.md` | UI/UX, 반응형, 인터랙션 테스트 | `_workspace/02_browser_*.md` |
| validator | `.claude/agents/validator.md` | 접근성, 성능, SEO, 콘텐츠 검증 | `_workspace/03_validator_*.md` |

## 핵심 도구: cheliped-browser

모든 팀원은 cheliped-browser 스킬을 사용하여 실제 브라우저에서 페이지를 탐색한다. cheliped-browser는 Chrome DevTools Protocol 기반의 브라우저 자동화 도구로, 페이지 이동, 스크린샷, 클릭, 폼 입력, JavaScript 실행 등이 가능하다.

## 워크플로우

### Phase 1: 준비

1. 사용자가 제공한 대상 URL 확인
2. `_workspace/` 디렉토리 생성
3. 대상 URL을 `_workspace/00_input/target.md`에 저장

### Phase 2: 팀 구성

1. 팀 생성:
   ```
   TeamCreate(
     team_name: "web-qa-team",
     description: "웹사이트 QA 테스트 팀"
   )
   ```

2. 팀원 스폰:
   ```
   Agent(name: "site-scanner", team_name: "web-qa-team", subagent_type: "general-purpose",
     prompt: ".claude/agents/site-scanner.md 정의를 읽고, cheliped-browser를 사용하여 {TARGET_URL}을 크롤링하라. 모든 페이지, 링크, 리소스를 수집하고 결과를 _workspace/01_scanner_*.md 파일에 저장하라.")
   
   Agent(name: "browser-tester", team_name: "web-qa-team", subagent_type: "general-purpose",
     prompt: ".claude/agents/browser-tester.md 정의를 읽고, site-scanner의 사이트맵이 준비되면 cheliped-browser로 각 페이지의 UI/UX를 테스트하라. 사이트맵이 없으면 직접 {TARGET_URL}부터 탐색을 시작하라.")
   
   Agent(name: "validator", team_name: "web-qa-team", subagent_type: "general-purpose",
     prompt: ".claude/agents/validator.md 정의를 읽고, cheliped-browser로 {TARGET_URL}의 접근성, 성능, SEO, 콘텐츠 품질을 검증하라. site-scanner의 결과를 활용하되, 없으면 직접 탐색하라.")
   ```

3. 작업 등록:
   ```
   TaskCreate(tasks: [
     { title: "사이트 크롤링", description: "전체 페이지 맵 생성, 링크 수집", assignee: "site-scanner" },
     { title: "링크 유효성 검사", description: "모든 내부/외부 링크의 HTTP 상태 확인", assignee: "site-scanner" },
     { title: "리소스 인벤토리", description: "이미지, CSS, JS 등 정적 리소스 목록", assignee: "site-scanner" },
     { title: "시각적 검증", description: "레이아웃 깨짐, 오버플로우, 이미지 깨짐 확인", assignee: "browser-tester" },
     { title: "반응형 테스트", description: "모바일/태블릿/데스크탑 뷰포트별 레이아웃", assignee: "browser-tester" },
     { title: "인터랙션 테스트", description: "네비게이션, 버튼, 폼 등 동작 확인", assignee: "browser-tester" },
     { title: "접근성 검증", description: "WCAG 2.1 AA 기준 검증", assignee: "validator" },
     { title: "SEO 검증", description: "메타 태그, OG 태그, 시맨틱 HTML", assignee: "validator" },
     { title: "콘텐츠 품질 검증", description: "날짜, 오탈자, 누락 정보 확인", assignee: "validator" },
     { title: "성능 분석", description: "로딩 시간, 리소스 크기 분석", assignee: "validator" },
   ])
   ```

### Phase 3: 테스트 실행

**실행 방식:** 팬아웃 — 3개 팀원이 병렬 작업

site-scanner가 사이트맵을 가장 먼저 완성하는 것이 이상적이나, browser-tester와 validator도 대상 URL부터 독립적으로 작업을 시작할 수 있다.

**팀원 간 통신 규칙:**
- site-scanner는 사이트맵 완성 시 browser-tester와 validator에게 SendMessage
- browser-tester는 심각한 시각적 결함 발견 시 validator에게 접근성 교차 검증 요청
- validator는 콘텐츠 오류 발견 시 리더에게 보고

**산출물 저장:**

| 팀원 | 출력 경로 |
|------|----------|
| site-scanner | `_workspace/01_scanner_sitemap.md`, `01_scanner_links.md`, `01_scanner_resources.md`, `01_scanner_errors.md` |
| browser-tester | `_workspace/02_browser_visual.md`, `02_browser_responsive.md`, `02_browser_interaction.md`, `02_browser_console.md` |
| validator | `_workspace/03_validator_accessibility.md`, `03_validator_performance.md`, `03_validator_seo.md`, `03_validator_content.md` |

### Phase 4: 결과 통합 및 이슈 리포트

1. 모든 팀원의 작업 완료 대기 (TaskGet으로 상태 확인)
2. `_workspace/` 내 모든 산출물을 Read로 수집
3. 발견된 문제를 심각도별로 분류:

   | 심각도 | 기준 | 예시 |
   |--------|------|------|
   | **Critical** | 핵심 기능 불가 | 등록 링크 깨짐, 페이지 로드 실패 |
   | **Major** | 사용성 저하 | 모바일 레이아웃 깨짐, 주요 정보 누락 |
   | **Minor** | 품질 개선 필요 | 오탈자, alt 텍스트 누락, 성능 최적화 |
   | **Info** | 권고 사항 | SEO 개선, 코드 정리 |

4. 최종 이슈 리포트 생성: `_workspace/04_qa_report.md`

   리포트 형식:
   ```markdown
   # QA 테스트 리포트: {사이트 이름}
   
   - 테스트 일시: {날짜}
   - 대상 URL: {URL}
   - 발견된 이슈: {총 N건} (Critical: n, Major: n, Minor: n, Info: n)
   
   ## Critical Issues
   ### [C-001] {이슈 제목}
   - **카테고리:** {링크/UI/접근성/성능/콘텐츠/SEO}
   - **발견 위치:** {URL 또는 페이지명}
   - **설명:** {구체적 설명}
   - **재현 방법:** {단계별}
   - **스크린샷:** {경로, 있는 경우}
   - **권장 수정:** {해결 방안}
   
   ## Major Issues
   ...
   
   ## Minor Issues
   ...
   
   ## Info
   ...
   
   ## 테스트 커버리지 요약
   | 영역 | 테스트 항목 수 | 통과 | 실패 | 미확인 |
   |------|-------------|------|------|--------|
   | 링크 검증 | | | | |
   | UI/시각적 | | | | |
   | 반응형 | | | | |
   | 접근성 | | | | |
   | 성능 | | | | |
   | SEO | | | | |
   | 콘텐츠 | | | | |
   ```

### Phase 5: 정리

1. 팀원들에게 종료 요청 (SendMessage: shutdown_request)
2. `_workspace/` 디렉토리 보존
3. 사용자에게 최종 리포트 경로와 요약 보고

## 데이터 흐름

```
[리더] → TeamCreate → [site-scanner] ──사이트맵──→ [browser-tester]
                          │                              │
                          │──사이트맵+링크──→ [validator] ←─시각결함─┘
                          │                      │
                          ↓                      ↓
                   01_scanner_*.md         02_browser_*.md
                                           03_validator_*.md
                          │                      │
                          └───────── Read ────────┘
                                     ↓
                              [리더: 통합]
                                     ↓
                           04_qa_report.md
```

## 에러 핸들링

| 상황 | 전략 |
|------|------|
| 팀원 1명 실패 | 리더가 감지 → 재시작 시도 → 실패 시 해당 영역을 "미검증"으로 표시 |
| cheliped-browser 연결 실패 | WebFetch로 대체하여 기본 HTML 분석 수행 |
| 대상 사이트 다운 | 사용자에게 알리고 중단 |
| 팀원 간 데이터 충돌 | 출처 명시 후 병기 |
| 크롤링 과다 (100+ 페이지) | 주요 페이지 20개로 한정하고 나머지는 링크 검증만 수행 |

## 테스트 시나리오

### 정상 흐름
1. 사용자가 `https://events.aifactory.page/2026AICSCK` URL 제공
2. Phase 1에서 _workspace/ 생성, URL 저장
3. Phase 2에서 3명 팀원 + 10개 작업 등록
4. Phase 3에서 병렬 테스트 실행, 팀원 간 사이트맵 공유
5. Phase 4에서 12개 산출물 통합 → 04_qa_report.md 생성
6. Phase 5에서 팀 정리, 사용자에게 리포트 전달

### 에러 흐름
1. Phase 3에서 browser-tester가 cheliped-browser 연결 실패
2. 리더가 유휴 알림 수신
3. browser-tester에게 WebFetch 기반 대체 테스트 지시
4. site-scanner와 validator 결과는 정상 수집
5. 최종 리포트에 "UI/시각적 테스트: WebFetch 대체 — 스크린샷 미포함" 명시
