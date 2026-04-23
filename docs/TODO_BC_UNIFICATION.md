# B, C 카드 이미지 기능 통일 작업 TODO

**생성일**: 2026-04-23
**목표일**: 2026-04-24
**예상 소요**: 약 1시간

---

## 🎯 목표

B, C 카드의 **이미지 처리 기능**을 A 카드와 동일하게 통일.
편집창/미리보기 레이아웃은 유지.

---

## 📊 카드별 작업 범위

### A 카드 (기준점 - 수정 불필요)
- 이미지: `_img` 1개, 자동 컬러 추출 있음

### B 카드
- 이미지: `_img`, `_img2` (제품 이미지 2개)
- **이미지 기능만** A와 통일 (로딩/핸들러/URL/위치조정)
- 컬러: 수정 없음 (기존 유지)

### C 카드
- 이미지: `_img` 1개
- **이미지 기능만** A와 통일
- 컬러: 배너 타입별로 분리 (`bannerColor_brand`, `bannerColor_sale`)

---

## STEP 1: 준비

### 1.1 백업 브랜치
```bash
cd "/Users/hj.moon/Desktop/M/4-MKTD/2026 OKR/CRM_카카오_브랜드메시지_템플릿_효율화/kakao-builder"
git checkout main
git pull
git branch backup-before-BC-unify
```

### 1.2 참고 문서
- `docs/COMPONENT_RULES.md` - ImageUploader 규칙
- `docs/TEMPLATE_CHECKLIST.md` - 체크리스트

---

## STEP 2: B 카드 이미지 기능 통일

### 2.1 A와 B의 이미지 UI 비교
```bash
sed -n '545,633p' public/kakao-crm-builder.html  # A
sed -n '634,681p' public/kakao-crm-builder.html  # B
```

### 2.2 renderEditorB에서 이미지 UI 수정
- [ ] `_img` 영역: A와 동일한 HTML 구조
- [ ] `_img2` 영역: A와 동일한 HTML 구조
- [ ] URL 입력칸 추가 (각 이미지별)
- [ ] URL 안내문 추가
- [ ] 이미지 조정 버튼 (초기화, 위치조정)

### 2.3 이미지 핸들러 확인/수정
- [ ] `handleImgFile_B`, `handleImgFile_B2` 정상 작동
- [ ] 로딩 표시
- [ ] URL 이미지 처리

### 2.4 테스트 후 커밋
```bash
git add public/kakao-crm-builder.html
git commit -m "feat: B 카드 이미지 기능을 A 카드와 통일"
```

---

## STEP 3: C 카드 이미지 기능 통일

### 3.1 A와 C의 이미지 UI 비교
```bash
sed -n '682,720p' public/kakao-crm-builder.html  # C
```

### 3.2 renderEditorC에서 이미지 UI 수정
- [ ] A와 동일한 HTML 구조
- [ ] URL 입력칸 + 안내문
- [ ] 이미지 조정 버튼

### 3.3 이미지 핸들러 확인/수정
- [ ] `handleImgFile_C` 정상 작동
- [ ] 로딩 표시
- [ ] URL 이미지 처리

### 3.4 테스트 후 커밋
```bash
git add public/kakao-crm-builder.html
git commit -m "feat: C 카드 이미지 기능을 A 카드와 통일"
```

---

## STEP 4: C 카드 배너 컬러 분리 (별도 작업)

### 4.1 데이터 구조 변경
- [ ] `bannerColor` → `bannerColor_brand`, `bannerColor_sale` 분리
- [ ] 287줄 C 카드 default 수정

### 4.2 배너 타입에 따른 컬러 적용
- [ ] `bannerType === '브랜드형'` → `bannerColor_brand` 사용
- [ ] `bannerType === '오세일형'` → `bannerColor_sale` 사용

### 4.3 컬러피커 UI
- [ ] 배너 타입에 따라 활성 컬러피커 표시

### 4.4 테스트 후 커밋
```bash
git add public/kakao-crm-builder.html
git commit -m "feat: C 카드 배너 타입별 컬러 분리"
```

---

## STEP 5: 마무리

### 5.1 전체 테스트
- [ ] A, B, C 모두 기본 동작
- [ ] 이미지 UI 통일성 확인
- [ ] 카드 간 전환 정상

### 5.2 푸시
```bash
git push
```

---

## 📚 참고

- `docs/COMPONENT_RULES.md`의 **ImageUploader 섹션**
- A 카드 `renderEditorA` 함수 (545-633줄)
- 이미지 핸들러: `handleImgFile`, `handleImgUrl`
