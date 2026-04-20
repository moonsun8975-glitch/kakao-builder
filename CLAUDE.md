# CLAUDE.md

> 카카오톡 브랜드 메시지 캐러셀 빌더 — AI/개발자 참고 문서
> 상세 디자인 스펙은 [`docs/DESIGN_SPEC.md`](./docs/DESIGN_SPEC.md) 참조

---

## 프로젝트 개요

카카오톡 브랜드 메시지 캐러셀 템플릿 사내 빌더. CRM 캠페인 담당자가 디자이너 없이 카드를 제작할 수 있게 해주는 웹 툴.

- **형태**: 단일 HTML 파일 (`public/kakao-crm-builder.html`)
- **스택**: 순수 HTML/CSS/JS (프레임워크 없음)
- **외부**: iro.js (컬러피커), remove.bg API (누끼)
- **캔버스**: 750×1000px, 미리보기 0.5배율

---

## 파일 구조

```
kakao-builder/
├── CLAUDE.md
├── docs/DESIGN_SPEC.md          ← 디자인 스펙 상세
├── api/
├── public/
│   ├── assets/
│   ├── index.html
│   ├── kakao-crm-builder.html       ← 메인 빌더
│   └── kakao-crm-builder.bak.html   ← 백업
└── vercel.json
```

---

## 개발 환경

### 3-터미널 워크플로우

**터미널 1 — browser-sync**
```bash
browser-sync start --server "/Users/hj.moon/Desktop/M/4-MKTD/2026 OKR/CRM_카카오_브랜드메시지_템플릿_효율화/kakao-builder/public" --index "kakao-crm-builder.html" --files "**/*" --port 3000
```
접속: `http://localhost:3000` · 파일 변경 시 자동 새로고침

**터미널 2 — Claude Code**
```bash
cd "/Users/hj.moon/Desktop/M/4-MKTD/2026 OKR/CRM_카카오_브랜드메시지_템플릿_효율화/kakao-builder" && claude
```

**터미널 3 — Git**
```bash
# 세트별 백업
cp "public/kakao-crm-builder.html" "public/kakao-crm-builder.bak.html"

# 배포 (GitHub push → Netlify 자동 배포)
git add .
git commit -m "작업 내용"
git push
```

### 배포
- GitHub: https://github.com/moonsun8975-glitch/kakao-builder
- Netlify: https://ohouse-crm-builder.netlify.app

---

## 카드 구성 체계

### 섹션과 템플릿
- **섹션** (`cover`/`inner`/`outro`) = 사이드바 시각 분류일 뿐. 카드 자체엔 고유 속성 없음
- **템플릿 ID**: A·B(표지) / C~I(내지) / J·K(아웃트로)
- **커스텀 에디터 완성**: A·B·C / **범용 fields 폼**: D·E·F·G·H·H1·H2·H3·I·J·K
- 전체 템플릿 상세는 [`docs/DESIGN_SPEC.md`](./docs/DESIGN_SPEC.md) 참조

### 카드 추가·이동 정책
- **최대**: 10장 (`MAX_CARDS=10`)
- **삽입**: 선택 카드 바로 아래. 선택 없으면 맨 뒤
- **섹션 귀속**: 삽입 위치 앞 카드의 섹션을 따라감
- **번호**: 배열 순서대로 자동 재계산

---

## 주요 상수·함수

### 상수
| 이름 | 값 | 의미 |
|------|-----|------|
| `MAX_CARDS` | 10 | 카드 최대 개수 |
| `DEFAULT_GRAD_COLOR` | `#FFFFFF` | 그라디언트 기본값 |
| `DEFAULT_BTN_COLOR` | `#00A1FF` | 버튼 기본값 |
| `REMOVE_BG_KEY` | (하드코딩) | remove.bg API 키 |

### 렌더링 흐름
```
renderAll()
  ├ renderSidebar()  — 사이드바 + "+ 카드 추가" 버튼 활/비활
  └ renderPreview()  — 미리보기 + 편집 핸들 복원
renderEditor() — 중앙 편집창 (templateId 기반 분기)
  ├ renderEditorA(card, body)   — 표지 A 브랜드형
  ├ renderEditorB(card, body)   — 표지 B 가격강조형
  ├ renderEditorC(card, body)   — 내지 C 혜택형
  └ 범용 fields 루프             — D~K
cardHTML(card) — 템플릿별 미리보기 HTML
```

### 카드 조작
| 함수 | 역할 |
|------|------|
| `addCard()` | 10장 제한 체크 + 선택 카드 아래 삽입 |
| `moveCard(e, idx, dir)` | 순서 이동 (섹션 내/간) |
| `delCard(e, idx)` | 삭제 |
| `selectCard(idx)` | 선택 + 이전 편집 상태 정리 |
| `_syncBadgeTypes(d, n)` | C 배지 개수 변경 시 무효값 보정 |
| `_switchImageTypeA(d, t)` | A 이미지 타입 전환 시 자동 모드만 재계산 |

### 이미지 처리
| 함수 | 역할 |
|------|------|
| `handleImgFile` / `B` / `C` | 파일 업로드 (템플릿별) |
| `handleImgUrl` / `B` / `C` | URL 이미지 |
| `removeBackground(src, isUrl, cb)` | remove.bg 호출 |
| `_applyImgAndExtract(d, onDone)` | 컬러 추출 + auto 플래그 세팅 |
| `extractImgColor` / `CenterColor` | 평균/중앙 컬러 추출 |
| `calcBtnColor` / `makeLightBgColor` / `makeGradient` | 컬러 가공 |
| `showImgHandles` / `B` / `2B` / `C` | 편집 핸들 표시 |
| `resetImg` / `B` / `2B` / `C` | 위치·크기 초기화 |
| `toggleImgEdit` / `B` / `2B` / `C` | 편집 모드 토글 |
| `showNukkiLoading` / `hideNukkiLoading` | 로딩 스피너 |

### 컬러피커
| 함수 | 역할 |
|------|------|
| `toggleIro(pid, key)` | iro.js 피커 열기/닫기 |
| `onIroHex(key, pid, el)` | HEX 입력 동기화 |
| `startEyedropper(edType, key, pid)` | 이미지에서 컬러 추출 |
| `autoUpdateBtnColor(d)` | 자동 모드일 때 버튼 컬러 재계산 |

### 자동/수동 토글 플래그
사용자가 iro 피커로 컬러를 변경하면 `_XXXAuto = false`로 세팅됨. "자동" 버튼 누르면 `true` 복원 + `_autoXXX` 값 적용.

| 플래그 | 제어 대상 |
|--------|-----------|
| `_btnColorAuto` | 버튼 컬러 |
| `_gradColorAuto` | 그라디언트 컬러 |
| `_bgColorAuto` | 배경 컬러 |

---

## 코딩 규칙

- 단일 HTML 파일 구조 유지 (`public/kakao-crm-builder.html`)
- 문자열 이스케이프는 `esc()` 사용 (`&`, `<`, `>`, `"` 변환)
- 편집창 변경 후 `updateField()` 호출 — 내부에서 `renderPreview()` + `renderSidebar()` 자동 실행
- 이미지 편집 상태(`_imgEditing`, `_img2Editing`)는 카드 전환 시 반드시 해제
- 누끼/컬러 추출은 비동기 → 완료 전 UI 상태 관리 주의 (BUG-05 참고)

---

## 최근 변경 이력

### 2026-04-20 — CRITICAL 버그 8개 패치
| # | 패치 | 영향 |
|:--:|------|------|
| BUG-01 | B 템플릿 2상품 날짜 `<span>` 누락 | 날짜/시간 양끝 정렬 복구 |
| BUG-02 | B 누끼 로딩 ID 불일치 | 정확한 로더 제거 |
| BUG-04 | A 이미지타입 전환 시 수동 컬러 보존 | 사용자 설정 유지 |
| BUG-05 | A 누끼 로딩 스피너 순서 재조정 | 로더 제때 표시·제거 |
| BUG-06 | 누끼 실패 사용자 alert 추가 | 실패 사실 전달 |
| BUG-07 | `resetImgC` 데이터 초기화 누락 | 전환 후 유지 |
| BUG-08 | C 배지 개수 변경 시 무효값 보정 | UI/데이터 일치 |
| NEW-03 | 카드 추가 정책 + 10장 제한 | 선택 카드 아래 삽입 |

---

## 향후 개선 로드맵

### 🔴 보안
- **IMP-04**: remove.bg API 키 노출 → 온디바이스(`@imgly/background-removal`) 마이그레이션

### 🟡 UX
- iro 컬러피커 ESC/외부 클릭 닫힘
- alert → 토스트 UI
- 카드 전환 시 편집 버튼 UI 잔재 정리
- 셀렉트 변경 시 스크롤 위치 유지

### 🟢 기능
- 미완성 템플릿 D~K 커스텀 에디터 구현
- PNG 내보내기 (현재 안내만)
- 카카오 규격 validator
- 템플릿 교체 시 데이터 마이그레이션

---

## 관련 문서
- [`docs/DESIGN_SPEC.md`](./docs/DESIGN_SPEC.md) — 디자인/기획 상세 스펙
