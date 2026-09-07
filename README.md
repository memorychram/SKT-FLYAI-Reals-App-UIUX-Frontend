# Reals — Mobile Client (SKT FLY AI Challenger 9기 · 열정 2조)

![Role](https://img.shields.io/badge/Role-UI%2FUX%20Design%20%26%20Client%20Integration-2563EB)
![Team](https://img.shields.io/badge/Team-6%20members-0F172A)
![Stack](https://img.shields.io/badge/React%20Native%200.86-Expo%2057-000020)
![Lang](https://img.shields.io/badge/TypeScript-6.0-3178C6)
![State](https://img.shields.io/badge/TanStack%20Query%205%20%C2%B7%20Zustand%205-FF4154)

> **Reals(리얼스)** — 40~60대 소상공인 사장님이 AI의 도움으로 숏폼(릴스/쇼츠)을
> **기획 → 촬영 → 편집 → 게시**까지 혼자 끝내는 모바일 앱.
> 이 저장소는 그 앱의 **클라이언트(React Native / Expo)** 와 **UI/UX 설계 산출물**을 담은 개인 쇼케이스입니다.
> 팀 전체 시스템(AI 서버 · 백엔드)은 [`SKT-FLYAI-Reals-Fullstack-Archive`](../SKT-FLYAI-Reals-Fullstack-Archive) 참고.

---

## 1. 팀 내 역할 (6인)

| 파트 | 인원 | 담당 |
|---|---|---|
| **UI/UX · 클라이언트 (본인)** | 1 | 서비스 화면 플로우 설계, 화면 시안(V1→V4) 제작, 디자인 토큰 명세, 클라이언트 API 연동 검증·QA |
| 프론트엔드 구현 | 1 | React Native 화면 구현, 시안 대조, EAS 빌드 |
| AI | 2 | LangGraph Agent(추천·편집·트렌드), FFmpeg 렌더러 |
| 백엔드 | 2 | FastAPI 메인 서버, MySQL/Alembic, AWS 인프라, SNS 연동 |

본인 산출물의 원본(시안 HTML/Figma, 플로우 다이어그램)은 [`design/`](./design) 에 있습니다.
코드 커밋 이력은 팀 저장소(`skt-flyai-9th/frontend`) 기준이며, 이 저장소는 그 스냅샷을 정리한 것입니다.

---

## 2. UI/UX 설계 의도 — "AI 추론값을 사장님이 왜곡 없이 받아들이게"

사용자는 스마트폰이 익숙하지 않은 40~60대 자영업자이고, 사용 환경은 **매장 안(한 손, 야외 조명, 짧은 시간)** 입니다. 설계 원칙 4가지:

| 원칙 | 구현 |
|---|---|
| **한 화면 = 질문 하나** | AI 추천 대화(`AiChatScreen`)는 턴당 질문 1개, 선택지 ≤ 3개. 필수 정보(홍보 대상·목적·촬영 시간·얼굴 노출) 충족 전에는 추천을 노출하지 않음 — AI 서버 대화 정책과 1:1 일치 |
| **추론 결과는 숫자로 꾸미지 않는다** | API 에 없는 필드는 화면에 만들지 않음. 인사이트(조회수·증감)는 서버 값만 표시, mock 값은 `EXPO_PUBLIC_FORCE_MOCK` 스위치로 격리 |
| **촬영은 컷 단위로 안내** | 촬영 가이드를 `scenes/tasks` 컷 순서대로 `CameraGuideOverlay` + `PipGuide`(참고 영상 PiP) 로 노출. 사장님은 "다음 컷" 만 누르면 됨 |
| **대비 4.5:1 이상, 라인 아이콘, pill 칩** | `DESIGN.md` / `src/design/tokens.json` 으로 값 고정. 브랜드 파랑 `#2563EB`, 본문 `#334155`, 헤어라인 `#E2E8F0`, 이모지 금지 |

화면 플로우 (핵심 경로):

```
온보딩(5장) → 로그인/회원가입 → 매장 등록(지도 검색)
  → 홈 피드(트렌드 숏폼) → AI 대화(목적·조건 수집) → 포맷 추천 1건
  → 촬영 가이드(컷별 PiP) → 촬영 → 업로드 → 편집 대기 → 결과(저장·공유·게시)
  → 마이페이지(내 숏폼 · 인사이트 · SNS 연동)
```

시안 → 코드 대응 이력: [`docs/시안V4_대응표.md`](./docs/시안V4_대응표.md), [`docs/디자인1차수정_적용현황.md`](./docs/디자인1차수정_적용현황.md)

---

## 3. 클라이언트 구현 요약

| 관심사 | 구현 | 위치 |
|---|---|---|
| **서버 통신 단일 창구** | Base URL 주입(EAS env → app.json 폴백), Authorization, snake↔camel 변환, `error_code` → 사용자 문구, access 만료 시 refresh 후 1회 재시도, 도메인별 mock 스위치 | `src/api/http.ts`, `src/api/endpoints.ts` |
| **비동기 상태** | TanStack Query — 도메인별 query/mutation 분리, 편집 결과 폴링, 추천 지연 카운트는 요청 시작 시점 기준 | `src/api/queries/*` |
| **클라이언트 상태** | Zustand — 세션 토큰(SecureStore), 앱 상태, 자동 저장 | `src/lib/session.ts`, `src/lib/appState.ts`, `src/lib/useAutoSave.ts` |
| **렌더링 최적화** | 홈 선반↔탭바 교차 페이드(320ms, JS 드라이버 — 레이아웃 속성 애니), 플레이어 언마운트 시 해제, 참고 영상 접기, 9:16 재구성 | `src/domains/feed/*`, `src/ui/GuidePlayer.tsx` |
| **디자인 시스템** | 토큰 JSON → theme.ts → `src/ui/*` 36개 공용 컴포넌트 (Button/Card/Chip/AppBar/TabBar/Donut/LineChart …). 화면 코드는 값 직접 사용 금지 | `src/design/`, `src/ui/` |
| **네이티브 기능** | expo-camera(촬영·카운트다운), expo-video, expo-media-library(갤러리 저장), expo-sharing, expo-notifications(편집 완료 푸시) | `src/domains/shoot/*`, `src/lib/useSaveToGallery.ts`, `src/lib/push.ts` |
| **화면** | 8개 도메인 · 25개 화면 (auth 4 · feed 3 · shoot 2 · edit 2 · my 8 · onboarding · store · settings · format · project) | `src/domains/*` |

---

## 4. Quick Start

```bash
# 요구: Node 20+, npm, Expo Go 또는 EAS dev client (Android)
git clone https://github.com/memorychram/SKT-FLYAI-Reals-App-UIUX-Frontend
cd SKT-FLYAI-Reals-App-UIUX-Frontend
npm install

# 서버 없이 UI 검증 (전 도메인 mock)
EXPO_PUBLIC_FORCE_MOCK=1 npx expo start

# 실서버 연동
EXPO_PUBLIC_API_BASE_URL=https://<backend-host> npx expo start

npm run typecheck        # tsc --noEmit
```

Firebase 푸시를 쓰려면 `google-services.example.json` 을 실제 `google-services.json` 으로 교체합니다 (저장소에는 포함하지 않음).

---

## 5. 저장소 구성

```
design/              UI/UX 설계 산출물 (시안 HTML · 플로우 · 토큰 명세)   ← 본인 산출물
docs/                시안↔코드 대응표, 버그 분석, 디자인 수정 적용 현황
src/api              http.ts · endpoints.ts · queries/ · schema/ · mock/
src/design           tokens.json · theme.ts · fonts.ts
src/ui               공용 컴포넌트 36개 · coach/
src/domains          화면 (auth · feed · shoot · edit · my · onboarding · store · settings · format · project)
src/navigation       React Navigation (stack + bottom tabs)
DESIGN.md            디자인 값 명세 (hex · 간격 · radius · 타이포)
CLAUDE.md            팀 프론트 작업 규칙 · 시안 대조 방법
```

## 6. 배운 것 (면접용 요약)

- 디자인 값은 "느낌"이 아니라 **토큰 숫자**로 넘겨야 구현 오차가 안 생긴다 — 로고가 16% 컸던 사고(`CLAUDE.md` §5)는 계수 하나를 빠뜨린 결과였다.
- AI 결과를 보여주는 화면은 **서버 계약이 곧 UI 스펙**이다. 필드가 없으면 그리지 않는다.
- 편집 대기 화면의 "몇 시간째 편집중"은 UX 문제가 아니라 **AI 서버의 무한 재큐 버그**였다 (`AI_전달사항.md` §1). 프론트에서 원인을 코드로 추적해 AI 팀에 전달했다.

---
License: 팀 프로젝트 저작물. 학습·포트폴리오 열람 목적 외 재배포 금지.
