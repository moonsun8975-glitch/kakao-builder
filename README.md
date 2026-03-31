# 카카오 와이드 템플릿 빌더

## 배포 방법 (Vercel, 약 3분)

### 1. Vercel 가입
https://vercel.com — GitHub 계정으로 무료 가입

### 2. 이 폴더를 GitHub에 올리기
```bash
git init
git add .
git commit -m "init"
git remote add origin https://github.com/YOUR_ID/kakao-builder.git
git push -u origin main
```

### 3. Vercel에서 배포
- vercel.com → New Project → GitHub 저장소 선택 → Deploy

### 4. 배포 완료 후 빌더 설정
배포된 URL (예: `https://kakao-builder.vercel.app`)을 열고:
- **04. 이미지** 패널에서 Remove.bg API 키 입력
- 프록시 URL: `https://kakao-builder.vercel.app/api/remove-bg`
- 저장 클릭 → 이미지 업로드 시 자동 누끼!

## Remove.bg API 키 발급
https://www.remove.bg/dashboard#api-key
(무료 50회/월)
