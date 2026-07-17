---
name: 이종호 ♥ 김희정 청첩장
description: 두 사람의 첫 전시(OUR FIRST EXHIBITION) — 미술관 도록 컨셉의 모바일 청첩장
colors:
  brass: "#b08d57"
  gold-highlight: "#d6b26e"
  rose-blush: "#c08a7a"
  ink: "#2e2a24"
  body-brown: "#4a4338"
  text-secondary: "#5a5246"
  text-informational: "#7a6d5b"
  label-muted: "#9a8c76"
  sand-deep: "#b9a888"
  border-sand: "#ddd0bc"
  border-light: "#d8cbb2"
  paper: "#f7f2ec"
  paper-bright: "#fbf8f2"
  frame-white: "#ffffff"
typography:
  display:
    fontFamily: "'Gowun Batang', Georgia, serif"
    fontSize: "26px-46px"
    fontWeight: 700
    lineHeight: 1.3
  display-en:
    fontFamily: "'Cormorant Garamond', Georgia, serif"
    fontSize: "17px-34px"
    fontWeight: 400
    letterSpacing: "0.04em"
  body:
    fontFamily: "'Gothic A1', sans-serif"
    fontSize: "13px-15px"
    fontWeight: 300
    lineHeight: 1.8
  label:
    fontFamily: "'Cormorant Garamond', Georgia, serif"
    fontSize: "10px-11px"
    fontWeight: 400
    letterSpacing: "0.4em"
rounded:
  none: "0"
  pill: "99px"
  circle: "50%"
components:
  button-primary:
    backgroundColor: "{colors.brass}"
    textColor: "{colors.frame-white}"
    rounded: "{rounded.none}"
    padding: "15px 0"
    typography: "{typography.body}"
  button-secondary:
    backgroundColor: "{colors.frame-white}"
    textColor: "#6b6253"
    rounded: "{rounded.none}"
    padding: "14px 15px"
  input-field:
    backgroundColor: "{colors.frame-white}"
    textColor: "{colors.ink}"
    rounded: "{rounded.none}"
    padding: "13px 13px"
---

# Design System: 이종호 ♥ 김희정 청첩장

## 1. Overview

**Creative North Star: "두 사람의 첫 도록(圖錄)"**

전시 도록을 손에 든 느낌이 시스템 전체를 지배한다. 크림빛 종이(#f7f2ec) 위에 흰 매트의 액자들이 걸리고, 앤티크 브래스(#b08d57)가 액자 테두리처럼 절제된 포인트를 찍는다. 사진 30장이 주인공이고, UI는 캡션과 여백으로 물러난다. 타이포는 세리프 2종(한글 고운바탕, 영문 Cormorant Garamond)이 도록의 격을, 고딕(Gothic A1)이 본문의 가독을 맡는다.

이 시스템이 명시적으로 거부하는 것: 템플릿 모바일 청첩장(바른손·더카드 류)의 뻔한 레이아웃, 반짝이 효과, 촌스러운 인트로. 어디서 본 듯한 순간이 하나라도 있으면 실패다.

**Key Characteristics:**
- 사진이 주인공, UI는 정갈하고 절제된 캡션 역할
- 앤티크 브래스 단일 악센트 — 화면의 10% 이하
- 액자 = 물리적 오브젝트 (흰 매트, 미세 회전, 구조적 그림자, 자이로 틸트)
- 위트는 디테일에 (SD 캐릭터, 꾹 누르기, 이모지 칩) — 화면 전체는 조용히
- 넓은 자간의 영문 라벨이 전시 서사를 관통 (GALLERY 01, OUR FIRST EXHIBITION)

## 2. Colors

가을 오후의 도록 팔레트 — 종이, 모래, 황동, 잉크.

### Primary
- **앤티크 브래스** (#b08d57): 유일한 악센트. 영문 라벨, 주 버튼, 구분선 포인트, 골드 디테일. 오래된 액자 테두리의 황동. 화면당 10% 이하로만.
- **골드 하이라이트** (#d6b26e): 브래스보다 밝은 금빛 — 인트로 로딩 도트 등 극소량의 반짝임 전용.

### Tertiary
- **로즈 블러시** (#c08a7a): 하트·애정 표시 등 낭만 디테일 극소량.

### Neutral
- **잉크** (#2e2a24): 제목·이름·핵심 텍스트. 검정 대신 따뜻한 흑갈색.
- **바디 브라운** (#4a4338): 본문 텍스트.
- **세컨더리** (#5a5246): 보조 문단.
- **정보 텍스트** (#7a6d5b): 12px대 안내문 최소색 — AA 4.5:1 하한선 (이보다 연하게 금지).
- **라벨 뮤트** (#9a8c76): 장식 라벨 전용 (GALLERY NN, No.xx). 정보 전달 텍스트에 사용 금지.
- **보더 샌드** (#ddd0bc) / **보더 라이트** (#d8cbb2): 테두리·구분선.
- **페이퍼** (#f7f2ec): 페이지 배경 — 도록의 종이.
- **페이퍼 브라이트** (#fbf8f2): 카드·박스 표면.
- **프레임 화이트** (#ffffff): 액자 매트와 버튼·입력창 표면.

### Named Rules
**황동 절제 규칙.** 브래스(#b08d57)는 화면의 10%를 넘지 않는다. 희소함이 곧 격이다. 새 섹션에 두 번째 악센트색을 들여오는 것은 금지.

**대비 이중 기준 규칙.** 정보를 전달하는 텍스트는 #7a6d5b(4.5:1)가 하한. 장식 라벨(#9a8c76, #b3a691)은 의도된 저대비 — 이 둘을 섞지 않는다.

## 3. Typography

**Display Font:** Gowun Batang (Georgia, serif 폴백) — 한글 세리프
**Display/Label Font:** Cormorant Garamond (Georgia, serif 폴백) — 영문 세리프
**Body Font:** Gothic A1 (sans-serif 폴백) — 한글 본문·UI

**Character:** 세리프 2종이 도록의 격식을, 얇은 고딕(300)이 본문의 조용한 가독을 맡는 3단 편성. 영문은 항상 세리프, 한글 본문은 항상 고딕.

### Hierarchy
- **Display** (Gowun Batang 700, 26–46px): 인트로 숫자 "10.11", 커버 이름, 섹션 제목.
- **Headline** (Cormorant Garamond 400, 17–34px, 자간 0.04em): 영문 표제, 이탤릭 캡션 (「」 안 작품명).
- **Body** (Gothic A1 300, 13–15px, 행간 1.8): 한글 본문. 짧은 문장, 핵심 먼저.
- **Label** (Cormorant Garamond 400, 10–11px, 자간 0.4em, 대문자): 전시 라벨 — GALLERY 01, R.S.V.P., GUEST NOTICE.
- **Micro** (Gothic A1, 11–12.5px): 안내문·주석 — 색상은 #7a6d5b 이상.

### Named Rules
**넓은 자간 라벨 규칙.** 영문 대문자 라벨은 자간 0.4em 내외로 넓게 튼다. 이 라벨이 섹션의 문패이자 전시 서사의 리듬이다.

## 4. Elevation

그림자는 구조적이다 — 액자가 벽에서 떠 있다는 물리적 사실을 말한다. 장식용 글로우나 분위기용 그림자는 없다. 모든 그림자는 따뜻한 갈색조(rgba(60,46,28,…))로, 크림 배경 위에서 순검정 그림자는 금지.

### Shadow Vocabulary
- **액자 부상** (`box-shadow: 0 22px 34px -20px rgba(60,46,28,.5)`): 갤러리 액자 기본 — 벽에서 살짝 뜬 깊이.
- **액자 접촉** (`box-shadow: 0 1px 2px rgba(60,46,28,.3)`): 액자 하단의 미세 접지감. 부상 그림자와 항상 세트.
- **대형 액자** (`box-shadow: 0 24px 38px -22px rgba(60,46,28,.5)`): 모시는 글·오시는 길 등 큰 액자.
- **라이트박스** (`box-shadow: 0 14px 34px -12px rgba(0,0,0,.55)`): 어두운 오버레이 위 확대 사진 전용 — 이때만 무채색 그림자 허용.

### Named Rules
**벽걸이 규칙.** 그림자는 액자(사진·큰 패널)에만 붙는다. 버튼·입력창·칩은 평평하다 — 테두리와 배경색으로만 구분한다.

## 5. Components

정갈하고 절제된 — 사진이 주인공, UI는 조용한 필수 도구.

### Buttons
- **Shape:** 직각 (radius 0). 버튼은 도록의 인쇄 블록처럼 각지다.
- **Primary:** 브래스 채움(#b08d57) + 흰 글자, padding 15px 0 (전폭), Gothic A1 13px 자간 0.1em — RSVP 제출 등 핵심 행동 전용.
- **Secondary:** 흰 배경 + 1px #d8cbb2 테두리, #6b6253 글자, padding 14px 15px — 복사·지도 등 보조 행동.
- **Hover / Focus:** 과장 없음. 터치 타겟 44px 이상 유지.

### Chips
- **Style:** 방명록 템플릿 칩 — 필 형태(99px), 이모지 + 문구, 가로 스크롤 행 (스크롤바 숨김).
- **State:** 클릭 시 입력창에 문구 채움. 유일하게 둥근 인터랙티브 요소 — 위트 담당.

### Cards / Containers
- **Corner Style:** 직각 (radius 0).
- **Background:** #fbf8f2 (박스) 또는 #ffffff (액자 매트).
- **Shadow Strategy:** 액자만 그림자 (Elevation 참조), 정보 박스는 평평 + 1px #ddd0bc 테두리.
- **Internal Padding:** 넉넉한 여백 — 도록의 마진.

### Inputs / Fields
- **Style:** 흰 배경, 1px #ddd0bc 테두리, 직각, padding 13px, Gothic A1 300 13px.
- **Focus:** outline 없음 (테두리 유지) — 절제 우선.

### Navigation
- 없음 — 단일 페이지 세로 스크롤. 섹션 라벨(GALLERY NN)이 이정표 역할.

### 액자 (Signature Component)
사진 = 흰 매트(#fff) + 미세 회전(±1~2도) + 구조적 그림자 + 하단 캡션(「작품명」 세리프 이탤릭 + 부제). 데스크톱 호버·모바일 꾹 누르기(180ms)로 들어올림, 대형 액자 3개는 자이로 틸트. 이 컴포넌트가 시스템의 심장이다.

## 6. Do's and Don'ts

### Do:
- **Do** 브래스(#b08d57)를 유일한 악센트로 — 화면당 10% 이하.
- **Do** 정보성 텍스트는 #7a6d5b(4.5:1) 이상, 터치 타겟 44px 이상.
- **Do** 그림자는 따뜻한 갈색조 rgba(60,46,28,…)로만, 액자에만.
- **Do** 영문은 세리프(Cormorant Garamond), 한글 본문은 Gothic A1 300 — 편성 유지.
- **Do** 새 안내 블록은 기존 문법 재사용 (골드 라벨 행, 구분선, GUEST NOTICE 형식).
- **Do** 모든 모션에 prefers-reduced-motion 대안 제공 (인트로 스킵 포함).

### Don't:
- **Don't** 템플릿 모바일 청첩장(바른손·더카드 류)처럼 보이는 어떤 요소도 — 반짝이 효과, 하트 파티클, 뻔한 레이아웃 금지.
- **Don't** 두 번째 악센트색 도입 금지 — 파랑·초록 계열 버튼이나 배지 절대 금지.
- **Don't** 버튼·카드에 radius 주지 않기 — 직각이 시스템. 둥근 것은 칩·토스트·도트뿐.
- **Don't** 장식 라벨색(#9a8c76 이하)을 정보 전달 텍스트에 쓰지 않기.
- **Don't** 순검정 그림자·장식용 글로우 금지.
- **Don't** 사진보다 UI가 시끄러워지는 어떤 장식도 — 위트는 인터랙션 디테일에만.
