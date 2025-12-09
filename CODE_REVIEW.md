# 국립중앙박물관 웹사이트 코드리뷰

**작성일:** 2025-12-09
**프로젝트:** National Museum of Korea Website
**기술 스택:** HTML, CSS, JavaScript, jQuery, Swiper.js

---

## 1. 전체 구조

### 1.1 프로젝트 개요
- 국립중앙박물관 소개 및 예약 시스템을 갖춘 웹사이트
- 한국어/영어 다국어 지원 (index.html, kor.html)
- 반응형 디자인 구현

### 1.2 파일 구조
```
├── index.html (영문 페이지)
├── kor.html (한국어 페이지)
├── css/ (스타일시트)
├── js/ (JavaScript 파일)
├── images/ (이미지 리소스)
└── 기타 HTML 페이지들 (info-eng.html, sayu.html 등)
```

---

## 2. 주요 발견사항

### 2.1 긍정적인 부분 ✅

1. **SEO 최적화**
   - Open Graph 메타 태그 구현
   - 적절한 description, keywords 설정
   - 파비콘 다양한 사이즈 제공

2. **모듈화**
   - CSS와 JS를 영역별로 분리 (header.css, footer.css, review.css 등)
   - 관심사의 분리가 잘 되어있음

3. **외부 라이브러리 활용**
   - Swiper.js를 활용한 슬라이더 구현
   - Font Awesome 아이콘 사용
   - 적절한 CDN 활용

4. **사용자 경험**
   - 캘린더 기반 예약 시스템
   - 모달을 활용한 티켓 프로모션
   - 퀵메뉴/맨위로 버튼 제공

---

## 3. 개선이 필요한 부분 ⚠️

### 3.1 코드 품질

#### 문제점 1: 인라인 스타일 과다 사용
**위치:** `index.html:99-238`, `kor.html:119-258`

```html
<style>
  .qwer {
    font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
    max-width: 800px;
    /* ... 100줄 이상의 스타일 */
  }
</style>
```

**문제:**
- HTML 파일에 100줄 이상의 스타일 코드 포함
- 코드 재사용성 저하
- 유지보수 어려움

**권장 개선:**
```html
<!-- 별도 CSS 파일로 분리 -->
<link rel="stylesheet" href="css/auth-modal.css" />
```

---

#### 문제점 2: 의미 없는 클래스명
**위치:** `index.html:100`, `kor.html:120`

```css
.qwer { /* 의미 불명확 */ }
.ww { /* 의미 불명확 */ }
.hh { /* 의미 불명확 */ }
```

**문제:**
- 클래스명이 의미를 전달하지 못함
- 코드 가독성 저하

**권장 개선:**
```css
.auth-container { /* qwer → auth-container */ }
.auth-button { /* ww → auth-button */ }
.auth-heading { /* hh → auth-heading */ }
```

---

#### 문제점 3: 주석 처리된 코드 과다
**위치:**
- `index.html:424-433` (캘린더 요일 헤더)
- `reservation.js:277-293` (GSAP 애니메이션)
- `kor.html:482-491` (이미지 슬라이드)

```html
<!--
<div class="calendar-weekdays">
  <div>SUN</div>
  ...
</div>
-->
```

**문제:**
- 코드베이스 가독성 저하
- 실제 사용 여부 불명확

**권장 개선:**
- 사용하지 않는 코드는 완전히 제거
- Git 히스토리에서 필요시 복원 가능

---

### 3.2 보안 및 성능

#### 문제점 4: 구버전 jQuery 사용
**위치:** `index.html:72`, `kor.html:80`

```html
<script src="js/jquery-1.12.3.js"></script>
```

**문제:**
- jQuery 1.12.3은 2016년 릴리즈 (9년 전)
- 보안 취약점 존재 가능성
- 최신 브라우저 기능 미활용

**권장 개선:**
```html
<!-- 최신 버전으로 업그레이드 -->
<script src="https://code.jquery.com/jquery-3.7.1.min.js"></script>
<!-- 또는 Vanilla JS로 마이그레이션 고려 -->
```

---

#### 문제점 5: 이미지 경로 불일치
**위치:**
- `index.html:557` (절대경로)
- `index.html:563` (상대경로)

```html
<img src="/images/kdh/kdh-1.png" alt=""> <!-- 절대경로 -->
<img src="images/info/info-bg.png" alt="배경이미지" class="info-bg" /> <!-- 상대경로 -->
```

**문제:**
- 경로 방식 혼재로 일관성 부족
- 배포 환경에 따라 경로 오류 발생 가능

**권장 개선:**
```html
<!-- 모든 경로를 상대경로로 통일 -->
<img src="./images/kdh/kdh-1.png" alt="">
<img src="./images/info/info-bg.png" alt="배경이미지">
```

---

### 3.3 접근성 (Accessibility)

#### 문제점 6: 빈 alt 속성
**위치:** `index.html:252, 255, 557`, `kor.html:271-274`

```html
<img src="./images/modal/modal-1-eng.png" alt="" />
<img src="/images/kdh/kdh-1.png" alt="">
```

**문제:**
- 스크린리더 사용자가 이미지 내용 파악 불가
- 웹 접근성 기준(WCAG) 미달

**권장 개선:**
```html
<img src="./images/modal/modal-1-eng.png" alt="Special exhibition ticket promotional image" />
<img src="/images/kdh/kdh-1.png" alt="Cultural event background">
```

---

#### 문제점 7: 버튼에 의미 전달 부족
**위치:** `index.html:558`

```html
<button class="kdh-start-btn"> >> <br> GAME <br> START</button>
```

**문제:**
- 버튼 내용이 ">>" 기호로 시작하여 의미 전달 불명확
- `<br>` 태그로 줄바꿈 (스타일로 처리해야 함)

**권장 개선:**
```html
<button class="kdh-start-btn" aria-label="Start cultural heritage game">
  <span class="btn-icon">&gt;&gt;</span>
  <span class="btn-text">GAME START</span>
</button>
```

---

### 3.4 코드 중복

#### 문제점 8: HTML 구조 중복
**위치:** `index.html`과 `kor.html`

**문제:**
- 한국어/영어 페이지가 별도 HTML 파일로 존재
- 거의 동일한 구조를 2번 작성
- 유지보수 시 두 파일 모두 수정 필요

**권장 개선:**
1. **템플릿 엔진 도입**
   - EJS, Handlebars 등 활용
   - 다국어 텍스트만 JSON으로 분리

2. **또는 클라이언트 사이드 다국어 처리**
   ```javascript
   // i18n 라이브러리 활용
   const translations = {
     ko: { title: "국립중앙박물관" },
     en: { title: "National Museum of Korea" }
   };
   ```

---

### 3.5 JavaScript 이슈

#### 문제점 9: 빈 JavaScript 파일
**위치:** `js/header.js`

```javascript
// 파일이 거의 비어있음 (1줄만 존재)
```

**문제:**
- 불필요한 파일이 로드되어 HTTP 요청 낭비
- 코드 구조 혼란

**권장 개선:**
- 사용하지 않는 파일 제거
- 또는 실제 헤더 기능 구현

---

#### 문제점 10: 하드코딩된 이벤트 데이터
**위치:** `js/reservation.js:34-83`

```javascript
generateSampleEvents() {
  const sampleEventTemplates = [
    {
      time: "09:00",
      title: "각角진 백자 이야기",
      type: "meeting",
      image: "/images/reservation/test.png",
    },
    // ... 더 많은 하드코딩된 데이터
  ];
}
```

**문제:**
- 이벤트 데이터가 코드에 하드코딩
- 데이터 수정 시 코드 변경 및 재배포 필요

**권장 개선:**
```javascript
// 외부 API나 JSON 파일에서 데이터 가져오기
async fetchEvents() {
  const response = await fetch('/api/events.json');
  return await response.json();
}
```

---

### 3.6 메타데이터 누락

#### 문제점 11: 빈 Open Graph 태그
**위치:** `kor.html:36-38`

```html
<meta property="og:url" content="" />
<meta property="og:image" content="" />
```

**문제:**
- 소셜 미디어 공유 시 메타데이터 누락
- SEO 및 공유 경험 저하

**권장 개선:**
```html
<meta property="og:url" content="https://nmk-project.vercel.app/kor.html" />
<meta property="og:image" content="https://nmk-project.vercel.app/images/info/info-bg.png" />
```

---

## 4. 권장사항 우선순위

### 🔴 높음 (즉시 수정 필요)
1. **구버전 jQuery 업데이트** - 보안 이슈
2. **빈 Open Graph 태그 채우기** - SEO 영향
3. **이미지 경로 통일** - 배포 오류 방지
4. **접근성 개선** (alt 속성) - 법적 요구사항

### 🟡 중간 (가까운 시일 내 개선)
5. **인라인 스타일 분리** - 유지보수성
6. **주석 처리된 코드 제거** - 코드 정리
7. **클래스명 개선** - 가독성
8. **빈 JS 파일 제거/구현** - 성능

### 🟢 낮음 (장기적 개선)
9. **다국어 처리 통합** - 아키텍처 개선
10. **이벤트 데이터 외부화** - 확장성
11. **Vanilla JS 마이그레이션 고려** - 현대화

---

## 5. 추가 제안사항

### 5.1 성능 최적화
- **이미지 최적화:** WebP 포맷 사용 고려
- **Lazy Loading:** 이미지와 비디오에 `loading="lazy"` 추가
- **CSS/JS 번들링:** 파일 개수 줄이기

### 5.2 개발 환경
- **빌드 도구 도입:** Webpack, Vite 등
- **CSS 전처리기:** Sass/SCSS 활용
- **린터 설정:** ESLint, Stylelint로 코드 품질 관리

### 5.3 버전 관리
- **package.json 생성:** 의존성 관리
- **의미있는 커밋 메시지:** Git 히스토리 정리
- **브랜치 전략:** feature/fix 브랜치 활용

---

## 6. 결론

전반적으로 **기능은 잘 구현**되어 있으나, **코드 품질과 유지보수성** 측면에서 개선이 필요합니다.

### 강점
- 다국어 지원 구현
- 적절한 외부 라이브러리 활용
- SEO 기본 설정 완료
- 반응형 디자인

### 개선 필요
- 코드 중복 제거
- 보안 업데이트 (jQuery)
- 접근성 개선
- 일관성 있는 코딩 스타일

**다음 단계:**
1. 높은 우선순위 항목부터 순차적으로 개선
2. 코드 리팩토링 진행
3. 테스트 코드 작성 고려
4. 성능 모니터링 설정

---

**작성자:** Claude Code
**문의:** 추가 질문이나 개선사항 논의가 필요한 경우 알려주세요.
