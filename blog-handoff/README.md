# 지원포유 블로그 작업 패키지

지금까지 조사한 글감 49개와 근거 자료를 VS Code(클로드 코드)에서 바로 쓸 수 있게 한 폴더로 모은 것입니다.

- 만든 날: 2026-10-29
- 대상 블로그: https://www.jiwon4u.kr

## 내 컴퓨터로 가져오기

깃허브 저장소 `soeunab/trading`의 `ccr-f4685b94-4fpr2i` 브랜치에 있습니다.

```bash
git clone -b ccr-f4685b94-4fpr2i https://github.com/soeunab/trading.git
code trading/blog-handoff
```

이미 받아 둔 저장소라면:

```bash
git fetch origin ccr-f4685b94-4fpr2i
git checkout ccr-f4685b94-4fpr2i
code blog-handoff
```

VS Code에서 `blog-handoff` 폴더를 연 뒤 클로드 코드를 실행하면 `CLAUDE.md`의 작성 규칙을 자동으로 읽습니다. 바로 쓸 지시문은 `prompts/README.md`에 있습니다.

## 들어 있는 것

```
blog-handoff/
├── CLAUDE.md                 클로드 코드 작업 규칙 (자동으로 읽힘)
├── README.md                 이 파일
├── topics.md                 글감 49개 표 (사람용)
├── topics.json               글감 49개 (자동화 프로그램·클로드 코드용)
├── research/                 주제별 조사 자료와 출처
│   ├── 01-photoshop-free.md          포토샵 무료 주장, 무료 이미지 도구
│   ├── 02-ai-video-tools.md          무료 AI 영상 도구 한도, 힉스필드, 리모션, 하이퍼프레임
│   ├── 03-claude-skills.md           추천 스킬 검증, 스킬 사이트, watch 스킬, 보안 체크리스트
│   ├── 04-search-console-indexing.md 지원포유 색인 점검·리디렉션 테스트 데이터
│   ├── 05-rea.md                     REA 정리
│   └── 06-zeta-webtoon-funnel-law.md 제타AI, AI 웹툰 저작권, 유입 구조, AI기본법
├── data/
│   ├── jiwon4u-posts.csv     발행한 글 38편 (내부 링크·중복 확인용)
│   └── youtube-60-videos.csv 확인한 유튜브 영상 60편
├── drafts/                   원고 초안 3편
│   ├── 2026-10-meeting-notes-ai-claude.md
│   ├── 2026-10-photoshop-free-fact-check.md
│   └── 2026-10-free-ai-video-limits.md
├── templates/post-template.md 원고 틀
└── prompts/README.md          클로드 코드에 붙여넣을 지시문 6개
```

## 글감 상태 (49개)

| 상태 | 개수 | 뜻 |
| --- | --- | --- |
| 초안 완료 | 3 | 체크리스트 확인 후 발행 |
| 바로 작성 가능 | 16 | 자료가 충분함 |
| 작성 전 공식 확인 필요 | 11 | 요금·기능을 당일 확인 |
| 직접 해 본 결과 필요 | 9 | 실습 후 작성 |
| 프롬프트 설계로 작성 | 10 | 업무별 프롬프트 글. 예시는 가상, 캡처는 직접 |

우선 추천: T42 아트크래프트 → T43 추천 스킬 6개 확인 → T44 스킬 보안 체크리스트 → T18 서치콘솔 상태 뜻 → T17 블로거 리디렉션 테스트.

## 블로그 자동화 프로그램에서 쓰기

`topics.json` 구조:

```json
{
  "id": "T42",
  "pillar": "C", "pillar_name": "비교·선택",
  "status": "ready", "status_name": "바로 작성 가능",
  "title": "아트크래프트(ArtCraft)란? ...",
  "slug": "",
  "keywords": ["아트크래프트", "ArtCraft"],
  "research": ["research/01-photoshop-free.md"],
  "internal_links": [{"title": "...", "url": "https://www.jiwon4u.kr/..."}],
  "draft": null,
  "notes": "..."
}
```

## 함께 보면 좋은 문서 (저장소 `docs/`)

- `docs/jiwon4u-content-plan.md` 콘텐츠 기획안(포지셔닝, 비율, 허브 페이지, 발행표)
- `docs/shorts-tools-review.md` 영상 60편 확인 결과 전체
- `docs/zeta-ai-*.md`, `docs/lazyowen-analysis.md`, `docs/ai-matching-service-plan.md` 분석·기획 원문

## 주의

- 가격·무료 한도는 2026년 10월 조사 기준입니다. 발행 전에 공식 페이지를 다시 확인하세요.
- 조사 자료의 일부 수치(별 수, 2차 출처 요금)는 바뀌기 쉬우니 원고에 단정하지 마세요.
