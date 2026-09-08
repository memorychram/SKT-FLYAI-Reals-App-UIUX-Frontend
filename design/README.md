# design/ — UI/UX 설계 산출물

## 최종 원본 (Source of Truth)

**Figma — [Reals_DesignSystem_Final](https://www.figma.com/design/hZtETvPLM5cf4ubd6F5uJu/Reals_DesignSystem_Final)**

최종 디자인 결정이 기록된 단일 원본. `mockups/` 의 확정 HTML 3종을 DOM 단위로 1:1 변환한 **35개 화면**과 색상 Variables, 컴포넌트, 의사결정 기록을 담는다.

| 페이지 | 내용 |
|---|---|
| `00 Cover · Decision Log · Tokens` | 의사결정 기록(와이어프레임 → 시안 → HTML → RN), 토큰 스와치, 레이아웃·radius·모션 값, 타입 스케일, 화면 인벤토리 |
| `01 Components` | Button(변형 세트) · Chip · Field · Card · StatusBar · AppBar · TabBar · StatusPill |
| `02 Screens` | Onboarding 5 · Auth 7 · App UI Kit 18 · State/Error 5 (총 35) |

### 화면 구성 (35)

| 그룹 | 화면 | 원본 |
|---|---|---|
| Onboarding (5) | `1-store` `2-ai-chat` `3-guide` `4-auto-edit` `5-insight` | [`mockups/01-onboarding.html`](./mockups/01-onboarding.html) |
| Auth (7) | `0-splash` `1-login` `2-signup` `3-permissions` `4-store-sync` `5-find-id` `6-reset-pw` | [`mockups/03-auth-login-signup.html`](./mockups/03-auth-login-signup.html) |
| App UI Kit (18) | `home` `saved` `chat` `mypage` `shoot-topic` `format` `camera` `editing` `export` `edit-profile` `my-video` `insight` `settings` `plans` `faq` `terms` `privacy` `states` | [`mockups/02-app-ui-kit.html`](./mockups/02-app-ui-kit.html) |
| State / Error (5) | `feed-loading` `feed-error` `feed-empty` `editing-timeout` `editing-failed` | `02-app-ui-kit.html` 상태 카탈로그 (설정 → 상태·오류 화면) |

---

## 폴더 구성

| 경로 | 내용 | 상태 |
|---|---|---|
| Figma `Reals_DesignSystem_Final` | 최종 디자인 원본 — 35화면 · 토큰 · 컴포넌트 · Decision Log | ✅ |
| `mockups/` | 화면 시안 원본 HTML 3종 — 온보딩 · 앱 본 화면 UI 킷 · 로그인/회원가입 ([목록](./mockups/README.md)) | ✅ |
| `flows/` | 사용자 플로우 · 화면 전환 다이어그램 | ⬜ 추가 예정 |
| [`../DESIGN.md`](../DESIGN.md) | 디자인 값 명세 (색 · 타이포 · 간격 · radius) | ✅ |
| [`../src/design/tokens.json`](../src/design/tokens.json) | 앱에 적용된 디자인 토큰 | ✅ |
| [`../docs/`](../docs/) | 시안 ↔ 코드 대응 이력 (`시안V4_대응표.md` 외) | ✅ |

---

## Design Tokens

Figma 파일의 Variables 컬렉션과 동일한 값. 구현 토큰은 [`../src/design/tokens.json`](../src/design/tokens.json).

```yaml
color:
  canvas: "#FFFFFF"
  surface: "#F8FAFC"
  panel: "#FFFFFF"
  ink: "#0F172A"
  ink-2: "#1E293B"
  ink-3: "#334155"
  slate-muted: "#64748B"
  hairline: "#E2E8F0"
  track: "#CBD5E1"
  brand: "#2563EB"
  brand-tint: "#EFF6FF"
  brand-border: "#BFDBFE"
  brand-soft: "#60A5FA"
  heart: "#EF4444"
  verified: "#10B981"
  naver: "#03C75A"
  on-brand: "#FFFFFF"
  scrim: "rgba(15,23,42,0.6)"
  scrim-light: "rgba(15,23,42,0.45)"

font:
  family: Pretendard          # Figma 파일에서는 Inter 로 대체 렌더 (Pretendard 미설치)
  weights: [400, 500, 600, 700, 900]

type:                         # size / weight
  display:  {size: 32, weight: 700}
  h1:       {size: 22, weight: 700}
  h1-bar:   {size: 18, weight: 700}
  h2:       {size: 16, weight: 600}
  body:     {size: 15, weight: 500}
  body-sm:  {size: 14, weight: 500}
  caption:  {size: 13, weight: 500}
  meta:     {size: 12, weight: 500}
  micro:    {size: 11, weight: 500}

layout:
  frame: {w: 393, h: 852}     # iPhone 14/15
  statusbar: 54
  header: 44
  tabbar: 83
  gutter: [16, 24]

radius: {input: 12, card: 16, dialog: 24, sheet: 28, frame: 48, pill: 999}
size:   {control: 48, input: 52, chip: 32, icon: [20, 22, 24], stroke: 2}

motion:
  fade: 220ms
  rise: 250ms
  sheet: 320ms
  ease-out: cubic-bezier(.16, 1, .3, 1)
  tap-scale: 0.95
```

---

## 제작 과정 (Decision Log 요약)

1. **와이어프레임 148장** — 핵심 플로우(온보딩 → 촬영 준비 → 가이드 촬영 → AI 편집 → 내보내기)와 4탭 구조 확정
2. **디자인 시안 69장** — 브랜드 블루(`#2563EB`) 단일 포인트 컬러 + 슬레이트 중립색 체계, radius 규칙(카드 16 / 입력 12 / 시트 28) 확정
3. **HTML 프로토타입** — React + Tailwind 단일 파일로 실제 동작하는 킷 제작. 상태·오류 화면 5종을 별도 카탈로그로 정의 → `mockups/`
4. **React Native 구현** — 이 저장소. 토큰 값은 Figma Variables 와 동일
5. **Figma 최종 원본 역산** — HTML DOM 을 Figma 로 1:1 변환해 단일 원본 확립

## Figma 변환 방식 및 주의

- Playwright 로 HTML 렌더 → DOM 좌표·스타일 추출 → Figma Plugin API 로 절대 배치 재구성 (**픽셀 충실도 우선, 오토레이아웃 미적용**)
- SVG 아이콘은 `Icon Library` 프레임의 컴포넌트로 중복 제거 후 인스턴스 배치
- 온보딩 일러스트의 루프 애니메이션은 **완료 상태**를 캡처
- 상태 카탈로그의 "카탈로그로 돌아가기" 리뷰용 버튼은 제외
- 알려진 원본 이슈: 내보내기 화면 `복사` 버튼의 lucide `copy` 아이콘이 HTML 아이콘 맵(`window.LUCIDE`)에 누락되어 빈 svg 로 렌더 → **RN 구현 시 아이콘 보완 필요**
