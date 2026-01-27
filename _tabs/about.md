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
     [1. 글로벌 설정 변수]
     ========================================= */
  :root {
    --font-main: "Pretendard", "Apple SD Gothic Neo", sans-serif;
    --color-black: #000000;
    
    /* 레이아웃 사이즈 */
    --page-padding: 0.6cm;
    --font-size-base: 8.8pt;
    --line-height-base: 1.25;
    
    /* 제목 사이즈 */
    --font-size-h1: 20pt;
    --font-size-h2: 9.5pt;
    --header-margin-top: -2.3cm;
  }

  /* =========================================
     [2. 페이지 및 컨테이너 초기화]
     ========================================= */
  @page { margin: 0; size: A4; }
  
  html, body {
    margin: 0 auto !important;
    padding: 0 !important;
    width: 210mm !important;
    height: 100% !important;
    background: #fff !important;
  }

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

  .container, .row, article {
    padding: 0 !important;
    margin: 0 !important;
    width: 100% !important;
    max-width: 100% !important;
  }

  /* =========================================
     [3. 숨김 처리]
     ========================================= */
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
     [4. 타이포그래피]
     ========================================= */
  h1, h2, b, strong, .org-name, .date-right, .job-title-main {
    color: var(--color-black) !important;
    font-weight: 600 !important;
  }

  h1 {
    margin-top: var(--header-margin-top) !important;
    font-size: var(--font-size-h1) !important;
    text-transform: none !important;
    border: none !important;
    margin-bottom: 2px !important;
    padding: 0 !important;
    line-height: 1.1 !important;
    display: block !important;
  }

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

  .job-title-main {
    font-size: 9.5pt !important;
    margin-bottom: 2px !important;
  }
  
  .contact-info {
    font-size: 8.5pt !important;
    margin-bottom: 6px !important;
  }

  .org-name {
    font-size: 9pt !important;
  }

  .date-right {
    text-align: right !important;
    white-space: nowrap !important;
    font-size: 8.8pt !important;
  }

  .role-row {
    font-size: 8.8pt !important;
    font-style: italic !important;
    color: var(--color-black) !important;
    margin: 1px 0 1px 0 !important; 
  }

  /* =========================================
     [5. 레이아웃 & 리스트]
     ========================================= */
  .entry-container { 
    margin-bottom: 6px !important;
    margin-top: 0 !important;
    page-break-inside: avoid; 
  }

  .header-row {
    display: flex !important;
    justify-content: space-between !important;
    align-items: baseline !important;
    width: 100% !important;
    margin-bottom: 1px !important;
  }

  ul {
    margin: 1px 0 3px 0 !important;
    margin-left: 5px !important; 
    padding-left: 0 !important;
  }
  
  li {
    list-style-type: none !important; 
    position: relative !important;
    padding-left: 10px !important; 
    margin-bottom: 0px !important;
    line-height: var(--line-height-base) !important;
    font-size: var(--font-size-base) !important;
    color: var(--color-black) !important;
  }
  
  li::before {
    content: "-" !important;
    position: absolute !important;
    left: 0 !important;
    top: 0px !important;
  }
  
  li:last-child { margin-bottom: 0 !important; }

  .sub-experience-title {
    font-size: 7.5pt !important;
    margin-top: 6px !important;
    margin-bottom: 1px !important;
    margin-left: 10px !important;     
    font-style: italic !important;
  }

  .sub-experience-list {
    margin-left: 20px !important; 
    margin-bottom: 6px !important;
  }

  .sub-experience-list li {
    font-size: 7.2pt !important;
    line-height: 1.5 !important;
  }
  
  .sub-experience-list li::before {
    content: "-" !important;
  }

  .key-skills {
    font-size: 8.5pt !important;
    font-size: 8.8pt;
    font-weight: 600 !important;
    color: #333 !important;
  }
}
</style>

# jmhwang
<div class="job-title-main">Data Engineer</div>
<div class="contact-info">
  Seoul, Korea &bull; +82-10-XXXX-XXXX &bull; email@example.com &bull; github.com/jmhwang-dev
</div>

<div class="key-skills">
  Skills: | Python | SQL | Apache Spark | Kafka (Confluent) | Airflow | PostgeSQL | Iceberg | MinIO | Docker | Grafana | Prometheus | PyTorch |
</div>

## Work Experience
<div class="entry-container">
  <div class="header-row">
    <span class="org-name">휴톰 (AI 수술 내비게이션 플랫폼)</span>
    <div class="date-right">2024.04 – 2025.01, 10개월</div>
  </div>

  <div class="role-row">Data Engineer (2024.04 – 2025.01, 10개월)</div>
  <ul>
    <li>약 11k 환자의 수술 전 <strong>다중 검사 항목별 1:N 시계열 데이터 모델링 및 PostgreSQL 기반 스키마 설계</strong></li>
    <li>8종 의료 데이터 및 CV feature의 식별자 체계를 정리하고 변환 흐름을 분석하여, <strong>데이터 리니지 가시성 확보 및 거버넌스 체계 정립의 기틀 마련</strong></li>
    <li>폐쇄망 환경을 고려한 <strong>Docker 기반 추론 환경 구성 및 Airflow 비동기 ML 추론 워크플로우 개발</strong> 및 기술 검토</li>
  </ul>
</div>

<div class="entry-container">
  <div class="header-row">
    <span class="org-name">이마고웍스 (AI 치과 CAD 솔루션)</span>
    <div class="date-right">2020.07 – 2023.06, 3년</div>
  </div>

  <div class="role-row">Data Engineer (2022.04 – 2023.06, 1년 3개월)</div>
  <ul>
    <li>GUI 기반 수동 작업을 Airflow Polling 기반 워크플로우로 전환하여 <strong>약 20k 케이스의 Annotation-ready Dataset 공급 자동화 (평균 20초/건)</strong></li>
    <li>Clinical Acceptance 기준 미충족 데이터의 자동 격리 및 Custom Status Code 기반의 변환 실패 원인 식별을 통해 <strong>품질 관리 체계 확보</strong></li>
    <li>데이터 처리 단계별 계층형 저장 구조 설계 및 파일명 표준화 기반의 <strong>데이터 일관성 및 멱등성 보장 체계 구축</strong></li>
  </ul>

  <div class="role-row sub-experience-title">Team Lead (2022.09 – 2023.06, 10개월)</div>
  <ul class="sub-experience-list">
    <li>Human-in-the-loop 기반 레이블 생성 프로세스의 병목 완화를 위해, Pseudo-label을 활용한 <strong>검증 중심 파이프라인 설계</strong></li>
    <li>글로벌 전시회 기술 데모 수행 및 <strong>글로벌 고객 대상 기술 Q&A 지원</strong> (IDS 2023, 독일 쾰른)</li>
  </ul>

  <div class="role-row">AI Engineer (2020.07 – 2022.03, 1년 9개월)</div>
  <ul>
    <li>Python Multi-processing 기반 <strong>대규모 Dataset 변환 로직 고도화</strong></li>
    <li>EDA를 통한 <strong>Training Dataset 품질 진단</strong> 및 원천 데이터의 특성 분석</li>
    <li>Computer Vision 리서치 (Segmentation, GAN)</li>
  </ul>
</div>

<div class="entry-container">
  <div class="header-row">
    <span class="org-name">포항공대 인공지능연구원 (구 정보통신연구소)</span>
    <div class="date-right">2019.04 – 2019.06, 3개월</div>
  </div>
  <div class="role-row">인턴 연구원</div>
  <ul>
    <li>YOLOv5 기반 실시간 객체 인식 및 서버 통신 연동을 통한 자율주행 RC카 추론 시스템 개발</li>
  </ul>
</div>

## Project
<div class="entry-container">
  <div class="header-row">
    <span class="org-name">E-Commerce Real-time Analytics Platform</span>
    <div class="date-right">2025.04 – 2025.11, 8개월</div>
  </div>
  <div class="role-row">
    Personal Project
    <a href="https://jmhwang-dev.github.io/posts/ecommerce/" target="_blank" class="project-link">[상세 링크]</a>
  </div>
  <ul>
    <li>이기종 하드웨어 기반 <strong>5-Node 홈랩 클러스터 구성</strong></li>
    <li>Kafka(Confluent), Spark, Airflow 기반 <strong>람다 아키텍처 설계 및 실시간 분석 파이프라인 구현</strong></li>
    <li>Iceberg를 활용해 <strong>메달리온 아키텍처</strong>를 구성하고, 데이터의 정제 수준과 사용 목적을 구분하는 저장 구조 설계</li>
    <li>Prometheus, Grafana 연동으로 파이프라인 <strong>핵심 메트릭(Latency, TPS) 실시간 모니터링</strong></li>
    <li><strong>동적 배칭 및 멀티 프로세싱 기반 LLM 추론 로직 구현</strong>을 통해 약 36k건의 리뷰 번역 효율 최적화 (RTX 3060 12GB, 7일 소요)</li>
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

## Etc.
<div class="entry-container">
  <div class="header-row">
    <span class="org-name">Certifications & Training</span>
  </div>
  <ul>
    <li><strong>ADsP (데이터분석 준전문가):</strong> 한국데이터산업진흥원 (2019.12)</li>
    <li><strong>포스코 청년 AI·Big Data 아카데미:</strong> 포스코 주관 전문 교육 수료 (2019.01 – 2019.04)</li>
  </ul>
</div>