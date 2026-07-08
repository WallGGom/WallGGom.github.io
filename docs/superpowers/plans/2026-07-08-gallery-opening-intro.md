# 전시장 개관 인트로 (Gallery Opening Intro) Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 청첩장 진입 순간 어두운 전시장에 조명이 켜지며 개관하는 오프닝을 넣고, 클라이맥스에 화면 흔들림으로 임팩트를 준다.

**Architecture:** `index.html` 단일 파일 안에서 해결. 초기 마크업에 포함된 `position:fixed` 오버레이 `<div data-opening>`가 CSS `@keyframes`로 스스로 재생·소멸(FOUC·JS실패 안전). JS `setupOpening(root)`는 스킵/스크롤잠금/화면흔들림/정리와 커버 리빌 시점 조율만 담당. 새 파일·라이브러리 없음.

**Tech Stack:** 순수 HTML/CSS/JS. 기존 커스텀 프레임워크(`DCLogic` 상속 `Component`, `componentDidMount`/`componentWillUnmount`).

## Global Constraints

- 새 파일·npm 의존성 추가 금지. 모든 변경은 `index.html` 안에서.
- 방문할 때마다 재생. localStorage 등 상태 저장 없음.
- 사운드 없음. 임팩트는 화면 흔들림 + `navigator.vibrate` best-effort로.
- `prefers-reduced-motion: reduce`이면 인트로·흔들림 생략, 커버 즉시 표시.
- 첫 페인트부터 오버레이가 화면을 덮어야 함(커버가 먼저 번쩍 보이면 안 됨).
- 오버레이 배경 `#17140f`, 금색 포인트 `#d6b26e`(기존 `--accent:#b08d57` 계열). 폰트는 기존 `Gothic A1` / `Gowun Batang`.
- 타이밍은 한 곳(JS 상단 상수 + CSS animation-delay)에서 조절 가능하게. 최종 길이는 미리보기 후 사용자가 확정하므로 값은 명확히 주석 표기.
- 자동화된 JS 테스트 하네스가 저장소에 없음. 시각 기능이므로 검증은 로컬 서버로 띄워 브라우저에서 확인한다.

### 로컬 검증 방법 (모든 태스크 공통)

```bash
# 저장소 루트에서 정적 서버 실행
python -m http.server 8000
# 브라우저에서 http://localhost:8000/ 열기 (Playwright MCP 또는 수동)
```

DOM 확인 스니펫(Playwright `browser_evaluate` 또는 콘솔):
```js
!!document.querySelector('[data-opening]')                        // 오버레이 존재?
getComputedStyle(document.querySelector('[data-opening]')).zIndex // "9999"
document.querySelector('[data-opening]').classList.contains('io-done') // 종료됨?
```

---

### Task 1: 오버레이 마크업 + CSS 연출 (JS 없이 스스로 재생·소멸)

이 태스크만으로 인트로가 시각적으로 완성되고, JS가 하나도 없어도 오버레이가
마지막에 투명해져 페이지가 잠기지 않아야 한다.

**Files:**
- Modify: `index.html` — `<style>` 블록(31-40행 부근)에 keyframes/스타일 추가; grain div(54행)와 콘텐츠 래퍼(56행) 사이에 오버레이 마크업 추가; 콘텐츠 래퍼(56행)에 `data-stage` 속성 추가.

**Interfaces:**
- Produces (뒤 태스크가 의존):
  - 오버레이 요소: `[data-opening]` (콘텐츠 래퍼의 형제, `position:fixed; z-index:9999`)
  - 흔들림 대상: `[data-stage]` (모든 섹션을 감싼 콘텐츠 래퍼)
  - 흔들림 클래스: `.io-shake` (JS가 `[data-stage]`에 토글) → keyframe `introShake`
  - 종료 클래스: `.io-done` (JS가 `[data-opening]`에 추가 → `display:none`)
  - 타이틀 글자분할 대상/클래스: `.io-title`, `.io-title.io-split`, `.io-ch`, `.io-sp` (Task 4에서 사용)

- [ ] **Step 1: `<style>` 블록에 인트로 CSS 추가**

`index.html`에서 아래 줄(39행 부근):
```css
  @keyframes grainShift{0%{transform:translate(0,0);}100%{transform:translate(-4%,-3%);}}
```
바로 다음, `</style>` 앞에 삽입:
```css
  /* ---- gallery opening intro (전시장 개관) ---- */
  @keyframes introSpotlight{from{opacity:0; transform:translate(-50%,-50%) scale(.7);}to{opacity:1; transform:translate(-50%,-50%) scale(1);}}
  @keyframes introLine{from{transform:scaleX(0);}to{transform:scaleX(1);}}
  @keyframes introFadeUp{from{opacity:0; transform:translateY(14px);}to{opacity:1; transform:translateY(0);}}
  @keyframes introLetter{from{opacity:0; transform:translateY(10px);}to{opacity:1; transform:translateY(0);}}
  @keyframes introLift{from{opacity:1;}to{opacity:0; transform:scale(1.06);}}
  @keyframes introShake{
    10%{transform:translate(-3px,2px) rotate(-.4deg);} 20%{transform:translate(4px,-2px) rotate(.4deg);}
    35%{transform:translate(-4px,1px) rotate(-.3deg);} 50%{transform:translate(3px,2px) rotate(.3deg);}
    65%{transform:translate(-2px,-1px) rotate(-.2deg);} 80%{transform:translate(2px,1px) rotate(.15deg);}
    100%{transform:translate(0,0) rotate(0);}
  }
  /* ★ 타이밍 튜닝 지점(CSS): animation-delay 숫자. JS의 SHAKE_AT/END_AT과 맞출 것 */
  [data-opening]{position:fixed; inset:0; z-index:9999; background:#17140f; overflow:hidden;
    display:flex; flex-direction:column; align-items:center; justify-content:center; text-align:center;
    animation:introLift .5s ease 1.9s forwards;}
  [data-opening]::before{content:""; position:absolute; left:50%; top:38%; width:120vw; height:120vw;
    transform:translate(-50%,-50%) scale(.7); pointer-events:none; opacity:0;
    background:radial-gradient(circle at center, rgba(214,178,110,.30) 0%, rgba(214,178,110,.10) 30%, transparent 60%);
    animation:introSpotlight .9s ease .3s forwards;}
  [data-opening] .io-grain{position:absolute; inset:0; opacity:.5; pointer-events:none;
    background-image:radial-gradient(rgba(120,100,70,.06) 1px,transparent 1px); background-size:4px 4px;}
  [data-opening] .io-title{position:relative; font-family:'Gothic A1',sans-serif; font-weight:300; font-size:12px;
    letter-spacing:.46em; color:#d6b26e; opacity:0; animation:introFadeUp .8s ease .8s forwards;}
  [data-opening] .io-title.io-split{opacity:1; animation:none;}
  [data-opening] .io-title.io-split .io-ch{display:inline-block; opacity:0; animation:introLetter .5s ease forwards;}
  [data-opening] .io-title.io-split .io-sp{display:inline-block; width:.5em;}
  [data-opening] .io-line{position:relative; width:52px; height:1px; margin:16px 0 0; transform:scaleX(0);
    background:linear-gradient(90deg,transparent,#d6b26e,transparent); animation:introLine .6s ease .9s forwards;}
  [data-opening] .io-names{position:relative; margin-top:18px; font-family:'Gowun Batang',serif;
    font-size:22px; color:#efe6d4; opacity:0; animation:introFadeUp .8s ease 1.4s forwards;}
  [data-opening].io-done{display:none;}
  [data-stage].io-shake{animation:introShake .45s ease;}
  @media (prefers-reduced-motion: reduce){
    [data-opening]{animation:none; opacity:0; pointer-events:none;}
    [data-stage].io-shake{animation:none;}
  }
```

- [ ] **Step 2: 오버레이 마크업 추가**

`index.html` 54행 grain div 다음, 56행 콘텐츠 래퍼 앞에 삽입:
```html
  <!-- ============ OPENING INTRO (전시장 개관) ============ -->
  <div data-opening aria-hidden="true">
    <div class="io-grain"></div>
    <div class="io-title">THE EXHIBITION OF TWO</div>
    <div class="io-line"></div>
    <div class="io-names">이종호 &amp; 김희정</div>
  </div>
```

- [ ] **Step 3: 콘텐츠 래퍼에 `data-stage` 추가**

56행을:
```html
  <div style="position:relative; z-index:1; max-width:680px; margin:0 auto;">
```
로 변경:
```html
  <div data-stage style="position:relative; z-index:1; max-width:680px; margin:0 auto;">
```

- [ ] **Step 4: 브라우저에서 시각 확인**

로컬 서버로 `http://localhost:8000/` 로드. 기대:
- 로드 즉시 화면 전체가 어두운 전시장(#17140f)으로 덮임(커버가 먼저 보이지 않음).
- ~0.3s부터 중앙에 금빛 스포트라이트가 번짐, 타이틀/라인/이름 순차 등장.
- ~1.9–2.4s에 어둠이 페이드+살짝 확대되며 걷혀 커버가 드러남.
- 애니메이션 종료 후 오버레이는 `opacity:0; pointer-events:none`이라 커버 클릭/스크롤 가능(아직 `display:none`은 JS 몫).

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "인트로: 전시장 개관 오버레이 마크업/CSS"
```

---

### Task 2: `setupOpening(root)` — 수명관리 · 스킵 · 화면흔들림 · 스크롤잠금

**Files:**
- Modify: `index.html` — `componentDidMount`(555-570행)에 호출 추가; `setupReveal` 메서드 뒤(633행 부근)에 `setupOpening` 메서드 추가; `componentWillUnmount`(572-578행)에 정리 추가.

**Interfaces:**
- Consumes: `[data-opening]`, `[data-stage]`, `.io-done`, `.io-shake` (Task 1); `this._timers`(componentDidMount 558행에서 `[]`로 초기화됨); `this._revealKick`(Task 3에서 정의, 없으면 무시).
- Produces: `finish()`가 종료 시 `this._revealKick?.()` 호출(커버 리빌 트리거); 인스턴스 필드 `this._openingSkips`(정리용 리스너 목록).

- [ ] **Step 1: `componentDidMount`에 호출 추가**

569행 `this.setupGuestbook(root);` 다음 줄에 추가:
```js
    this.setupOpening(root);
```

- [ ] **Step 2: `setupOpening` 메서드 추가**

`setupReveal(root){...}` 메서드가 끝나는 `}` 다음(633행 부근)에 삽입:
```js
  // ---- opening intro (전시장 개관) lifecycle ----
  setupOpening(root) {
    const overlay = root.querySelector('[data-opening]');
    if (!overlay) return;
    const stage = root.querySelector('[data-stage]');
    const reduce = window.matchMedia && window.matchMedia('(prefers-reduced-motion: reduce)').matches;

    // ★ 타이밍 튜닝 지점(JS, ms). CSS의 introLift delay(1.9s)와 SHAKE_AT를 맞출 것.
    const SHAKE_AT = 1900; // 개관(어둠 걷힘) 순간
    const END_AT   = 2400; // 오버레이 완전 제거 = 커버 리빌 시작

    const el = document.documentElement;
    const prevOverflow = el.style.overflow;
    if (!reduce) el.style.overflow = 'hidden'; // 스크롤 잠금

    // 스킵 리스너: 스크롤이 잠겨도 동작하도록 wheel/touchmove/keydown/pointerdown 사용
    const skips = [];
    const bind = (t, type) => { t.addEventListener(type, onSkip, { passive: true }); skips.push([t, type]); };

    let done = false;
    const finish = () => {
      if (done) return; done = true;
      overlay.classList.add('io-done');
      el.style.overflow = prevOverflow; // 스크롤 잠금 해제
      skips.forEach(([t, type]) => t.removeEventListener(type, onSkip));
      if (this._revealKick) this._revealKick(); // 커버 리빌 시작
    };
    function onSkip() { finish(); }

    if (reduce) { finish(); return; } // 동작 줄이기: 즉시 커버

    const shake = () => {
      if (stage) {
        stage.classList.add('io-shake');
        this._timers.push(setTimeout(() => stage.classList.remove('io-shake'), 700));
      }
      if (navigator.vibrate) { try { navigator.vibrate([20, 40, 20]); } catch (e) {} }
    };

    bind(window, 'wheel'); bind(window, 'touchmove'); bind(window, 'keydown'); bind(overlay, 'pointerdown');
    this._timers.push(setTimeout(shake, SHAKE_AT));
    this._timers.push(setTimeout(finish, END_AT));
    this._openingSkips = skips; // unmount 정리용
    this._openingSkipFn = onSkip;
  }
```

- [ ] **Step 3: `componentWillUnmount`에 정리 추가**

573행 `if (this._gbUnsub) this._gbUnsub();` 다음에 추가:
```js
    if (this._openingSkips) this._openingSkips.forEach(([t, type]) => t.removeEventListener(type, this._openingSkipFn));
```
(타이머는 기존 573-574행 `this._timers` 정리 로직이 함께 처리.)

- [ ] **Step 4: 브라우저 검증**

`http://localhost:8000/` 로드 후:
- 인트로 재생 중 화면을 **탭**(또는 데스크톱에서 스크롤 휠) → 오버레이 즉시 사라지고 커버 표시(스크롤 가능).
- 건너뛰지 않고 두면 ~1.9s에 화면이 짧게 **흔들리고** ~2.4s에 오버레이가 `display:none`(`.io-done`) 처리됨.
- 확인 스니펫:
```js
document.querySelector('[data-opening]').classList.contains('io-done') // 종료 후 true
getComputedStyle(document.documentElement).overflow                     // 종료 후 '' 또는 원래값
```
- OS "동작 줄이기"(Playwright: `browser_resize` colorScheme와 별개로 reduced-motion 에뮬레이션, 또는 수동) 켠 상태로 로드 → 인트로/흔들림 없이 커버 즉시.

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "인트로: setupOpening 수명관리·스킵·화면흔들림"
```

---

### Task 3: 커버 리빌을 인트로 종료 후로 조율

인트로가 화면을 덮은 동안 커버가 뒤에서 먼저 드러나 버리면 "개관 후 작품이
하나씩" 연출이 낭비된다. 초기 리빌 검사를 `setupOpening.finish()`가 부르는
`this._revealKick`으로 넘긴다. 인트로 실패 시에도 콘텐츠가 숨은 채 멈추지
않도록 안전장치를 둔다.

**Files:**
- Modify: `index.html` — `setupReveal`(599-633행)의 초기 검사 부분(628-632행) 교체.

**Interfaces:**
- Consumes: `check`(setupReveal 내부 함수), `this._timers`.
- Produces: `this._revealKick` — 인자 없는 함수, 호출 시 즉시 + 지연 재검사로 뷰포트 내 `[data-reveal]`를 드러냄(멱등: 이미 드러난 요소는 `_revealed` 가드로 무시).

- [ ] **Step 1: 초기 리빌 트리거를 `_revealKick`으로 교체**

`setupReveal` 안의 아래 두 줄(628, 632행 부근):
```js
    requestAnimationFrame(check);
    // re-run the in-view check across load milestones (fonts/images settling,
    // late layout) — only reveals what's actually in view, preserving the
    // staged entrance for content further down the page.
    [120, 350, 800, 1500].forEach((ms) => setTimeout(check, ms));
```
를 다음으로 교체:
```js
    // 인트로(전시장 개관)가 끝난 뒤 커버가 순차로 드러나도록, 초기 리빌을
    // setupOpening.finish()가 호출하는 _revealKick으로 넘긴다.
    this._revealKick = () => { check(); [200, 600, 1200].forEach((ms) => setTimeout(check, ms)); };
    // 안전장치: 인트로 로직이 어떤 이유로 실패해도 콘텐츠가 숨은 채 멈추지 않게.
    this._timers.push(setTimeout(() => { if (this._revealKick) this._revealKick(); }, 3000));
```

- [ ] **Step 2: 브라우저 검증**

`http://localhost:8000/` 로드:
- 인트로가 걷힌 **뒤에** 커버 요소(타이틀/사진/이름/날짜)가 순차적으로 떠오름.
- 계속 아래로 스크롤 → 이후 섹션들도 진입 시 정상적으로 드러남(기존 동작 유지).
- 확인 스니펫(오버레이 종료 후):
```js
getComputedStyle(document.querySelector('[data-reveal]')).opacity // '1'
```

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "인트로: 커버 리빌을 개관 종료 후로 조율 + 안전장치"
```

---

### Task 4: 타이틀 글자 각인 (선택 강화 — 미리보기 후 유지/제거 결정)

타이틀을 한 글자씩 각인되듯 등장시켜 "우와"를 강화한다. JS가 없거나 이 태스크를
빼도 Task 1의 블록 페이드로 정상 동작한다(순수 강화).

**Files:**
- Modify: `index.html` — `setupOpening` 메서드 안, 스킵/타이머 바인딩 직전(`bind(window,'wheel')...` 줄 앞)에 글자 분할 추가.

**Interfaces:**
- Consumes: `overlay`(setupOpening 지역변수), `.io-title` 요소, CSS `.io-split`/`.io-ch`/`.io-sp`(Task 1).

- [ ] **Step 1: 글자 분할 로직 추가**

`setupOpening` 안, `const shake = () => {...};` 정의 다음이자 `bind(window, 'wheel');` 줄 앞에 삽입:
```js
    // 타이틀 글자 각인: 한 글자씩 stagger. 블록 페이드 대신 글자별 애니메이션으로 교체.
    const titleEl = overlay.querySelector('.io-title');
    if (titleEl) {
      const text = titleEl.textContent;
      titleEl.textContent = '';
      titleEl.classList.add('io-split');
      const BASE = 800, STEP = 45; // ms (★ 타이밍 튜닝: 첫 글자 지연 / 글자 간격)
      [...text].forEach((ch, i) => {
        const span = document.createElement('span');
        if (ch === ' ') { span.className = 'io-sp'; }
        else { span.className = 'io-ch'; span.textContent = ch; span.style.animationDelay = (BASE + i * STEP) + 'ms'; }
        titleEl.appendChild(span);
      });
    }
```

- [ ] **Step 2: 브라우저 검증**

`http://localhost:8000/` 로드:
- `THE EXHIBITION OF TWO`가 왼쪽부터 한 글자씩 금색으로 떠오름(블록 통짜 페이드가 아님).
- 확인 스니펫:
```js
document.querySelectorAll('[data-opening] .io-ch').length // > 0 (공백 제외 글자 수)
document.querySelector('[data-opening] .io-title').classList.contains('io-split') // true
```
- reduced-motion에서는 finish()가 먼저 반환하므로 분할이 실행되지 않아도 무방(오버레이 즉시 제거됨). 확인: reduced-motion 로드 시 커버 즉시 표시.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "인트로: 타이틀 글자 각인(선택 강화)"
```

---

## 최종 검증 (전체)

- [ ] 데스크톱 폭 / 모바일 폭(예: 375px)에서 각각 로드하여 전체 시퀀스 육안 확인, 길이감 튜닝(`SHAKE_AT`/`END_AT`/CSS delay/`BASE`·`STEP`).
- [ ] 탭 스킵 / 휠·터치 스킵 각각 즉시 종료.
- [ ] reduced-motion 켠 상태: 인트로·흔들림 생략, 커버 즉시.
- [ ] 인트로 종료 후 커버 순차 리빌 → 이후 스크롤 리빌 정상.
- [ ] `git push` (사용자 확인 후) → GitHub Pages 반영 확인.

## Self-Review 결과

- **스펙 커버리지:** 어두운 전시장(T1)·스포트라이트(T1)·타이틀 각인(T1 블록 + T4 글자)·헤어라인(T1)·이름(T1)·개관 리프트(T1)·화면흔들림+진동(T2)·스킵(T2)·스크롤잠금(T2)·방문마다 재생(상태저장 없음, 전체)·reduced-motion(T1 CSS + T2 JS)·FOUC 방지(T1 초기 마크업+CSS)·커버 리빌 조율(T3)·타이밍 튜닝 지점(T2·T4 상수, T1 delay) — 모두 태스크로 매핑됨.
- **플레이스홀더:** 없음(모든 스텝에 실제 코드/앵커/명령).
- **타입 일관성:** `finish()`/`onSkip`/`this._revealKick`/`this._timers`/`this._openingSkips`/`this._openingSkipFn`/클래스명(`io-done`·`io-shake`·`io-split`·`io-ch`·`io-sp`)이 태스크 간 일치.
