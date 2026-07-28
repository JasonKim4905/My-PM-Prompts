# Modulor 디자인 시스템 — Claude 작업용 마스터 가이드

> 이 문서는 **claude.ai 공유 프로젝트의 지식(Knowledge)에 등록**해서 사용합니다.
> 기획자가 Claude에게 화면 제작을 요청하면, Claude는 이 문서의 규칙에 따라 피그마 라이브러리의 **실제 컴포넌트와 변수**로 화면을 조립합니다.
> 모든 값은 2026-07-27 기준 피그마 마스터 파일에서 직접 추출·검증되었습니다. **충돌 시 항상 피그마 파일이 우선입니다.**

---

## 1. 소스 정보 (Source of Truth)

| 항목 | 값 |
|---|---|
| 마스터 파일 | 🧩 모듈러 🧩 (CORE_작업 중) — **읽기 전용, 조회 용도만** |
| fileKey | `OHu3YQ55gYCdOlTH0y9H4Q` |
| 산출물 파일 (플레이그라운드) | MODULOR_PLAYGROUND — https://www.figma.com/design/o5hH9ZvvDFHxCYNFh3fvF9/MODULOR_PLAYGROUND (fileKey: `o5hH9ZvvDFHxCYNFh3fvF9`) |
| 라이브러리 게시 | ✅ 게시됨 — `search_design_system`으로 검색 가능 |
| libraryKey | `lk-1a70cbf7f79ed07301f497481c8a344aa2096686cdf181ae5d201b8fb240b595faee8c478dfe204912f72552c925a292ee2d0842e5909fdf81d1fe98ff97e7ba` |
| 폰트 | Pretendard (Regular / Medium / SemiBold / Bold) |
| 아이콘 | Google Material Symbols 기반 자체 아이콘 세트 |

---

## 2. 변수 컬렉션 맵 (피그마 실측)

| 컬렉션 | 변수 수 | 모드 | 역할 |
|---|---|---|---|
| 🌈 Theme | 208 | 🧩 Modulor · 🏠 HomeDot · 🗺️ RA · 📑 RD · 🏢 RTB | **시맨틱 레이어** — 제품 전환의 핵심 |
| ⛓️ Modulor_Default | 23 | Default · Contrast | 뉴트럴 표면/텍스트/보더 |
| 🎨 1~5 Palette | 84 / 90 / 210 / 80 / 91 | 제품별 1모드 | 원시 팔레트 (직접 사용 금지) |
| 🅰️ Typography | 25 | Web · Mobile | 폰트 크기/행간/굵기 |
| ⚫️ Radius | 7 | Modulor | 모서리 반경 |
| 📏 Spacing_Web / _Mobile | 18 / 16 | Modulor | 4px 그리드 간격 |
| 🖥️ Responsive | 17 | XL~XXS 6단계 | 브레이크포인트/그리드 |
| 💡 Utility | 9 | Mode 1 | 플레이스홀더 문자열 (라벨/인풋 기본 텍스트) |

**그림자는 변수가 아니라 Effect Style**로 관리됨: `Elevation-Small / Medium / Large / X Large`, `focus`, `Destructive-focus`

> ⚠️ **표면 컬러 규칙 (실제 프로덕트 기준으로 검증됨)**:
> - **페이지 기본 배경 = `bg/layer/default`(#FFFFFF)** — 캔버스는 흰색이 기본입니다.
> - **필터/구분 패널 = `bg/tile`(#F7F8F9, 🌈 Theme 컬렉션)** — 흰 페이지 위 연회색 패널, 보더 없이 사용.
> - 그 외 라이트 표면: `bg/layer/fill`(#F3F4F5), `bg/layer/contrast`(#DCDEE3).
> - **`bg/neutral/*`은 이름과 달리 다크 계열 fill** (`default` = #2A3038, `muted` = #555D6D) — 라이트 UI 배경에 절대 사용 금지.
> - **텍스트 컬러는 `fg/text/*`만 사용** (`default`/`alternative`/`assistive`/`disable`). **`fg/secondary`는 텍스트가 아니라 브랜드 세컨더리 컬러**다 (HomeDot=그린, RD=레드, RA=블루) — 본문에 물리면 글자가 브랜드색으로 물든다.
> - **Contrast 모드의 실체**: ⛓️ Modulor_Default의 `fg/text/*` 4개를 한 단계 진하게 만드는 모드 (예: 본문 gray.800→gray.900). HomeDot·RTB가 항상 Contrast를 쓰는 이유.

---

## 3. 제품 모드 (🌈 Theme 컬렉션 모드 전환)

새 화면 프레임에 `setExplicitVariableModeForCollection`으로 Theme 모드를 지정하면 전체가 해당 제품 컬러로 전환됩니다.

| 제품 | Theme 모드명 | bg/primary 실측값 |
|---|---|---|
| Modulor (하우스) | 🧩 Modulor | `#7F56D9` (primary/600) |
| HomeDot | 🏠 HomeDot | `#5534E1` (Brand/primary/600) |
| R.A | 🗺️ RA | `#5E44CB` (Brand/primary/700) |
| R.D | 📑 RD | `#01B761` (Brand/primary/500) |
| RTB | 🏢 RTB | `#0F62FE` (primary/500) |

> **⛓️ Modulor_Default 모드 동시 지정 (필수)**: 최상위 프레임에 🌈 Theme 모드를 지정할 때 ⛓️ Modulor_Default 컬렉션 모드도 함께 명시 지정한다 — **🏠 HomeDot과 🏢 RTB는 항상 `Contrast`**, 🧩 Modulor · 🗺️ RA · 📑 RD는 `Default`.

---

## 4. 파운데이션 실측값 (⚠️ 기존 문서 오류 정정 포함)

### Radius (⚫️ Radius)
| 토큰 | 값 | 비고 |
|---|---|---|
| None | 0 | |
| XS | **4** | ⚠️ 구 문서에 2로 오기재 |
| SM | 6 | |
| MD | 8 | |
| LG | 16 | |
| XL | **24** | ⚠️ 구 문서에 누락 |
| F&C | **999** | ⚠️ 구 문서에 9999로 오기재 |

### Spacing_Web (4px 그리드, 전체 18단계)
`Space-0`=0, `050`=2, `100`=4, `125`=6, `150`=8, `200`=12, `250`=16, `300`=20, `350`=24, `400`=28, `450`=32, `500`=36, `550`=40, `600`=44, `650`=48, `700`=56, `750`=64, `800`=80
> ⚠️ 구 문서는 9단계만 기재. `Space-125`(6px) 등 중간 단계 존재.

### Typography — 텍스트 스타일 네이밍 (⚠️ 구 문서와 체계가 다름)
구 문서의 "Display/Heading/Label/Caption" 명칭은 실제 피그마에 **존재하지 않습니다.** 실제 스타일(총 44개):

| 스타일 | 크기/행간 | 굵기 |
|---|---|---|
| Web/Headline/Headline 1~6 | 56/90 · 48/77 · 40/64 · 32/51 · 28/45 · 24/38 | Bold |
| Web/Subtitle/Subtitle 1~5 | 20/32(B) · 16/26(B) · 16/26(SB) · 16/26(M) · 14/22(SB) | 표기 참조 |
| Web/Body/Body 1~3 | 16/26 · 14/22 · 12/19 | Regular (+Bold/Medium/Link 변형) |

폰트 크기 변수: `font/size/xxs(12)~5xl(56)`, 행간은 Web/Mobile 모드별 상이 (예: 5xl 행간 Web 90 / Mobile 68)

---

## 5. 컴포넌트 사용 규칙

1. **항상 게시된 라이브러리 컴포넌트를 인스턴스로 사용** — `search_design_system`(위 libraryKey로 스코프 제한) → `importComponentByKeyAsync` → 인스턴스 배치. 도형으로 컴포넌트를 그려 흉내내지 않는다.
2. **동명 컴포넌트가 여러 세대 존재** (Button ×2, table cell ×2 등). `_New📌` 접미사 또는 `updatedAt`이 최신인 세트를 우선 사용한다. `Legacy` 페이지의 컴포넌트는 사용 금지.
3. **웹/모바일 구분**: 모바일 화면은 `_Mobile` 접미사 컴포넌트(App bar, Button_Mobile 등)와 Typography `Mobile` 모드, `📏 Spacing_Mobile`을 사용한다.
4. **컬러는 시맨틱 토큰만 바인딩** (`bg/*`, `fg/*`, `border/*`). Palette 컬렉션 직접 참조·hex 하드코딩 금지 — 제품 모드 전환이 깨진다.
5. **상태 컬러는 색+텍스트 병기** (접근성 규칙: 색상만으로 의미 구분 금지).
6. **테이블 세로 구분선은 `border/divider-weight` 변수로 제어** — 🌈 Theme 컬렉션의 숫자 변수로, 제품 모드에 따라 0(없음)/1(있음)이 자동 적용된다. 셀에 보더를 임의로 추가·제거하지 않는다.
7. UI 카피는 한국어 우선, `-세요` 명령형, 이모지 사용 금지. 플레이스홀더는 💡 Utility 변수 값 참조.
8. **CORE 라이브러리만 사용** — 검색은 반드시 위 libraryKey로 스코프 제한. "공유용_작업중", "CORE_RTB_뷰어용", "HomeDot(test)" 등 유사 명칭 라이브러리는 레거시이므로 절대 사용 금지.
9. **접미사 없는 이름이 웹, `_m`/`_Mobile` 접미사가 모바일** (예: badge = 웹, badge_m = 모바일). 검색에서 컴포넌트가 안 보이면 "없다"고 단정하지 말고 마스터 파일의 해당 페이지를 직접 조회해 재확인한다.

---

## 6. 제품별 앱 셸(GNB·사이드네브) 스펙

**원칙: 셸은 스펙대로 고정, 콘텐츠만 새로 디자인한다.** 화면 요청이 오면 해당 제품의 셸을 아래 스펙대로 먼저 조립하고, 그 안의 콘텐츠 영역만 요청에 맞게 만든다. 셸 구성(GNB 요소, 사이드네브 스타일)을 임의로 바꾸지 않는다. RA·RD·RTB는 전용 셸 컴포넌트가 아직 없으므로, CORE의 기존 컴포넌트 + 해당 제품 Theme 모드로 **근사 조립**한다 (목표: 실제 제품과 "비슷하게 보이기").

### 🏠 HomeDot (실제 프로덕트 캡쳐 기준 검증됨)
```
┌ GNB: headerNav — 로고 + "서비스 바로가기" 드롭다운(좌) /
│      라벨 프리픽스형 셀렉트 2개("회사 | 값 ▾", "단지 | 값 ▾") + 계정 드롭다운(우)
├ 탭 바: 흰 배경 브라우저 탭형 — 🏠 홈 고정 탭 + 닫기 가능한 페이지 탭(활성 탭 상단 라운드) + 우측 "전체 창 닫기"
├ 좌측: sideNav — 아이콘+라벨 세로 메뉴, 하단에 이용약관·개인정보처리방침·카피라이트 영역
└ 콘텐츠: 브레드크럼 → 페이지 타이틀(+우측 액션: 그레이 보조 + Primary 주 액션) → 필터 패널(bg/tile) → 본문
```
**버튼 크기 위계 (필수)**: **페이지 주 액션(타이틀 행 우측: 등록·배포계획 등)이 화면에서 항상 최우선** — Primary + Large(40px). 타이틀 행 보조 버튼은 Large 아웃라인. 필터의 조회는 다크(button/secondary)·초기화는 그레이지만 **기본 크기(32px)로, 주 액션보다 크면 안 된다.** 피그마·프로토타입 공통 규칙.
**테이블 규칙**: 헤더 셀 사이 세로 구분선은 **항상 표시**, 바디 행 구분선만 `border/divider-weight`(HomeDot=0)로 제어. 진행상태 컬럼은 프로그레스 바 + 완료율 % + 상태 도트(대기/진행중/완료/제외) 패턴. 컬럼이 넘치면 `.mdl-tablewrap` 내부 스크롤로 처리하고 페이지 프레임을 깨지 않는다.

**프로토타입 셸 표준 마크업 (그대로 복사해서 시작 — 임의 변형 금지)**
```html
<body data-product="homedot">  <!-- body에 여백·외곽 프레임 금지, 풀블리드 -->
<nav class="mdl-gnb mdl-gnb--grouped">
  <div class="mdl-gnb__group">
    <span class="mdl-logo-img mdl-logo-img--homedot"></span>
    <button class="mdl-drop mdl-drop--plain">서비스 바로가기 ▾</button>
  </div>
  <div></div>
  <div class="mdl-gnb__group mdl-gnb__group--right">
    <button class="mdl-drop mdl-drop--labeled"><span class="lbl">회사</span> 회사명 ▾</button>
    <button class="mdl-drop mdl-drop--labeled"><span class="lbl">단지</span> 선택 ▾</button>
    <button class="mdl-drop mdl-drop--plain">사용자명 ▾</button>
  </div>
</nav>
<div class="mdl-tabbar">
  <span class="mdl-ptab">홈</span>
  <span class="mdl-ptab is-active">페이지명 <span class="x">×</span></span>
  <span class="mdl-tabbar__end">전체 창 닫기</span>
</div>
<div class="mdl-body">
  <aside class="mdl-sidenav">
    <div class="mdl-sidenav__menu">
      <div class="mdl-sidenav__group">그룹명</div>
      <a href="#">메뉴</a> <a class="is-active" href="#">현재 메뉴</a>
    </div>
    <div class="mdl-sidenav__foot"><a href="#">이용약관</a><a href="#">개인정보처리방침</a>© RSQUARE FM Inc.</div>
  </aside>
  <main class="mdl-main">
    <div class="mdl-crumb"><span>홈</span><span>›</span><b>페이지명</b></div>
    <div class="mdl-titlerow"><h1 class="mdl-h6">페이지명</h1>
      <div><button class="mdl-btn mdl-btn--lg mdl-btn--secondary-outline">보조 액션</button>
           <button class="mdl-btn mdl-btn--lg">주 액션</button></div></div>
    <div class="mdl-panel"><!-- 필터: 조회는 .mdl-btn--dark(32px), 초기화는 .mdl-btn--gray --></div>
    <div class="mdl-tablewrap"><table class="mdl-table"><!-- ... --></table></div>
  </main>
</div>
</body>
```

### 🧩 Modulor (하우스 기본 — 셸 미지정 요청의 기본값)
```
┌ GNB: headerNav 기본형 — 로고 + 워크스페이스 드롭다운(좌) / 계정(우)
├ 좌측: sideNav 아이콘 레일형
└ 콘텐츠: Section Header 패턴(브레드크럼+타이틀+액션) → 본문
```

### 🗺️ RA — RSQUARE Analytics (캡쳐 분석 기준 · CORE 컴포넌트로 근사 조립)
```
┌ GNB(56px): 로고 "RSQUARE Analytics"(좌) / 중앙 텍스트 메뉴: 탐색·테마·비교·기업·통계분석·마켓리포트
│      (활성 메뉴만 primary 컬러) / 우측: 계정 아이콘 · ⋯ · 단위 토글(평|KRW|한국어) · 로그아웃
├ 사이드네브 없음 — 전 화면 GNB 단독
├ 탐색(지도) 화면: GNB 아래 툴바(검색 인풋 + 데이터소스 세그먼트 + 우측 필터 pill들 + 필터 Primary 버튼)
│      → 본문 = 좌측 목록 패널(~360px, 총 건수+정렬+카드 리스트) + 풀블리드 지도(우측 플로팅 툴 레일)
├ 분석/비교 화면: 좌측 레일(목록 or 분석 항목+피벗 빌더) + 우측 콘텐츠 카드 스택
└ 리포트 화면: 사이드 없음, 상단 고정 헤더 카드(타이틀+탭 pill+내려받기 버튼) + 섹션 카드 콘텐츠
```
근사 조립: headerNav 기본형 베리언트에서 셀렉트 없이 텍스트 메뉴 구성, 활성 메뉴는 fg/primary. 콘텐츠는 라운드 큰 카드(Radius LG) 위주. Theme 모드 = 🗺️ RA.

### 📑 RD — RSQUARE DataHub (캡쳐 분석 기준 · CORE 컴포넌트로 근사 조립)
```
┌ GNB(56~60px): 로고 "RSQUARE DataHub"(좌) / 중앙 텍스트 메뉴: 통합·개별·엑셀·지도·내역·변동알림·관리
│      / 우측: 상태 pill(다크 배경 "인터넷등기소 원활") + 알림(벨+배지) + 내 정보
├ 사이드네브 없음, 콘텐츠는 중앙 정렬 단일 컬럼(좌우 넉넉한 여백)
├ 홈(통합): primary 그라디언트 히어로 밴드(중앙 타이틀 + 흰색 검색 카드: 탭 4개 + 대형 검색 인풋)
│      → 아래 중앙 컬럼: 최근 신청 테이블 · 공지 리스트 + 알림 요약 카드
├ 업무 화면(개별 등): 페이지 타이틀 → 검색방법 탭 카드 → 필터 행(셀렉트들 + 주소 인풋 + 초기화/조회)
│      → 결과 영역(빈 상태 일러스트 포함)
└ 지도 화면: 풀블리드 필지 지도 + 좌측 플로팅 패널 + 상단 검색/필터 + 하단 중앙 다크 토스트 바
공통: 하단에 파트너 로고 밴드 + 회사 정보 푸터 상시 노출
```
Theme 모드 = 📑 RD (그린 #01B761이 자동 적용됨).

### 🏢 RTB — 전용 패턴 라이브러리 존재 ⭐ (피그마 실측 기준)
RTB는 예외적으로 **RTB_UX 패턴 라이브러리를 사용한다** (게시됨 · 활발히 갱신 중):
- fileKey: `qW9n1v48EFgX5arMboBPxm` / libraryKey: `lk-1ec39a38528d46258d44650d5504de9aacd0e3013cb22187cafd7eb2df49ec3aa19b1d49ca5a32253cd865d481ae373322d4104967d4d7ec23ffdc6abc91c32e`
- ※ "CORE만 사용" 규칙의 유일한 예외. RTB 셸·테이블은 이 라이브러리를, 그 외 공용 컴포넌트는 CORE를 사용.
```
┌ Header(56px, 컴포넌트 "Header"): 로고+브레드크럼(좌) / 중앙 header_item_menu_1depth 메뉴 7개+구분선+1개
│      / 우측: 사용자명 드롭다운 + header_item_notification
├ 목록 화면: "Page tab"(56px) → "FilterBar"(134px: Segmented Control + 우측 버튼 2개 /
│      Dropdown/filter 칩 다수 + 초기화·필터 저장·필터 불러오기) → "Table Toolbar"
│      → 테이블(TableComponent_HeaderCell 40px / TableComponent_RowCell 62px) → Pagination-full
└ 상세 화면: "PageHeader"(56px) → 좌측 336px 정보 레일(요약 카드 + 연결 정보 카드 + Index info_cardui 스택)
       + 우측 콘텐츠(섹션 타이틀+수정 버튼 → detailveiw_infocard_items_row 2컬럼 그리드,
       Section Tab_switch로 하위 전환, 내장 테이블)
```
Theme 모드 = 🏢 RTB.

---

## 7. 기획자용 — 요청하는 법

Claude에게 이렇게 요청하면 됩니다:

> "**[제품명]** 테마로 **[화면 설명]** 화면을 피그마에 만들어줘. 파일: [작업할 피그마 파일 링크]"
>
> 예시: "RD 테마로 매물 목록 필터 + 테이블 화면을 피그마에 만들어줘."

- 제품명(Modulor/HomeDot/RA/RD/RTB)을 반드시 명시 — 없으면 Claude가 확인 질문
- 웹/모바일 여부 명시
- 대상 피그마 파일 링크 첨부 (편집 권한 필요)

## 8. Claude 작업 절차 (요약)

1. 대상 파일 확인 → 없으면 새 파일 생성 제안
2. `search_design_system`으로 필요한 컴포넌트 검색 (libraryKey 스코프)
3. 프레임 생성 → Theme 컬렉션 모드를 요청된 제품으로 지정
4. 라이브러리 컴포넌트 인스턴스로 조립, 간격은 Spacing 변수 바인딩
5. 텍스트는 Web/Mobile 텍스트 스타일 적용, 실데이터 느낌의 한국어 카피 작성
6. 스크린샷으로 결과 검증 후 링크 공유
