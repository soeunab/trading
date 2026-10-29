# AI 영상 도구 무료 한도 확인 자료

- 확인일: 2026-10-29 (크레딧·횟수는 발행 전 재확인 필수)
- 관련 글감: T24, T25, T26, T27, T30, T36
- 데이터: `../data/youtube-60-videos.csv` (영상 60편 목록, 도구 태그, 광고·제휴 신호)

## 영상 60편 통계
- 다룬 도구(영상당 최대 3개): 클로드 코드 16, 힉스필드 14, 제미나이 13, 클로드 스킬 11, 구글 플로우·Veo 9, 리모션 8, 하이퍼프레임 4, 나노바나나 4, 아트크래프트 3, 메타 AI·Vibes 3
- 제휴·추적 링크 19편, 그중 "유료 광고" 표시 4편, 강의·커뮤니티 홍보 29편
- 한계: 유튜브 검색으로 숏츠만 거를 수 없어 일반 영상 포함. 영상 음성이 아닌 제목·설명란 기준.

## 도구별 확인 결과

| 도구 | 무료 범위 | 요구사항 | "무료" 주장 |
| --- | --- | --- | --- |
| 구글 Flow | 하루 50크레딧, 이월 없음. Veo 3.1 Lite 10 / Fast 20 / Quality 100크레딧 → 무료로 Fast 약 2편, Quality 불가. 한국 지원, 18세 이상 | 웹 | 맛보기 |
| 구글 Vids | 개인 계정 월 10편 무료 생성 (2026-04-02 발표) | 웹 | 맛보기 |
| Gemini 앱 영상 | 무료 한도 공식 수치 없음. 7~8월 Gemini Omni 무료 10편 행사 8/11 종료 | 웹·앱 | 사실상 유료 |
| Meta Vibes | 생성 무료, 한도 비공개, 구독 예고. Meta AI 앱은 2026-05 한국 출시, Vibes 한국 사용 가능 여부 미확인 | 앱·웹 | 확인 불가 |
| Vrew | 2026-04-22부터 통합 크레딧. 무료 매월 1일 200크레딧, 음성 분석 1분=1크레딧(자막 약 200분). 유료 라이트 2,000/스탠다드 10,000/비즈니스 12,000크레딧. AI 목소리 상업 이용 가능(Voicevox만 출처 표기) | 맥 앱 포함 | 제한적 무료 |
| YouTube Create | 무료. Android(RAM 4GB+)와 iPhone(XR+, iOS 17+) 모두 지원, 한국 포함 | 모바일 | 맞음 |
| Higgsfield MCP | 공식 도움말: "MCP로 생성하는 모든 것은 플랜과 무관하게 크레딧 차감". 무제한은 웹사이트 일부만. 상시 무료 플랜 없음. 요금(제3자 정리, 9월): Starter $15/200, Plus $49/1,000, Ultra $129/3,000크레딧. 제휴 프로그램 최대 25% 수수료 12개월. 크리에이터 105명이 협찬 영상 290개 | 원격 MCP | 틀림 |
| HyperFrames (HeyGen) | Apache 2.0, 렌더링당 요금·상업 이용 조건 없음. Studio 데스크톱 앱 무료(macOS Apple Silicon/Intel, Windows, Linux) | Node.js 22+, FFmpeg. Claude Code: `claude plugin marketplace add heygen-com/hyperframes` → `/hyperframes:hyperframes`. 기타: `npx skills add heygen-com/hyperframes` | 맞음(모션그래픽) |
| Remotion + 공식 스킬 | 개인·직원 3명 이하 영리 회사·비영리·평가 무료. 그 외 Company License: Creators 좌석당 월 $25, Automators 렌더당 $0.01(최소 월 $100), Enterprise 월 $500~. 5.0에서 라이선스 변경 예정 | Node 기반. 스킬: `npx skills add remotion-dev/skills`, 대표 `/remotion-best-practices`. 공식 저장소 외 복제본 주의 | 조건부 맞음 |
| ComfyUI + LTX | 무료 로컬. Apple Silicon MPS 공식 지원. 다만 LTX-2는 MPS가 float64·fp8 미지원이라 우회 필요, 모델 약 40GB → 메모리 64GB+ 권장. 공식 LTX Desktop은 맥에서 API 모드만 | 고사양 | 기본형 맥 미니로는 어려움 |
| Claude Design | 출시 시 Pro/Max/Team/Enterprise. 현재 지원 문서엔 Free까지 적혀 있으나 다른 공식 페이지와 다름. 내보내기 PDF/PPTX/HTML/zip, MP4 없음 | 웹 | "영상 편집" 틀림 |
| Claude Motion (10/8 베타) | 텍스트·차트·도형·이미지를 코드로 움직여 MP4로. 실사·사람 생성 불가. Team/Enterprise 전용 | 웹 | 개인은 불가 |

## 핵심 구분 (글에 쓰기 좋은 설명)
- **영상 생성 AI**(Flow/Veo, Higgsfield, Vibes): 실사·애니 장면을 만든다. 만들 때마다 크레딧.
- **코드 기반 모션그래픽**(HyperFrames, Remotion + 클로드 코드): 글자·도형·차트 애니메이션. 도구는 무료, AI 구독료만.
- "클로드로 영상 만들기" 영상 대부분은 두 번째 종류.

## 출처
- https://support.google.com/flow/answer/16526234?hl=en · https://support.google.com/flow/answer/16353544
- https://blog.google/products-and-platforms/products/workspace/google-vids-updates-lyria-veo/
- https://support.google.com/gemini/answer/16275805?hl=en · https://mezha.ua/en/news/google-extends-free-gemini-omni-access-313995/amp/
- https://www.techcrunch.com/2026/02/05/meta-tests-a-standalone-app-for-its-ai-generated-vibes-videos · https://en.fnnews.com/news/202605131607277102
- Vrew 통합 크레딧 공지: https://docs.channel.io/vrew-kr/ko/articles/%EA%B3%B5%EC%A7%80-2026422-%ED%86%B5%ED%95%A9-%ED%81%AC%EB%A0%88%EB%94%A7-%EB%B0%A9%EC%8B%9D%EC%9C%BC%EB%A1%9C-%EB%B3%80%EA%B2%BD%EB%90%A9%EB%8B%88%EB%8B%A4-58dfc5fd
- Vrew 상업 이용: https://docs.channel.io/vrew-kr/ko/articles/%EC%83%81%EC%97%85-%EC%9D%B4%EC%9A%A9-AI-%EB%AA%A9%EC%86%8C%EB%A6%AC%EB%A5%BC-%EC%83%81%EC%97%85%EC%A0%81%EC%9D%B8-%EC%9A%A9%EB%8F%84%EB%A1%9C-%EC%82%AC%EC%9A%A9%ED%95%A0-%EC%88%98-%EC%9E%88%EC%9D%84%EA%B9%8C%EC%9A%94-addbf954
- https://support.google.com/youtube/answer/13521789 · https://support.google.com/youtube/answer/13952912
- https://higgsfield.ai/creator-hub/help-center/integrations/what-is-higgsfield-mcp · https://www.layer3labs.io/guides/higgsfield-mcp · https://higgsfield.ai/blog/higgsfield-affiliate-program-2026 · https://sponsorradar.com/brands/higgsfield
- https://github.com/heygen-com/hyperframes · https://hyperframes.heygen.com
- https://raw.githubusercontent.com/remotion-dev/remotion/main/LICENSE.md · https://www.remotion.pro/license · https://www.remotion.dev/docs/ai/skills
- https://docs.comfy.org/installation/system_requirements · https://help.ltx.io/hc/en-us/articles/37088959955986
- https://www.anthropic.com/news/claude-design-anthropic-labs · https://support.claude.com/en/articles/14604416-get-started-with-claude-design · https://support.claude.com/en/articles/17454997-get-started-with-claude-motion.md
