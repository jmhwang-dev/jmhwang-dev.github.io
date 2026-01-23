---
# the default layout is 'page'
icon: fas fa-info-circle
order: 4
---

<style>
/* [폰트 로드] Pretendard */
@import url("https://cdn.jsdelivr.net/gh/orioncactus/pretendard@v1.3.9/dist/web/static/pretendard.min.css");

@media print {
  /* [1. 페이지 공통 여백] */
  @page { 
    margin: 1.2cm; 
    size: A4; 
  }
  
  /* [2. 테마 간섭 완전 초기화] */
  html, body, #main-wrapper, .container, article, .row {
    margin: 0 !important;
    padding: 0 !important;
    width: 100% !important;
    max-width: 100% !important;
    background: #fff !important;
    border: none !important;
    box-shadow: none !important;
    top: 0 !important;
  }

  /* [3. 숨김 처리] */
  .dynamic-title, .post-title, .page-heading, .post-heading, 
  #sidebar, #topbar, #panel-wrapper, #tail-wrapper, 
  footer, .post-navigation, .prompt-tip, .back-to-top { 
    display: none !important; 
    height: 0 !important;
    margin: 0 !important;
    padding: 0 !important;
  }

  /* [4. 폰트 설정] */
  body {
    font-family: "Pretendard", "Apple SD Gothic Neo", sans-serif !important;
    font-size: 10pt !important;
    line-height: 1.35 !important;
    color: #333 !important;
    letter-spacing: -0.02em !important;
  }

  /* [5. 1페이지 레이아웃 조정 (사용자 설정 값: -2.3cm)] */
  h1 {
    margin-top: -2.3cm !important; /* 레이아웃 잡힌 값 적용 */
    
    font-size: 24pt !important;
    font-weight: 800 !important;
    color: #2b7a8e !important;
    text-transform: none !important;
    border: none !important;
    margin-bottom: 5px !important;
    padding: 0 !important;
    line-height: 1.1 !important;
    display: block !important;
  }

  /* [H2: 섹션 헤더] */
  h2 {
    font-size: 11.5pt !important;
    font-weight: 800 !important;
    color: #2b7a8e !important;
    text-transform: uppercase !important;
    border-bottom: 1px solid #2b7a8e !important;
    margin-top: 18px !important;
    margin-bottom: 10px !important;
    padding-bottom: 2px !important;
    letter-spacing: 0.5px !important;
    display: block !important;
    page-break-after: avoid; 
  }

  /* [레이아웃 & 텍스트 스타일] */
  .job-title-main {
    font-size: 11pt !important;
    font-weight: 600 !important;
    color: #555 !important;
    margin-bottom: 5px !important;
  }
  
  .contact-info {
    font-size: 9pt !important;
    color: #666 !important;
    margin-bottom: 20px !important;
  }

  .entry-container { 
    margin-bottom: 10px !important;
    page-break-inside: avoid; 
  }

  .header-row {
    display: flex !important;
    justify-content: space-between !important;
    align-items: baseline !important;
    width: 100% !important;
    margin-bottom: 2px !important;
  }

  .org-name {
    font-size: 10.5pt !important;
    font-weight: 700 !important;
    color: #000 !important;
  }

  .org-desc {
    font-size: 10pt !important;
    font-weight: 400 !important;
    color: #444 !important;
  }

  .date-right {
    font-weight: 700 !important;
    text-align: right !important;
    white-space: nowrap !important;
    color: #000 !important;
    font-size: 10pt !important;
  }

  .role-row {
    font-size: 10pt !important;
    font-weight: 400 !important;
    font-style: italic !important;
    color: #222 !important;
    margin: 2px 0 !important;
  }

  ul {
    margin: 2px 0 6px 0 !important;
    padding-left: 26px !important;
  }
  
  li {
    margin-bottom: 1px !important;
    line-height: 1.35 !important;
    color: #333 !important;
    font-size: 10pt !important;
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

## Relevant Work Experience

<div class="entry-container">
  <div class="header-row">
    <div>
      <span class="org-name">Hutom (휴톰)</span>, 
      <span class="org-desc">AI 수술 내비게이션 플랫폼</span>
    </div>
    <div class="date-right">2024.04 – 2025.01, 10개월</div>
  </div>

  <div class="role-row">Data Engineer (2024.09 – 2025.01, 5개월)</div>
  <ul>
    <li>임상 데이터 분석을 위한 OLAP 설계 및 데이터 흐름(Lineage) 스토리지 체계화</li>
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
    <div>
      <span class="org-name">Imagoworks (이마고웍스)</span>, 
      <span class="org-desc">AI 치과 CAD 솔루션</span>
    </div>
    <div class="date-right">2020.07 – 2023.06, 3년</div>
  </div>

  <div class="role-row">Team Lead (2022.09 – 2023.06, 10개월)</div>
  <ul>
    <li>조직의 역할을 엔지니어링 중심 구조로 개편 및 데이터 수집 매뉴얼 표준화</li>
    <li>사내 데이터 프로덕트 개발 관련 주요 의사결정 수행</li>
  </ul>

  <div class="role-row">Data Engineer (2022.04 – 2023.06, 1년 3개월)</div>
  <ul>
    <li>3D 치아 스캔 데이터 ETL 파이프라인 설계 및 임상 허용 오차 기반 품질 검증 자동화</li>
    <li>엣지 케이스 격리 자동화 시스템 구축</li>
  </ul>

  <div class="role-row">AI Engineer (2020.07 – 2022.03, 1년 9개월)</div>
  <ul>
    <li>병렬 처리 기반 대규모 학습 데이터셋 생성 가속화</li>
    <li>Computer Vision 리서치 (Segmentation, GAN) 수행</li>
  </ul>
</div>

<div class="entry-container">
  <div class="header-row">
    <div>
      <span class="org-name">POSTECH AI Research (포항공대 인공지능연구원)</span>
    </div>
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
    <span class="org-name">Sejong University (세종대학교 일반대학원)</span>
    <div class="date-right">2014.09 – 2017.02</div>
  </div>
  <div class="role-row">M.S. in Computer Graphics (GPA: 4.47 / 4.5)</div>
  <ul>
    <li>석사학위논문: 3차원 형태 특징의 다중 사전을 이용한 기하 복원</li>
  </ul>
</div>

<div class="entry-container">
  <div class="header-row">
    <span class="org-name">Sejong University (세종대학교)</span>
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