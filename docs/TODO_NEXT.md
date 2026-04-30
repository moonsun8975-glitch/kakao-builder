# 다음 작업 TODO

**최종 업데이트**: 2026-04-30
**다음 작업**: D 카드 커스텀 에디터 또는 추가 개선

---

## ✅ 완료 (2026-04-23)

### B 카드 이미지 기능 통일
- 이미지 UI A와 동일하게 (호버 오버레이, 위치 조정 토글, 편집 힌트)
- object-fit: cover (연출컷)
- 누끼 처리 제거
- 최소 스케일 1, 드래그 위치 제한 (parentElement 기준)
- 핸들 blockClick 추가

Commit: `b016dda`

---

## ✅ 완료 (2026-04-30)

### C 카드 유의사항 텍스트 편집
- noticeText 필드 추가 (30자 제한)
- 토글 스위치로 표시/미표시 전환
- 폰트 두께 400 → 300 (Light)
- 딤드 opacity 0.2 → 0.3

### A, B 카드 버튼 화살표 옵션
- btnArrow 토글 (기본 on)
- A는 화살표 옵션화, B는 화살표 신규 추가
- 미리보기 조건부 렌더링

### 토글 스위치 컴포넌트 신규 도입
- .switch-toggle 클래스 (44×24px, --y200 ON 컬러)
- 컬러피커 스타일 두 줄 레이아웃
- 적용 4곳: A 화살표, B 화살표, B 날짜, C 유의사항

### C 카드 배지 밝기 옵션
- darkImg 토글 추가 (배지 개수 옆 가로 배치)
- ON 시 배지 배경 #3F3F3F, OFF 시 #2F2F2F
- 자동 분석은 CORS 문제로 포기 → 수동 토글 채택

### C 카드 배지 간격
- 8px → 10px

### 호버 오버레이 버그 수정
- B, C 카드 이미지 핸들러에서 hover overlay display 갱신 추가
- 이미지 업로드 후 호버 안내가 안 뜨던 문제 해결

### 카드 복제 기능
- duplicateCard(idx) 함수 추가
- 사이드바 카드 우클릭 메뉴 (복제/삭제)
- 사이드바 카드 호버 시 복제 SVG 아이콘
- ctx-menu 컴포넌트 (외부 클릭/ESC로 닫힘)

### 사이드바 카드 UI 개선
- 카드 desc 텍스트 변경 (A,B,C,D - "연출/연출or누끼" 추가)
- card-name 폰트 11.5px → 11px
- card-desc 폰트 10px → 8px, letter-spacing 0.03em 추가
- card-item gap 8px → 7px (복제 버튼 공간 확보)

### Netlify 배포 수정
- public/_redirects 추가 (루트 → kakao-crm-builder.html)
- 이전 index.html 삭제로 인한 404 해결

### 문서 정리
- COMPONENT_RULES.md: 시스템 컬러 토큰, 토글 스위치, 이미지 핸들러 표준 시그니처, 자동 컬러 추출 로직 흐름 섹션 추가
- DESIGN_SPEC.md: 캐러셀(750×1000) 전용 명세서로 재구성, 카드별 빈 템플릿 구조

Commits: `8f6e60f`, `1090afe`, `0f8bbb3`, `e7eba88`

---

## 🎯 다음 할 일

### 1️⃣ D 카드 커스텀 에디터
현재 D 카드는 범용 fields 폼만 사용 중. 다른 카드처럼 커스텀 에디터로 작업.

체크리스트:
- [ ] D 카드 디자인 확정 (Figma)
- [ ] 필요한 데이터 필드 정의 (이미지, 띠배너, 제품정보 등)
- [ ] customEditor: true 설정
- [ ] renderEditorD 함수 구현
- [ ] 이미지 핸들러 추가 (handleImgFileD, handleImgUrlD)
- [ ] 미리보기 렌더링 구현
- [ ] DESIGN_SPEC.md의 D 카드 섹션 채우기
- [ ] TEMPLATE_CHECKLIST 따라 진행

### 2️⃣ DESIGN_SPEC.md 채우기 (점진적)
빈 템플릿으로 둔 카드별 명세 채우기. 각 카드 작업 시 함께 진행.

- [ ] A 카드 명세
- [ ] B 카드 명세
- [ ] C 카드 명세
- [ ] D~K 작업 시 추가

### 3️⃣ E~K 카드 커스텀 에디터 (장기)
현재 범용 fields 폼만 사용 중인 카드들 순차 진행.
- [ ] G 이미지형
- [ ] H 프레임형
- [ ] H1~H3 룩북형
- [ ] I 자유형
- [ ] J 카테고리형

### 4️⃣ 추가 개선 아이디어 (백로그)
- [ ] 사이드바 카드 액션 버튼을 ⋮ 메뉴로 통합 (필요 시)
- [ ] 카드 복제 시 단축키 (Cmd+D)
- [ ] 실행 취소 (Cmd+Z) — 복잡도 높음

---

## 📚 참고 문서

- `docs/COMPONENT_RULES.md` — 컴포넌트 규칙 (시스템 토큰, 토글, 이미지 핸들러, 자동 컬러 추출)
- `docs/TEMPLATE_CHECKLIST.md` — 새 템플릿 체크리스트
- `docs/DESIGN_SPEC.md` — 캐러셀 디자인 명세 (점진적 채우기)
- `CLAUDE.md` — 프로젝트 개요

---

## 🔖 최근 커밋

- `e7eba88` — 카드 복제 + 사이드바 UI 개선
- `0f8bbb3` — Netlify 리다이렉트 수정
- `1090afe` — C 배지 밝기 + 호버 버그 + 디자인 명세 분리
- `8f6e60f` — 토글 스위치 + B 화살표 + C 유의사항
- `fd71a6d` — 컴포넌트 규칙 + TODO 재정리
- `b016dda` — B 카드 이미지 기능 A 카드 연출컷 방식으로 통일
