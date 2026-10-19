# 제타(zeta) `/ko/discover` 분석 보고서

- 대상: https://zeta-ai.io/ko/discover
- 분석일: 2026-10-19
- 방법: [REA (Reverse Engineer Anything)](https://github.com/morluto/rea)의 웹 분석 흐름
  (`inspect_web_page` → `analyze_web_bundle` → `export_web_scripts`)을 따랐다.
  분석 환경에서 `rea-agents` npm 패키지 실행 권한이 막혀 있어서, 같은 단계를 curl과 Chromium(headless)으로 직접 수행했다.
- 범위: 누구나 받을 수 있는 공개 자산(HTML, JS 번들, robots.txt, manifest)과,
  페이지가 비로그인 상태에서 직접 호출하는 읽기 전용 API 1건(`GET /v1/discovery-tab`)만 봤다.
  로그인, 결제, 내부 경로 접근이나 쓰기 요청은 하지 않았다.

---

## 1. 한 줄 요약

제타는 스캐터랩(Scatter Lab, '이루다' 개발사)이 만든 **AI 캐릭터 스토리 채팅 서비스**다.
웹은 **Next.js 16.4 (App Router, Turbopack) + React 19.3 canary**로 만들었고 **Vercel**에서 서비스한다.
디스커버 탭의 내용은 서버(`api.zeta-ai.io`)가 정해 주는 **섹션 목록(서버 주도 UI)**으로 채워진다.
콘텐츠 단위는 "캐릭터"보다 **"플롯(Plot)"**, 곧 캐릭터 여럿과 세계관을 묶은 스토리다.

## 2. 서비스 정체성 (메타데이터)

| 항목 | 값 |
| --- | --- |
| 설명 문구 | "대한민국 1등 원조 AI 채팅 맛집 제타(zeta). 다른 덴 유료, 이 집은 무료." |
| 키워드 | 제타, 이루다, 스캐터랩, 강다온, 허세중, 너티(Nutty), CharacterAI, c.ai, talkie, chai, janitor 등 |
| 지원 언어 | ko-KR, ja-JP, en-US/GB/CA/AU/NZ |
| 앱 | iOS App Store ID `1619030760`, Android 패키지 `com.scatterlab.messenger`, 딥링크 `zeta://` |
| 웹 버전 | Sentry 릴리스 태그 `zeta-web@3.50.98` |

키워드에 경쟁 서비스(Character.AI, Talkie, Chai, Janitor AI) 이름을 넣어 검색 유입을 노린다.
"너티(Nutty)"와 `/nutty-migration` 경로가 있는 것으로 보아, 스캐터랩의 다른 앱(너티) 사용자를 제타로 옮기는 작업이 진행 중이다.

## 3. 기술 스택

| 계층 | 사용 기술 | 근거 |
| --- | --- | --- |
| 프레임워크 | Next.js **16.4.0**, App Router, Turbopack 빌드 | `window.next={version:"16.4.0",appDir:!0}`, `turbopack-*.js` 청크 |
| UI 런타임 | React **19.3.0-canary** | 번들 문자열 |
| 호스팅 | Vercel (`iad1` 리전 응답) | `server: Vercel`, `x-vercel-id` |
| 정적 자산 CDN | `web-cdn.zeta-ai.io`, 이미지는 `image.zeta-ai.io` | `<script src>`, preconnect |
| API | `https://api.zeta-ai.io` (REST, axios 인스턴스) | `baseURL:"https://api.zeta-ai.io"` |
| 상태 관리 | zustand(persist 미들웨어), `QueryClient`(React Query 계열로 추정) | 번들 문자열 |
| 폰트 | Pretendard Variable (cdnjs, jsdelivr) | `<link>` |
| 오류 모니터링 | Sentry (샘플링 0.1%, `/monitoring` 터널) | `sentry-*` 메타, `_sentryRewritesTunnelPath` |
| 분석·마케팅 | Firebase Analytics, Amplitude, Airbridge(앱 어트리뷰션), Meta Pixel(`fbq`), Microsoft Clarity, Google Ad Manager(`securepubads`) | 호스트와 함수명 |
| 소셜 로그인 | Kakao JS SDK 2.6.0, Apple, Google, LINE | `/auth/{kakao,apple,google,line}` 경로, SDK preload |
| 결제 | Toss Payments, Toss 브랜드페이, CodaPay(해외), App Store, Play Store, 바우처 | 결제수단 enum |

### API 클라이언트 동작

- 모든 요청에 붙는 헤더: `X-Client-Type: web`, `X-Client-Version`, `X-User-Language`, `X-Device-Type`
- 인증: `Authorization: Bearer <token>`. 401이 오면 인터셉터가 토큰을 갱신하고 같은 요청을 다시 보낸다.
- **처음 방문할 때 로그인 없이도** Next.js 미들웨어가 익명 JWT(`TOKEN`, `"ano":true`)와 `REFRESH_TOKEN`, `DEVICE_ID` 쿠키를 발급한다.
  그래서 비회원도 바로 탐색하고 대화를 시작할 수 있다.
- 접속 위치(IP)에 따라 언어가 정해진다. 미국 IP로 `/ko/discover`에 가면 `/en/discover`로 307 리다이렉트되고, `Accept-Language: ko`를 보내면 한국어 페이지가 나온다.

## 4. 디스커버 페이지 구조

### 4.1 렌더링 방식

- 서버가 내려주는 HTML에는 하단 탭(홈, 발견, 만들기, 대화, 마이페이지)과 **스켈레톤 카드**만 있다.
- 실제 섹션은 브라우저가 `GET /v1/discovery-tab`을 호출해 받아 그린다(CSR).
  `GET /v1/discovery-tab/latest-updated-at`으로 바뀐 내용이 있는지 확인하는 엔드포인트도 따로 있다.
- 레이아웃은 모바일 앱처럼 화면 가운데에 좁은 세로 컬럼 하나를 두고, 다크 테마(`#151516`)를 쓴다.

### 4.2 서버 주도 섹션 (2026-10-19 기준 응답)

`sections[]` 배열의 `type` 값에 따라 클라이언트가 어떤 컴포넌트로 그릴지 고른다.

| 순서 | type | 제목 | 내용 |
| --- | --- | --- | --- |
| 1 | `SmallProfile` | 🎃 Happy Halloween! | 시즌 기획전 플롯 10개 |
| 2 | `SmallProfile` | 🔥 갓 출시된 따끈따끈한 플롯 | 신작 플롯 10개 |
| 3 | `SmallProfile` | ✈️ 방금 상륙한 바다 건너 신작 | 번역된 해외 플롯 10개 |
| 4 | `CreatorRow` | ✨ 지금 꼭 팔로우해야 할 크리에이터 | 크리에이터 10명(팔로워 수, 대표 플롯) |
| 5 | `FeedPreviewList` | 💬 시끌벅적! 공감 200% 피드 | 크리에이터 피드 글 10개(좋아요, 댓글) |

섹션마다 `id`, `updatedAt`, `title`, `subtitle`, `highlightText` 필드가 있어 운영자가 앱 배포 없이 큐레이션을 바꿀 수 있다.
응답에 `requestId`가 들어 있어 노출 로그나 A/B 실험(`/v1/ab-experiments/*`)과 연결하는 것으로 보인다.

### 4.3 플롯(Plot) 데이터 모델

```text
Plot {
  id, name, imageUrl, shortDescription,
  hashtags[],                 # 장르·관계 태그 (로판, 혐관, bl/nl/hl/gl, 2026Halloween …)
  characters[], firstCharacterName,
  creator { id, nickname, username, profileImageUrl, badge },
  interactionCount, interactionCountWithRegen,   # 대화 수 (재생성 포함 여부로 구분)
  mode,                       # 이번 응답은 30개 모두 STORY_CHAT
  plugins[],                  # 플롯별로 켜는 기능 모듈
  supportedFeatures[],        # CHAT_SNAPSHOT_IMAGE, HALLOWEEN_PLUGIN_SKIN_2026
  isTranslatedEdition, verified, contestCategory,
  releasedAt, updatedAt, publishedVersion
}
```

디스커버에 노출된 30개 플롯 통계:

- 대화 수(`interactionCount`): 최소 약 1.5천, 중앙값 약 4.6만, 최대 약 76만
- 번역판(`isTranslatedEdition`): 16개 / 원작: 14개. 해외 신작을 번역해 들여오는 비중이 크다.
- **플러그인 사용 빈도**: `character-diary`(21) · `zetagram`(14) · `message`(14) · `phone-peek`(14) · `dream`(13) · `cartoonify`(12) · `ending`(11) · `ghost-chat`(10) · `anonymous-board`(8) · `relationship-chart`(4) · `quest` · `clock` · `bgm`(각 3) · `event-cut` · `dice` · `sudden_event`(각 1)

플러그인을 보면 제타가 단순 1:1 채팅이 아니라 **채팅 위에 게임과 SNS 요소를 얹은 구조**라는 걸 알 수 있다.
캐릭터 일기, 가상 인스타(zetagram), 캐릭터 폰 훔쳐보기, 꿈, 대화 구간 만화화(cartoonify, 유료·출력 형식은 미확인), 엔딩, 익명 게시판, 관계도, 퀘스트, 주사위 같은 기능이 있다.
플러그인 목록, 아이콘, 매니페스트는 `/v1/plugins`, `/v1/plugins/:pluginId/manifest`로 따로 받아 오므로, 플러그인을 하나의 확장 시스템으로 운영하는 것으로 보인다.

## 5. 클라이언트 번들에서 찾은 API 표면

정적 번들 51개(약 2.4MB)에서 찾은 REST 경로다. 디스커버와 공통 청크에 들어 있는 것만이며, 채팅이나 결제 화면 청크는 따로 로드되므로 여기 없다.

| 도메인 | 대표 엔드포인트 |
| --- | --- |
| 디스커버·추천 | `/v1/discovery-tab`, `/v1/infinite-plots{,/tabs,/curations}`, `/v1/plots/ranking`, `/v1/plots/random-recently-created`, `/v1/plots/prerelease/recent` |
| 플롯 | `/v1/plots/:plotId{,/viewer,/status,/similar-plots,/language-editions,/plugins,/scraps}` |
| 검색 | `/v1/plots/search`, `/v2/plots/search`, `/search/autocomplete`, `/search/recommended-placeholder` |
| 대화방 | `/v1/rooms`, `/v1/rooms/:roomId{,/clone,/pin}`, `/v1/rooms/active-room-id`, `/v1/rooms/purge` |
| 채팅 프로필(페르소나) | `/v1/user-chat-profiles`, `/:id/selected` |
| 댓글 | `/v1/plots/:plotId/comments/*` (좋아요, 답글, 신고, **번역**) |
| 크리에이터 | `/v1/creators/:creatorId/{follow,followers,followings,stats,feed-posts}` |
| 크리에이터 피드 | `/v1/creator-feed/posts/*` (투표 `poll/votes`, 고정, 번역, 신고) |
| 운영 도구 | `/v1/feature-flags/:key`, `/v1/ab-experiments/*`, `/v1/daily-quizzes` |
| 기타 | `/v1/app-push/settings`, `/v1/users/{me,blocks,logout}` |

## 6. 전체 화면 구성 (라우트 매니페스트)

Sentry가 번들에 넣어 둔 라우트 목록에서 서비스 전체 구조를 볼 수 있다. 주요 경로를 기능별로 묶었다.

- **탐색**: `/discover`, `/ranking`, `/recommend`, `/new-plots`, `/search`, `/plots/hashtags`, `/character-pools`, `/plot-pools`
- **대화**: `/rooms`, `/rooms/:roomId`, `/rooms/:roomId/bookmarks`, `/plots/:plotId/saved-rooms`, `/plots/:plotId/snapshots`
- **창작**: `/plots/create`, `/plots/assistant`(AI 창작 도우미), `/plots/:plotId/edit`, `/lorebooks/:lorebookId`, `/creation-stamp`
- **크리에이터 도구**: `/creator-center`, `/dashboard/:plotId/{exposure,click-conversion,play-conversion,success-conversion}`
  - 크리에이터에게 노출 → 클릭 → 플레이 → 성공으로 이어지는 **전환 퍼널**을 보여 준다.
- **커뮤니티**: `/feed/posts/*`, `/creators/:creatorId`, `/plot-comment-activities`
- **수익화**: `/stores`, `/piece/{charge,auto-charge,history}`(유료 재화 "피스"), `/my-page/subscription`(구독 "PASS"), `/usage-vouchers`, `/redeem-code`, `/purchase/{success,fail}`
- **이벤트·실험**: `/events/:eventId`, `/halloween-4cut`(할로윈 네 컷 만들기), `/zeta-labs`
- **정책**: 일본 특정상거래법 및 자금결제법 고지(`/policy/japan_*`) 페이지가 있어 **일본 시장 결제**까지 지원한다.

## 7. 비즈니스 모델 정리

1. **무료 기본 + 유료 재화**: 메타 설명은 "다른 덴 유료, 이 집은 무료"라고 내세우지만, 실제로는 재화 "피스(Piece)" 충전과 자동충전, 구독 "PASS"가 있다.
2. **결제 채널 다변화**: 국내는 Toss Payments와 브랜드페이, 해외는 CodaPay, 모바일은 인앱결제를 쓴다.
3. **광고**: Google Ad Manager 스크립트가 포함돼 있다.
4. **UGC 플랫폼**: 사용자가 플롯을 만들고 대시보드로 성과를 확인하며 팔로워와 피드로 팬덤을 키운다. 크리에이터 생태계가 핵심이다.
5. **글로벌 확장**: 플롯, 댓글, 피드에 번역 API가 붙어 있고, "바다 건너 신작" 섹션을 따로 둔다. 한국어·일본어·영어권을 동시에 공략한다.

## 8. 관찰된 보안·운영 포인트 (참고)

접근 시도 없이 공개 번들만 읽고 확인한 내용이다.

- **내부 경로 이름 노출**: 라우트 매니페스트에 `/x7k2-internal-login`, `/:locale/debug` 같은 내부용 경로 이름이 그대로 들어 있다.
  경로 이름을 숨기는 방식은 보안 수단이 되지 못하므로, 서버 쪽에서 인증과 IP 제한을 하고 있어야 한다. 실제로 그런지는 확인하지 않았다.
- 소스맵은 배포돼 있지 않다(`sourceMappingURL` 없음).
- 인증 쿠키는 `HttpOnly; Secure`이고 HSTS(`max-age=63072000`)가 켜져 있다.
- `robots.txt`는 `/auth/*`만 막고 나머지는 모두 허용한다.

## 9. 벤치마킹 시사점

비슷한 서비스를 만든다면 참고할 점:

1. **서버 주도 홈 섹션**(`type` + 아이템 배열): 앱 배포 없이 큐레이션과 시즌 기획전을 바꿀 수 있다.
2. **익명 토큰을 먼저 발급**하는 구조: 회원가입 전에 탐색과 대화를 허용해 가입 장벽을 낮춘다.
3. **플러그인으로 몰입감 높이기**: 채팅 외에 일기, SNS, 폰 훔쳐보기, 엔딩 같은 보조 콘텐츠를 모듈로 붙인다.
4. **크리에이터 퍼널 대시보드**: UGC 공급자에게 노출에서 성공까지의 전환 데이터를 줘서 창작 품질을 끌어올린다.
5. **번역판(Language Edition)**: 인기 플롯을 다른 언어로 유통해 콘텐츠 공급을 늘린다.

---

### 부록: 재현 절차

```bash
# 1) HTML과 스크립트 목록 (언어 쿠키를 받으려면 쿠키 저장 필요)
curl -sSL -c jar.txt -b jar.txt -H "Accept-Language: ko-KR" https://zeta-ai.io/ko/discover -o discover.html
grep -oE '<script[^>]*src="[^"]+"' discover.html

# 2) 번들 정적 분석 (REA analyze_web_bundle와 같은 역할)
grep -ohE '["`]/v[0-9]+/[A-Za-z0-9_/:.-]+' js/*.js | sort -u

# 3) REA 정식 사용 (권한이 있는 환경)
npx rea-agents setup
chromium --remote-debugging-port=9222 --user-data-dir=/tmp/rea-profile https://zeta-ai.io/ko/discover
rea list-browser-targets http://127.0.0.1:9222 --json
rea inspect-web-page http://127.0.0.1:9222 <TARGET_ID> --include-script-sources --json > capture.json
rea export-web-scripts capture.json ./exported --json
rea analyze-javascript-application ./exported/files --json
```
