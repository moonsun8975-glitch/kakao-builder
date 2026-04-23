# 컴포넌트 규칙서

카카오톡 브랜드 메시지 캐러셀 빌더의 **반복 사용되는 컴포넌트**의 규칙과 구조.
새 템플릿을 만들 때 이 규칙을 따르면 일관성 유지 + 버그 감소.

**참고 문서**:
- `CLAUDE.md` - AI/개발자용 간결 가이드
- `docs/DESIGN_SPEC.md` - 제품 전체 기능 스펙
- `docs/TEMPLATE_CHECKLIST.md` - 새 템플릿 만들 때 체크리스트

---

## 📑 목차

1. [ImageUploader](#1-imageuploader) - 이미지 업로드
2. [ColorPicker](#2-colorpicker) - 컬러피커
3. [공통 폼 요소](#3-공통-폼-요소) - 입력 필드, 버튼, 드롭다운, 모달
4. [미리보기 카드](#4-미리보기-카드-구조) - 렌더링 구조
5. [공통 유틸리티](#5-공통-유틸리티) - 렌더링 흐름, 상태 관리
6. [디자인 토큰](#6-디자인-토큰) - CSS Variables

---

## 1. ImageUploader

모든 템플릿의 이미지 업로드 UI 공통 규칙.

### 레이아웃 구조

```
┌─────────────────────────────┐
│  [이미지 미리보기 영역]      │  ← 4:3 비율
│                             │
├─────────────────────────────┤
│ [또는 이미지 URL 붙여넣기]   │
├─────────────────────────────┤
│ ⚠️ URL 안내문                │
├─────────────────────────────┤
│ 이미지 조정 [초기화] [위치조정]│
└─────────────────────────────┘
```

### 이미지 타입

| 타입 | 동작 | 사용 상황 |
|------|------|----------|
| 연출컷 | 원본 그대로 | 일반 상품/라이프스타일 이미지 |
| 누끼컷 | remove.bg API로 배경 제거 | 제품 단독 이미지 |

### 상태별 UI

| 상태 | 미리보기 영역 표시 |
|------|------------------|
| 빈 상태 | 업로드 안내 + 아이콘 |
| 업로드 중 | 스피너 + "로딩 중..." |
| 완료 | 실제 이미지 (object-fit: cover) |
| 에러 | "이미지를 불러올 수 없습니다" |

### URL 입력 규칙

- placeholder: `또는 이미지 URL 붙여넣기`
- URL 입력 시 자동 처리
- 하단 안내문 (필수):

> ⚠️ URL에 따라 누끼 처리·자동 컬러 추출이 적용되지 않을 때도 있어요.
> 이때는 이미지를 우클릭하여 데스크톱에 저장한 뒤 파일로 업로드해 주세요.

### 위치 조정 팝업

```
┌──── 위치 조정 ────┐
│                  │
│  [드래그 영역]    │
│  (이미지 표시)    │
│                  │
├──────────────────┤
│   [취소] [적용]   │
└──────────────────┘
```

**작동 규칙**:
- 드래그로 이미지 위치 조정 (CSS transform)
- ESC 키: 취소
- 팝업 외부 클릭: 취소
- 확대/축소: 마우스 휠 또는 버튼
- 적용 시: `cards[selectedIdx].data._imgTransform` 저장

### 코드 네이밍 규칙

- 파일 업로드: `handleImgFile[템플릿ID]()`
  예: `handleImgFile_B()`, `handleImgFile_C()`
- URL 입력: `handleImgUrl[템플릿ID]()`
  예: `handleImgUrl_B()`, `handleImgUrl_C()`
- 누끼 처리: `removeBackground(imgUrl)` (공통)

---

## 2. ColorPicker

모든 템플릿의 컬러 입력 UI 공통 규칙.

### 레이아웃 구조 (접힌 상태)

```
라벨
⚫ # HEX입력 [자동]
```

### 레이아웃 구조 (펼친 상태)

```
라벨
⚫ # HEX입력 [자동]
┌─────────────────────┐
│ [Box - Saturation]  │
│                     │
│ [Slider - Hue]      │
│                     │
│ [스포이드]    [완료] │
└─────────────────────┘
이미지에서 자동 추출됨.
```

### 컴포넌트 구조

| 요소 | 기능 | 설명 |
|------|------|------|
| dot | 현재 색 시각화 | 20×20 원, HEX 값 배경 |
| HEX 입력 | 직접 입력 | 6자리 16진수만 허용 |
| [자동] 버튼 | 자동 추출 토글 | ON: 이미지에서 자동 / OFF: 수동 |
| Box | 채도/명도 선택 | iro.js Box 컴포넌트 |
| Slider | 색상(Hue) 선택 | iro.js Slider 컴포넌트 |
| [스포이드] | 화면 색 추출 | 네이티브 EyeDropper API |
| [완료] | 피커 닫기 | 선택 색 확정 |

### 핵심 규칙

1. **자동 플래그**: `_[키]Auto` 패턴
   - 예: `_btnColorAuto`, `_gradColorAuto`, `_bgColorAuto`
   - `true`: 이미지 변경 시 자동 재추출
   - `false`: 사용자가 수동 지정

2. **수동 변경 시**: 자동 플래그를 자동으로 `false`로 변경

3. **HEX 입력**: `0-9, a-f, A-F`만 허용, 6자리

4. **스포이드**:
   - 네이티브 `EyeDropper` API 사용 (Chrome/Edge/Opera)
   - Safari/Firefox는 alert로 안내
   - ESC로 취소

5. **한 번에 하나만**: 다른 컬러피커 열면 기존 것 자동 닫힘

### 사용 예시 (iroCP 함수)

```javascript
// 새 템플릿에서 컬러피커 사용
${iroCP({
  labelText: '버튼 컬러',
  key: 'btnColor',
  pid: 'iro_btnColor',
  edType: 'btn',              // 'btn' 또는 'grad' - 스포이드 활성화 여부
  autoKey: '_btnColorAuto',   // 자동 플래그 키
  hint: '이미지에서 자동 추출됨.'
})}
```

### 지원 컬러 키

| 키 | 자동 플래그 | 용도 |
|----|-----------|------|
| `btnColor` | `_btnColorAuto` | 버튼 배경색 |
| `_gradColor` | `_gradColorAuto` | 그라디언트 하단 색 |
| `bgColor` | `_bgColorAuto` | 카드 배경색 |

---

## 3. 공통 폼 요소

### 텍스트 입력 필드

```
라벨
[입력 필드              ]  X/Y
```

**규칙**:
- 글자 수 카운터: 우측 하단 `현재/최대`
- 최대 초과: HTML `maxlength`로 입력 차단
- 빈 값 허용 여부는 템플릿별로 결정
- 색상: 기본 검은색, 카운터는 `--text-sub`

### 버튼 스타일

| 종류 | 배경색 | 글자색 | 용도 |
|------|--------|--------|------|
| Primary | `var(--y200)` | `var(--kakao-brown)` | 주요 액션 (저장, 적용) |
| Secondary | `var(--border)` | `var(--text-sub)` | 보조 액션 (취소) |
| Danger | `var(--red)` | `white` | 위험 액션 (삭제) |
| Icon | 투명 | 아이콘 색 | 작은 기능 버튼 |

### 드롭다운/셀렉트

```
라벨
[현재 값          ⌄]
```

**규칙**:
- Custom select 사용 (HTML `<select>` 대신)
- 클릭 시 목록 펼침
- 선택 시 자동 닫힘
- 외부 클릭 시 닫힘
- ESC 키로 닫힘
- 다른 드롭다운 열면 기존 것 닫힘

### 모달/팝업 공통 규칙

**구조**:
- 배경 오버레이: `rgba(0,0,0,0.5)`
- 중앙 정렬
- 최대 너비 제한 (모바일 대응)

**닫는 방법 3가지 (필수)**:
1. ESC 키
2. 오버레이 클릭
3. 닫기 버튼 (X 또는 취소)

---

## 4. 미리보기 카드 구조

### DOM 구조 (필수)

```html
<div class="card-wrap [active]">
  <div class="card-render">
    <div class="card-inner">
      <img id="cardImg_N" src="...">
      <!-- 텍스트/배지 레이어 (absolute positioning) -->
      <div class="card-text">...</div>
      <div class="card-badges">...</div>
      <div class="card-button">...</div>
    </div>
  </div>
</div>
```

**핵심**:
- `active` 클래스: 현재 선택된 카드
- `cardImg_N`: 이미지 ID (N은 카드 인덱스)

### 렌더링 규칙

- **화면 크기**: 375×500 (모바일 기준)
- **실제 출력**: 750×1000 (2x 해상도)
- **배경 이미지**: `object-fit: cover`, `object-position: 50% 50%`
- **텍스트 레이어**: absolute positioning, 이미지 위에 배치
- **반응형**: 화면 크기에 따라 등비 스케일

### 카드 번호 배지 (하단)

```
[번호] 템플릿ID · 템플릿이름
```

예: `① A · 브랜드형`, `② C · 혜택형`

---

## 5. 공통 유틸리티

### 렌더링 흐름

```
사용자 입력
  ↓
cards[selectedIdx].data 업데이트
  ↓
renderAll()
  ├→ renderSidebar()  ← 왼쪽 카드 목록
  ├→ renderPreview()  ← 중앙 미리보기
  └→ renderEditor()   ← 오른쪽 편집 폼
```

### 상태 관리 (전역 변수)

| 변수 | 용도 |
|------|------|
| `cards` | 모든 카드 데이터 배열 |
| `selectedIdx` | 현재 선택된 카드 인덱스 |
| `activeColorPicker` | 현재 열린 컬러피커 ID (1개만) |
| `_iroInstances` | iro.js 인스턴스 저장 (pid → instance) |

### HTML Escape

사용자 입력은 반드시 `esc()` 함수로 이스케이프:

```javascript
const safeText = esc(userInput);  // XSS 방지
```

### 색 관련 유틸

- `resolveBgColor(data)`: 배경색 결정 (수동 > 자동 > 기본값)
- `resolveBtnColor(data)`: 버튼색 결정
- `resolveGradColor(data)`: 그라디언트색 결정

### 자동 추출 트리거

이미지가 변경되면 자동 추출이 켜진 컬러 키는 재계산:

```javascript
// 이미지 변경 후
if (data._btnColorAuto) {
  data.btnColor = extractDominantColor(img);
}
```

---

## 6. 디자인 토큰

CSS Variables로 정의되어 있음 (`:root` 셀렉터).

### 컬러

| 변수 | 값 | 용도 |
|------|-----|------|
| `--y100` | 연한 노랑 | 배경, 하이라이트 |
| `--y200` | 카카오 노랑 | 강조 버튼 |
| `--kakao-brown` | 카카오 갈색 | 강조 텍스트 |
| `--text-main` | 진한 회색 | 본문 텍스트 |
| `--text-sub` | 중간 회색 | 보조 텍스트, 카운터 |
| `--border` | 연한 회색 | 기본 테두리 |
| `--border-strong` | 진한 회색 | 강조 테두리 |
| `--red` | 빨강 | 위험, 에러 |

### 타이포그래피

- 기본 폰트: `var(--font)` = Pretendard
- 본문: 14px
- 라벨: 12px
- 글자 수 카운터: 11px

---

## 🎯 새 템플릿 만들 때

이 규칙서에서 각 섹션의 규칙을 먼저 읽고 시작하세요.
구체적인 단계별 가이드는 `docs/TEMPLATE_CHECKLIST.md`를 참고.
