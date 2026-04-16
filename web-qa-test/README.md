# web-qa-test

웹사이트 풀 QA 테스트 하네스. cheliped-browser 기반으로 사이트 크롤링, UI/UX 테스트, 접근성, 성능, SEO, 콘텐츠 품질을 종합 검증하고 이슈 리포트를 생성합니다.

## 아키텍처

- **실행 모드**: 에이전트 팀 (팬아웃/팬인)
- **팀원 수**: 3명 (병렬 실행)
- **핵심 도구**: cheliped-browser (Chrome DevTools Protocol)

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

## 에이전트

| 에이전트 | 역할 | 출력 |
|---------|------|------|
| **site-scanner** | 사이트 크롤링, 링크 수집, 리소스 매핑, 깨진 링크 탐지 | `_workspace/01_scanner_*.md` |
| **browser-tester** | 시각적 검증, 반응형 테스트, 인터랙션 테스트, 콘솔 에러 수집 | `_workspace/02_browser_*.md` |
| **validator** | 접근성(WCAG 2.1), SEO, 콘텐츠 품질, 성능, 보안 검증 | `_workspace/03_validator_*.md` |

## 테스트 영역

| 영역 | 검증 항목 |
|------|----------|
| **링크 검증** | 내부/외부 링크 HTTP 상태, 깨진 링크, 리다이렉트 |
| **UI/시각적** | 이미지 깨짐, 텍스트 오버플로우, 요소 겹침, 폰트 렌더링 |
| **반응형** | 모바일(375px), 태블릿(768px), 데스크탑(1440px) 레이아웃 |
| **인터랙션** | 네비게이션, 버튼, 폼, 모달, 탭, 아코디언 |
| **접근성** | alt 텍스트, heading 계층, ARIA, skip link, 키보드 접근성 |
| **SEO** | meta 태그, OG 태그, canonical, JSON-LD, 시맨틱 HTML |
| **성능** | 이미지 최적화, lazy loading, 렌더링 차단 리소스 |
| **콘텐츠** | 오탈자, 날짜 정확성, 정보 완결성, 수치 일관성 |
| **보안** | HTTPS, 혼합 콘텐츠, 외부 스크립트 |

## 사용법

### 설치

```bash
cp -r agents/* <your-project>/.claude/agents/
cp -r skills/* <your-project>/.claude/skills/
```

### 실행

```
> https://example.com 을 풀 테스트해줘
> 웹사이트 QA 테스트 실행해
> web QA test https://example.com
```

### 트리거 키워드

`웹사이트 테스트`, `QA 테스트`, `사이트 검증`, `웹 풀테스트`, `full test`, `web QA`

## 산출물

| 파일 | 내용 |
|------|------|
| `_workspace/01_scanner_sitemap.md` | 전체 사이트맵 |
| `_workspace/01_scanner_links.md` | 링크 검증 결과 |
| `_workspace/01_scanner_resources.md` | 정적 리소스 목록 |
| `_workspace/01_scanner_errors.md` | 크롤링 에러 |
| `_workspace/02_browser_visual.md` | 시각적 결함 |
| `_workspace/02_browser_responsive.md` | 반응형 테스트 |
| `_workspace/02_browser_interaction.md` | 인터랙션 테스트 |
| `_workspace/02_browser_console.md` | JS 콘솔 에러 |
| `_workspace/03_validator_accessibility.md` | 접근성 검증 |
| `_workspace/03_validator_seo.md` | SEO 검증 |
| `_workspace/03_validator_content.md` | 콘텐츠 품질 |
| `_workspace/03_validator_performance.md` | 성능/보안 분석 |
| `_workspace/04_qa_report.md` | **통합 QA 리포트** |

## 이슈 심각도 분류

| 심각도 | 기준 | 예시 |
|--------|------|------|
| **Critical** | 핵심 기능 불가 | 등록 링크 깨짐, 페이지 로드 실패 |
| **Major** | 사용성 저하 | 모바일 레이아웃 깨짐, 주요 정보 누락 |
| **Minor** | 품질 개선 필요 | 오탈자, alt 텍스트 누락, 성능 최적화 |
| **Info** | 권고 사항 | SEO 개선, 코드 정리 |

## 의존성

- [cheliped-browser](https://github.com/anthropics/claude-code) — Chrome DevTools Protocol 기반 브라우저 자동화

## 실전 적용 사례

2026 ASK Conference & Awards 웹사이트(`https://events.aifactory.page/2026AICSCK`) 풀 테스트에서 73개 항목을 검증하여 35건의 이슈(Critical 7, Major 11, Minor 13, Info 4)를 발견했습니다.
