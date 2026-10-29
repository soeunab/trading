# REA (Reverse Engineer Anything) 자료

- 확인일: 2026-10-19 ~ 10-29
- 관련 글감: T32, T45
- 기존 발행 글: [클로드 코드에서 REA를 활용한 '제타AI' 구조 분석](https://www.jiwon4u.kr/2026/10/claude-code-rea-website-structure-analysis.html) (10/22 발행)

## 무엇인가
- MIT 라이선스 오픈소스. AI 코딩 에이전트(Claude Code, Codex, Cursor, Gemini CLI 등)를 역공학 도구에 연결해 주는 MCP 서버 + CLI.
- 새 디컴파일러가 아니라, Ghidra·Hopper·IDA 같은 기존 도구와 에이전트를 잇고, 결과를 근거(오프셋, 코드 조각)와 함께 돌려준다.
- 설치: `npx rea-agents setup`. Node.js 22.19+ (또는 24.11+, 26+).
- 분석 대상(README 표): 네이티브 바이너리, Go/Rust 바이너리, WASM, JavaScript/Electron 앱, **웹사이트**(Chrome CDP로 페이지 구조·스크립트·네트워크·스크린샷), HAR 캡처, .NET, Android APK, 펌웨어 등.
- 웹 분석 흐름: `list_browser_targets` → `inspect_web_page`(DOM, 스크립트, 리소스) → `analyze_web_bundle`(청크, 라우트·엔드포인트 후보, 라이브러리 지문) → `export_web_scripts` → `analyze_javascript_application`.
- 공식 출처: GitHub `morluto/rea`, 사이트 rea.tools, npm `rea-agents`. README 스스로 "비슷한 저장소 이름이나 복사된 README는 공식임을 보장하지 않는다"고 경고.

## 주의
- 인기(별 수)가 출처마다 크게 다름(10만~33만). 숫자는 글에 단정하지 말 것.
- 팩트체크 기사에서 **트로이 목마가 든 가짜 복제본** 유포를 경고.
- 유튜브에서 "REA로 일반인도 프로그램을 복제할 수 있다"는 식의 소개가 있음(아트크래프트 영상) → 과장 주의. 분석·이해 도구이지 복제 도구로 홍보하면 법적 오해 소지.
- 지원포유 실습 환경 메모: 클라우드 환경에서는 `rea-agents` 설치가 권한 정책으로 막혀 REA의 분석 순서를 curl·Chromium으로 대신 수행했다. 운영자 맥에서는 직접 설치 가능.

## 지원포유가 REA 방식으로 분석한 것 (글감 소재)
- 제타AI 디스커버 페이지: Next.js 16.4 + Vercel, 서버 주도 섹션, 플롯 데이터 모델, API 약 90개, 라우트 지도 → `docs/zeta-ai-discover-analysis.md`
- lazyowen.com: Next.js + Cloudflare, 이메일 게이트, 유입 퍼널, 협찬·구독 수익 → `docs/lazyowen-analysis.md`
- 공개 자산만 읽고, 로그인·결제·내부 경로 접근은 하지 않는 원칙.

## 출처
- https://github.com/morluto/rea
- https://www.inside.com.tw/article/42615-rea-reverse-engineer-anything-open-source-mcp-ai-agent-github
- https://pasqualepillitteri.it/en/news/23845/rea-reverse-engineer-anything-github-true-false
