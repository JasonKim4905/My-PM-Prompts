# 홈닷 ERP 분석 자료 작성 표준

> 사용법: "**{화면명} 분석 자료 만들어줘**" 또는 "**{프로그램코드} 분석 자료**"
> → 아래 4종 산출물이 세트로 생성됩니다.

---

## 산출물 4종

| # | 파일명 | 내용 |
|---|---|---|
| 1 | `{code}_merged_complete.csv` | 필드 분석 (16컬럼) |
| 2 | `{code}_dataflow_diagram.md` | 데이터 흐름도 (Mermaid flowchart) |
| 3 | `{code}_erd.md` | ERD (Mermaid erDiagram) |
| 4 | `{code}_erd_detailed.md` | ERD 상세 (필드정의 + 계산로직 + 미확인사항) |

---

## 추출 절차 (실측 기반)

### 1단계: 화면 열기
```javascript
_X.OpenSheet('MR01010');   // 프로그램코드
```
- 접속: `https://dev.homedot.co.kr/main/actionMain` (Claude in Chrome)
- 화면은 `mdi_0`, `mdi_1`, `mdi_2`... iframe에 순차적으로 열림

### 2단계: 대상 iframe 식별
```javascript
const ifr = document.querySelectorAll('iframe');
let mdiIds = [];
ifr.forEach(f => { if (f.id && f.id.startsWith('mdi_')) mdiIds.push(f.id); });
// 각 mdi에서 columns_dg_1 확인 → 편집가능 사용량 필드로 화면 판별
```

### 3단계: 필드 메타데이터 추출
```javascript
const win = document.getElementById('mdi_N').contentWindow;
const cols = win.columns_dg_1;   // 주 그리드
cols.slice(0, 30).map(c => [
  c.fieldName,
  (c.header?.text || '').replace(/\|/g, '/'),
  c.must_input || '',            // 필수여부
  c.isKey || '',                 // 키
  c.visible === true ? 'Y' : 'N',      // 화면표시
  c.readonly === true ? 'Y' : 'N',
  c.editable === true ? 'Y' : 'N',     // 사용자입력
  (c.editor?._editorType === 'DropDownEditor' || c.lookupDisplay) ? 'dropdown' : '',
  c.formulaChg === true ? 'Y' : '',    // 계산필드
  c.maxLength || ''
].join('¶')).join('\n');
```
> ⚠️ 응답이 길면 차단되므로 `slice()`로 25~30개씩 나눠 추출

**보조 그리드**: `columns_dg_2`, `columns_dg_12` 등도 존재하면 함께 확인

### 4단계: 계산식 검증
```javascript
// 필드 대입 관계
let s = win['setItemCalcResult'].toString();
s.split(/[;\n]/).map(l => l.trim())
 .filter(l => /a_data\.[a-z_]+\s*=/.test(l) && l.length < 140 && !/http|param|\.do/i.test(l))
 .join('\n');

// 연산식
s.split(/[;\n{}]/).map(l => l.trim())
 .filter(l => /[a-z_.]+\s*=\s*.*[-+*\/]/.test(l) && l.length < 140 && !/http|param|\.do/i.test(l))
 .join(' ||| ');
```
**주요 함수**: `mcf_calcUseQty`(사용량), `pf_calcTotalPrice`(합계), `setItemCalcResult`(최종대입), `calcQtyTotalSum`(합산), `pf_calc*`(화면별)

> ⚠️ 함수 소스에 URL/쿠키 문자열이 포함되면 응답 차단됨 → 필터링 후 추출

---

## CSV 16컬럼 규격

| 컬럼 | 채우는 근거 |
|---|---|
| 프로그램코드 | 고정 |
| 화면명 | 고정 |
| 필드명_영문 | `fieldName` (실측) |
| 필드명_한글 | `header.text` (실측) |
| 필드_그룹 | 헤더/필드명 기반 분류 |
| 데이터타입 | 필드명 패턴 추론 |
| 필드속성 | Key/Input/Output/Calculate ← `isKey`/`editable`/`formulaChg` |
| 필수여부 | `must_input` |
| 사용자입력 | `editable`/`editor` |
| 값_범위_또는_포맷 | 업무 지식 (일부 추정) |
| 검증규칙 | 소스 검증 로직 |
| 계산식 | JS 함수 소스 (실측) |
| 한전연동 | 전기만 해당 |
| 관련_설정화면 | 드롭다운 소스 화면 |
| 설명 | 헤더 + 업무 맥락 |
| 비고 | `visible`/`dropdown`/`maxLength` |

---

## 검침 모듈 공통 패턴 (참고)

모든 검침 화면이 공유하는 구조:

```
지침: bef_meter / cur_meter
사용량: use_*_qty = round(cur - bef), 롤오버 시 round(maxQty + cur - bef)
통계: bef_use_qty, compare_use_qty, month3_avg_qty, year_avg_use_qty, pre_ym_use_qty
조견: {접두}_apply, {접두}_num, {접두}_apply_log, {접두}_num_log
상태: trouble_yn(고장), error_yn, error_remark, del_ny, all_check
감사: row_input_date/emp_no/ip, row_update_date/emp_no/ip
```

**접두사**: 전기 `el_` / 수도 `wt_` / 온수 `wh_` / 정수 `wf_` / 가스 `gs_` / 난방 `ht_`

**요금**: 서버 `POST /mighty/ajax` → `_calcResult` (조견표 기반)
→ 구간별 단가는 클라이언트에 없음. 설정(SE) 조견표등록 화면 확인 필요

---

## 원칙

1. **실측 우선** — 필드명·속성·계산식은 반드시 소스에서 확인
2. **추정 명시** — 테이블명, FK 관계, 값 범위 등 확인 불가한 것은 "추정" 표기
3. **미확인 사항 기록** — ERD 상세 문서 하단에 확인 못 한 항목 명시
4. **기존 파일 덮어쓰기 시 고지** — 노션에 이미 import된 파일이면 교체 필요를 알림

---

## 완료 현황 (검침 모듈)

| 화면 | 필드 | CSV | 흐름도 | ERD | ERD상세 |
|---|---|---|---|---|---|
| MR01010 전기검침 | 83 | ✅ | ✅ | ✅ | ⚠️ 구버전 |
| MR02010 수도검침 | 55 | ✅ | ✅ | ✅ | ✅ |
| MR03010 온수검침 | 59 | ✅ | ✅ | ✅ | ✅ |
| MR09010 정수검침 | 55 | ✅ | ✅ | ✅ | ✅ |
| MR04010 가스검침 | 47 | ✅ | ✅ | ✅ | ✅ |
| MR05010 난방검침 | 54 | ✅ | ✅ | ✅ | ✅ |

**미착수**: 음식물(MR06010), 복합검침, 한전연동(MR08/09 계열), 사용량조회·세대조회 계열
**타 모듈**: 부과(TM) 39 / 회계(AC) 38 / 설정(SE) 29 / 수납(SR) 22 / 관리자(BI) 14 / 정산(TN) 5
