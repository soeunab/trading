# 클로드 스킬 저장소·모음 사이트 확인 자료

- 확인일: 2026-10-29. 읽기만 하고 설치·실행하지 않음.
- 관련 글감: T28, T31, T37, T43, T44, T49
- 별 수·커밋 날짜는 조사 시점 기준이며 자주 바뀐다. 글에는 "많이 쓰이는" 정도로 쓰고 숫자는 발행 전 재확인.

## 유튜브에서 추천된 스킬

| 저장소 | 하는 일 | 설치 | 라이선스 | 위험도 | 블로거 적합도 |
| --- | --- | --- | --- | --- | --- |
| Canine89/hwpxskill | HWPX(한글) 문서를 템플릿(공문·보고서·회의록·제안서)으로 생성하거나 양식 유지한 채 내용 교체, 공문서 표기 검사 | `git clone` 후 `~/.claude/skills/hwpxskill`에 복사, `pip install lxml` | **없음**(재배포·수정 배포 금지 권장) | 낮음: 스크립트 14개에 네트워크 호출·키 요청 없음 | 중간(회사 문서) |
| mattpocock/skills | 개발자용 모음. `grilling`은 계획을 질문 라운드로 캐묻고 추천 답 제시, 비개발 용도는 `/grill-me` | `claude plugin install mattpocock-skills@claude-plugins-official` | MIT | 낮음: hooks 없음 | grill-me만 높음 |
| nidhinjs/prompt-master | 도구별(Midjourney, Claude Code 등) 최적화 프롬프트 작성 | claude.ai에 ZIP 업로드(권장) 또는 `~/.claude/skills`에 clone | MIT | 낮음: SKILL.md + md 2개뿐, 스크립트 없음 | **높음** |
| pbakaus/impeccable | AI가 만든 웹 UI의 "AI 티"를 잡아 다듬는 디자인 스킬 | `npx impeccable install` 또는 `/plugin marketplace add pbakaus/impeccable` | Apache-2.0 | 중간: 엔진 바이너리를 `~/.impeccable/bin`에 내려받음, 훅이 모델 승인과 무관하게 실행(README 명시), 외부 요청과 익명 텔레메트리(`npx impeccable telemetry off`로 끔) | 중간(블로그 HTML 손볼 때) |
| ayghri/i-have-adhd | 답변을 "다음 행동 먼저, 번호 단계, 목록 5개 이하, 서론·맺음말 없음"으로. 한국어 README | 플러그인 마켓플레이스 | MIT | 낮음: SessionStart 훅 1개, 플래그 파일 있을 때만 출력, 네트워크 없음 | 중간 |
| bradautomates/claude-video (watch) | 영상 URL/파일 → 타임스탬프 프레임 + 자막. Gemini 엔진(YouTube URL을 구글에 전달) 또는 로컬 엔진(yt-dlp + ffmpeg) | `/plugin marketplace add bradautomates/claude-video` → `/plugin install watch@claude-video` | (저장소 확인) | 낮음: 세션 시작 시 설치 상태 확인 스크립트만, 키는 `~/.config/watch/.env` | 중간 |

### watch 스킬 추가 사실 (T13·T31·T49용)
- Claude Code에서만 동작. Claude 채팅 앱·Cowork에서는 안 됨(README).
- 필요: Python 3.10+, FFmpeg/ffprobe, 최신 yt-dlp, YouTube는 Deno(JS 런타임). Gemini 엔진은 GEMINI_API_KEY(무료 키 가능, 한도 있음).
- 10분 이하 영상이나 구간 지정 시 가장 정확. 장면 단위 프레임이라 세밀한 코드 화면은 놓칠 수 있음.
- "클로드가 공부한다"의 실체: 모델이 학습하는 것이 아니라, 영상 내용을 skill-creator로 SKILL.md 작업 설명서로 저장하는 것.
- 로컬 엔진은 영상을 내려받음 → 유튜브 약관(허락 없는 다운로드 금지) 문제 소지. Gemini 엔진은 URL만 전달.
- 출처: https://github.com/bradautomates/claude-video , https://lazyowen.com/guides/claude-youtube-skill , https://ai.google.dev/gemini-api/docs/video-understanding

## 스킬 모음 사이트

| 사이트 | 정체 | 신뢰도 | 비고 |
| --- | --- | --- | --- |
| skills.sh | Vercel 운영. `npx skills add owner/repo` | 셋 중 가장 높음 | /audits에 Gen·Socket·Snyk 결과가 있으나 상위 일부만, "Pending"·"High Risk"도 섞임 |
| skillsmp.com | 개인 운영 GitHub 자동 수집. Anthropic 공식 아님 | 중간 | 등록 수가 출처마다 2.1만~120만으로 들쭉날쭉 |
| skillhub.club | 운영 법인 표기 없음, 15.5만 개, 유료 크레딧 상품 | 낮음 | AI 점수가 "설치 안전을 보장하지 않음"이라고 스스로 밝힘. **페이지에 AI 에이전트를 조종하려는 숨은 문구(프롬프트 인젝션)** 발견 |

결론: 모음 사이트는 검색용으로만. 설치는 원본 GitHub 저장소를 확인한 뒤.

## 비개발자에게 무거운 것 (참고만)
- sirioberati/YouTube-AI-Motion-Graphics(MIT): HTML 보드 74개 + Whisper 싱크 + Chrome/FFmpeg로 MP4. `setup.sh`가 `curl | sh`로 설치. 프롬프트에 자사(TEIN) 홍보 문구 삽입 지시.
- sirioberati/Genjustsu: 인물 교체. 유료 API 키 필요, cloudflared 외부 터널. 위험·비용 높음.
- sirioberati/Emotion-Control: 비상업·연구 전용 라이선스 → 수익화와 충돌.
- nam-ai-trend/shorts-auto: 남의 쇼츠를 yt-dlp로 받아 분석·재생성. 라이선스 없음, 약관·저작권 문제, 저장소가 생긴 지 열흘.

## 스킬 설치 전 보안 체크리스트 (T44 뼈대)
1. 원본 저장소인가? (비슷한 이름의 복제본, 가짜 배포본 주의. REA는 트로이 목마 복제본 사례가 보도됨)
2. `hooks/` 폴더나 `hooks.json`이 있는가? 있으면 언제 무엇을 실행하는지 읽기
3. 설치 스크립트가 바이너리를 내려받거나 `curl | sh`를 쓰는가?
4. 외부로 요청을 보내는가? 텔레메트리가 있는가? 끄는 방법은?
5. API 키를 요구하는가? 어디에 저장하는가?
6. 라이선스가 있는가? (없으면 수정 배포 금지)
7. 마지막 업데이트와 이슈 상태

## 출처
- https://github.com/Canine89/hwpxskill · https://github.com/mattpocock/skills · https://github.com/nidhinjs/prompt-master · https://github.com/pbakaus/impeccable · https://github.com/ayghri/i-have-adhd
- https://skills.sh · https://skills.sh/audits · https://skillsmp.com/about · https://skillhub.club
- https://www.kdnuggets.com/top-5-agent-skill-marketplaces-for-building-powerful-ai-agents
- https://github.com/sirioberati · https://github.com/nam-ai-trend/shorts-auto
