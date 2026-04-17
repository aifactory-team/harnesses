# AIFactory Harnesses

Claude Code 에이전트 팀 하네스 컬렉션.

도메인별 전문 에이전트 팀을 정의하고, 각 에이전트가 사용할 스킬을 포함하여 복잡한 작업을 체계적으로 수행할 수 있도록 구성된 하네스 패키지입니다.

## Harnesses

| 하네스 | 설명 | 에이전트 | 스킬 | 의존스킬 |
|--------|------|---------|------|---------|
| [web-qa-test](./web-qa-test/) | 웹사이트 풀 QA 테스트 (크롤링, UI/UX, 접근성, SEO, 성능, 콘텐츠) | 3 | 1 | 1 |

## 사용법

### 1. 하네스 설치

원하는 하네스의 파일들을 프로젝트의 `.claude/` 디렉토리에 복사합니다.

```bash
# 예: web-qa-test 하네스 설치
cp -r web-qa-test/agents/* <your-project>/.claude/agents/
cp -r web-qa-test/skills/* <your-project>/.claude/skills/
```

### 2. 실행

Claude Code에서 해당 하네스의 트리거 키워드를 사용합니다.

```
# web-qa-test 예시
> https://example.com 을 풀 테스트해줘
> 웹사이트 QA 테스트 실행해
```

## 하네스 구조

각 하네스는 다음 구조를 따릅니다:

```
harness-name/
├── README.md              # 하네스 설명, 사용법
├── agents/                # 에이전트 정의 파일
│   ├── agent-1.md
│   ├── agent-2.md
│   └── ...
└── skills/                # 스킬 파일
    └── skill-name/
        └── skill.md       # 오케스트레이터 스킬
```

## 기여

새로운 하네스를 추가하려면:

1. 하네스 디렉토리 생성 (`harness-name/`)
2. 에이전트 정의 파일 추가 (`agents/*.md`)
3. 오케스트레이터 스킬 추가 (`skills/skill-name/skill.md`)
4. 하네스 README.md 작성
5. 이 파일의 Harnesses 테이블 업데이트

## Acknowledgments

이 리포지토리의 하네스들은 [revfactory harness](https://github.com/revfactory/harness) 프레임워크를 기반으로 설계되었습니다. revfactory harness는 [Minho Hwang(@revfactory)](https://github.com/revfactory)가 개발한 Claude Code 에이전트 팀 하네스 아키텍처로, 도메인별 전문 에이전트 팀을 자동 설계하고 6가지 아키텍처 패턴(파이프라인, 팬아웃/팬인, 전문가 풀, 생성-검증, 감독자, 계층적 위임)과 Progressive Disclosure 기반 스킬 관리를 제공합니다. 통제 실험에서 LLM 코드 에이전트 출력 품질을 평균 60% 향상시킨 것으로 보고되었습니다.

- [revfactory/harness](https://github.com/revfactory/harness) — Harness 메타 스킬
- [revfactory/harness-100](https://github.com/revfactory/harness-100) — 10개 도메인 100개 프로덕션 레디 하네스
- [Harness 공식 문서](https://revfactory.github.io/harness/)

## License

MIT
