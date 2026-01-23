---
# the default layout is 'page'
icon: fas fa-info-circle
order: 4
---

<style>
/* [0. 폰트 로드] Pretendard */
@import url("https://cdn.jsdelivr.net/gh/orioncactus/pretendard@v1.3.9/dist/web/static/pretendard.min.css");

@media print {
  /* =========================================
     [1. 글로벌 설정 변수 (여기만 수정하면 됨)]
     ========================================= */
  :root {
    --font-main: "Pretendard", "Apple SD Gothic Neo", sans-serif;
    --color-black: #000000;
    
    /* 레이아웃 사이즈 */
    --page-padding: 0.6cm;      /* 페이지 상하좌우 여백 */
    --font-size-base: 8.8pt;    /* 본문 폰트 크기 */
    --line-height-base: 1.25;   /* 본문 줄 간격 */
    
    /* 제목 사이즈 */
    --font-size-h1: 20pt;       /* 이름 크기 */
    --font-size-h2: 9.5pt;      /* 섹션 헤더 크기 */
    --header-margin-top: -2.3cm; /* 테마 여백 상쇄용 (이름 위로 끌어올리기) */
  }

  /* =========================================
     [2. 페이지 및 컨테이너 초기화 (Nuclear Reset)]
     ========================================= */
  @page { margin: 0; size: A4; }
  
  html, body {
    margin: 0 auto !important;
    padding: 0 !important;
    width: 210mm !important;
    height: 100% !important;
    background: #fff !important;
  }

  /* 메인 래퍼: 실제 여백과 폰트 설정 */
  #main-wrapper {
    margin: 0 auto !important;
    padding: var(--page-padding) !important;
    width: 100% !important;
    box-sizing: border-box !important;
    border: none !important;
    
    font-family: var(--font-main) !important;
    font-size: var(--font-size-base) !important;
    line-height: var(--line-height-base) !important;
    color: var(--color-black) !important;
    letter-spacing: -0.02em !important;
  }

  /* 테마 간섭 요소 초기화 */
  .container, .row, article {
    padding: 0 !important;
    margin: 0 !important;
    width: 100% !important;
    max-width: 100% !important;
  }

  /* =========================================
     [3. 숨김 처리 (Display None)]
     ========================================= */
  /* 테마 UI, 네비게이션, 투명 문단(p) 등 불필요 요소 제거 */
  .dynamic-title, .post-title, .page-heading, .post-heading, 
  header.post-header, h1.dynamic-title,
  #sidebar, #topbar, #panel-wrapper, #tail-wrapper, 
  footer, .post-navigation, .prompt-tip, 
  #back-to-top, .back-to-top, i.fas.fa-arrow-up,
  .post-tail-wrapper, .post-tags, .post-meta, .share-wrapper,
  p { 
    display: none !important; 
    width: 0 !important;
    height: 0 !important;
    margin: 0 !important;
    padding: 0 !important;
    font-size: 0 !important;
    line-height: 0 !important;
  }

  /* =========================================
     [4. 타이포그래피 (Typography)]
     ========================================= */
  /* 공통: 검정색 & 굵기 설정 */
  h1, h2, b, strong, .org-name, .date-right, .job-title-main {
    color: var(--color-black) !important;
    font-weight: 600 !important;
  }

  /* H1: 이름 영역 */
  h1 {
    margin-top: var(--header-margin-top) !important; /* 상단 여백 상쇄 */
    font-size: var(--font-size-h1) !important;
    text-transform: none !important;
    border: none !important;
    margin-bottom: 2px !important;
    padding: 0 !important;
    line-height: 1.1 !important;
    display: block !important;
  }

  /* H2: 섹션 헤더 */
  h2 {
    font-size: var(--font-size-h2) !important;
    text-transform: uppercase !important;
    border-bottom: 1px solid var(--color-black) !important;
    margin-top: 16px !important;    
    margin-bottom: 10px !important;
    padding-bottom: 2px !important;
    letter-spacing: 0.5px !important;
    display: block !important;
    page-break-after: avoid; 
  }

  /* 상단 정보 (직무, 연락처) */
  .job-title-main {
    font-size: 9.5pt !important;
    margin-bottom: 2px !important;
  }
  
  .contact-info {
    font-size: 8.5pt !important;
    margin-bottom: 12px !important;
  }

  /* 회사명 */
  .org-name {
    font-size: 9pt !important;
  }

  /* 날짜/기간 (우측 정렬) */
  .date-right {
    text-align: right !important;
    white-space: nowrap !important;
    font-size: 8.8pt !important;
  }

  /* 직무명 (Italic & Normal Weight) */
  .role-row {
    font-size: 8.8pt !important;
    font-weight: 400 !important;
    font-style: italic !important;
    color: var(--color-black) !important;
    margin: 1px 0 !important;
  }

  /* =========================================
     [5. 레이아웃 & 리스트]
     ========================================= */
  /* 항목 컨테이너 */
  .entry-container { 
    margin-bottom: 6px !important;
    margin-top: 0 !important;
    page-break-inside: avoid; 
  }

  /* 헤더 행 (Flexbox) */
  .header-row {
    display: flex !important;
    justify-content: space-between !important;
    align-items: baseline !important;
    width: 100% !important;
    margin-bottom: 1px !important;
  }

  /* 리스트 스타일 */
  ul {
    margin: 1px 0 3px 0 !important;
    padding-left: 18px !important;
  }
  
  li {
    margin-bottom: 0px !important;
    line-height: var(--line-height-base) !important;
    font-size: var(--font-size-base) !important;
    color: var(--color-black) !important;
    list-style-type: disc !important;
  }
  
  li:last-child { margin-bottom: 0 !important; }
}
</style>

# jmhwang
<div class="job-title-main">Data Engineer</div>
<div class="contact-info">
  Seoul, Korea &bull; +82-10-XXXX-XXXX &bull; email@example.com &bull; github.com/jmhwang-dev
</div>

## Work Experience
<div class="entry-container">
  <div class="header-row">
    <span class="org-name">휴톰 (AI 수술 내비게이션 플랫폼)</span>
    <div class="date-right">2024.04 – 2025.01, 10개월</div>
  </div>

  <div class="role-row">Data Engineer (2024.09 – 2025.01, 5개월)</div>
  <ul>
    <li>임상 데이터 분석을 위한 OLAP 설계 및 데이터 Lineage, 스토리지 체계화</li>
    <li>데이터 정합성 리스크 식별 및 공유 프로세스 수립</li>
  </ul>

  <div class="role-row">MLOps Engineer (2024.04 – 2024.08, 5개월)</div>
  <ul>
    <li>폐쇄망 환경 내 ML 추론 파이프라인 구축 및 기술 검증</li>
    <li>서비스 배포 파이프라인과의 호환성을 고려한 분석 환경 통합</li>
  </ul>
</div>

<div class="entry-container">
  <div class="header-row">
    <span class="org-name">이마고웍스 (AI 치과 CAD 솔루션)</span>
    <div class="date-right">2020.07 – 2023.06, 3년</div>
  </div>

  <div class="role-row">Team Lead (2022.09 – 2023.06, 10개월)</div>
  <ul>
    <li>조직의 역할을 엔지니어링 중심 구조로 개편 및 데이터 수집 매뉴얼 표준화</li>
    <li>사내 데이터 프로덕트 개발 관련 주요 의사결정 수행</li>
  </ul>

  <div class="role-row">Data Engineer (2022.04 – 2023.06, 1년 3개월)</div>
  <ul>
    <li>치과용 CAD 소프트웨어를 통한 GUI 수동 작업을 Polling 기반 워크플로우로 전환하여 Annotation-ready Dataset 공급 자동화</li>
    <li>신규 데이터 감지용 Custom Sensor 구현을 통한 비정형 데이터 처리의 비동기적 안정성 확보</li>
    <li>의료 데이터 보안을 위한 On-premise 환경 내 XCom 병목 해결을 위한 Path-passing 방식 적용</li>
    <li>계층형 저장 구조 설계 및 파일명 표준화 기반의 데이터 일관성 및 멱등성 보장 체계 구축</li>
    <li>Clinical Acceptance 미달 데이터 자동 격리 및 커스텀 상태 코드 체계 구축을 통한 품질 관리 가시성 확보</li>
  </ul>

  <div class="role-row">AI Engineer (2020.07 – 2022.03, 1년 9개월)</div>
  <ul>
    <li>병렬 처리 기반 대규모 학습 데이터셋 생성 가속화</li>
    <li>Computer Vision 리서치 (Segmentation, GAN) 수행</li>
  </ul>
</div>

<div class="entry-container">
  <div class="header-row">
    <span class="org-name">포항공대 인공지능연구원 (구 정보통신연구소)</span>
    <div class="date-right">2019.04 – 2019.06, 3개월</div>
  </div>
  <div class="role-row">인턴 연구원</div>
  <ul>
    <li>실시간 영상 인식 기반 자율주행 시스템 개발 참여</li>
  </ul>
</div>

## Project
<div class="entry-container">
  <div class="header-row">
    <span class="org-name">E2E Data Pipeline Design & Dev</span>
    <div class="date-right">2025.04 – 2025.11, 8개월</div>
  </div>
  <div class="role-row">Personal Project</div>
  <ul>
    <li>람다 아키텍쳐 기반 데이터 파이프라인 구축 및 홈랩 클러스터 구성 (Mini-PC, Pi 5)</li>
    <li>배송 현황/판매 성과 대시보드 및 시스템 모니터링 환경 구축</li>
  </ul>
</div>

## Education
<div class="entry-container">
  <div class="header-row">
    <span class="org-name">세종대학교 일반대학원</span>
    <div class="date-right">2014.09 – 2017.02</div>
  </div>
  <div class="role-row">M.S. in Computer Graphics (GPA: 4.47 / 4.5)</div>
  <ul>
    <li>석사학위논문: 3차원 형태 특징의 다중 사전을 이용한 기하 복원</li>
  </ul>
</div>

<div class="entry-container">
  <div class="header-row">
    <span class="org-name">세종대학교</span>
    <div class="date-right">2008.03 – 2014.08</div>
  </div>
  <div class="role-row">B.S. in Computer Engineering</div>
</div>

## Skills & Etc.
<div class="entry-container">
  <div class="header-row">
    <span class="org-name">Certifications & Training</span>
  </div>
  <ul>
    <li><strong>ADsP (데이터분석 준전문가):</strong> 한국데이터산업진흥원 (2019.12)</li>
    <li><strong>포스코 청년 AI·Big Data 아카데미:</strong> 포스코 주관 전문 교육 수료 (2019.01 – 2019.04)</li>
  </ul>
</div>