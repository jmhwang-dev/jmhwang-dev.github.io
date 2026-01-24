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
    margin-bottom: 12px !important;
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
    margin: 1px 0 1px 10px !important;
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
    margin-left: 20px !important;
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
    /* color: #666666 !important; */
    margin-top: 6px !important;
    margin-bottom: 1px !important;
    margin-left: 20px !important;     
    font-style: italic !important;
    /* font-weight: 600 !important; */
  }

  .sub-experience-list {
    /* 제목보다 살짝 더 안쪽으로 들여쓰기 */
    margin-left: 32px !important; 
    margin-bottom: 6px !important;
  }

  .sub-experience-list li {
    font-size: 7.2pt !important;
    /* color: #666666 !important; */
    line-height: 1.5 !important;
  }
  
  /* 하위 리스트 불렛 */
  .sub-experience-list li::before {
    /* color: #666666 !important; */
    content: "-" !important;
  }
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

  <div class="role-row">Data Engineer (2024.04 – 2025.01, 10개월)</div>
  <ul>
    <li>전사 마스터 테이블 구축을 위한 데이터 전수 조사 및 이원화된 식별자 체계의 매핑 리스크 식별을 통한 중앙 집중식 관리 가이드라인 수립</li>
    <li>데이터 변환 히스토리 문서 부재 해결을 위한 리니지 시각화 및 대용량 데이터 병합 시 발생하는 기술적 병목 진단</li>
    <li>Pre-op 임상 데이터의 비정기 시계열 특성을 반영하여, 다중 검사 항목에 산재된 지표를 구조화하고 분석용 데이터 모델 설계</li>
    <li>REST API 기반 External Trigger를 활용한 비동기 Airflow 워크플로우 구축으로 병원 폐쇄망 내 서비스 PoC 수행 및 기술 검증</li>
  </ul>
</div>

<div class="entry-container">
  <div class="header-row">
    <span class="org-name">이마고웍스 (AI 치과 CAD 솔루션)</span>
    <div class="date-right">2020.07 – 2023.06, 3년</div>
  </div>

  <div class="role-row">Data Engineer (2022.04 – 2023.06, 1년 3개월)</div>
  <ul>
    <li>치과용 CAD 소프트웨어를 통한 GUI 수동 작업을 Polling 기반 워크플로우로 전환하여 Annotation-ready Dataset 공급 자동화</li>
    <li>신규 데이터 감지용 Custom Sensor 구현을 통한 비정형 데이터 처리의 비동기적 안정성 확보</li>
    <li>의료 데이터 보안을 위한 On-premise 환경 내 XCom 병목 해결을 위한 Path-passing 방식 적용</li>
    <li>계층형 저장 구조 설계 및 파일명 표준화 기반의 데이터 일관성 및 멱등성 보장 체계 구축</li>
    <li>Clinical Acceptance 미달 데이터 자동 격리 및 커스텀 상태 코드 체계 구축을 통한 품질 관리 가시성 확보</li>
  </ul>

  <div class="role-row sub-experience-title">Team Lead (2022.09 – 2023.06, 10개월)</div>
  <ul class="sub-experience-list">
    <li>비주기적, 오프라인 데이터 인입 환경에서 기존 자동화 데이터 처리 흐름의 병목을 개선하고, 다양한 케이스를 대응할 수 있도록 적용 범위를 확장</li>
    <li>도메인 전문가 참여 기반의 학습 데이터 구성과 검증 흐름을 정비하고, Pseudo-label을 활용한 검증 중심 접근으로 인적 개입 병목 완화 방향성 제시</li>
    <li>글로벌 전시회 기술 데모 수행 및 글로벌 고객 대상 기술 Q&A 지원 (IDS 2023, 독일 쾰른)</li>
  </ul>

  <div class="role-row">AI Engineer (2020.07 – 2022.03, 1년 9개월)</div>
  <ul>
    <li>Python Multi-processing 기반 대규모 Dataset 변환 로직 고도화</li>
    <li>EDA를 통한 Trainin Dataset 품질 진단 및 원천 데이터의 특성 분석</li>
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
    <li>이기종 하드웨어 기반 5-Node 홈랩 클러스터 구축 및 계층 분리를 통한 가동 안정성 확보</li>
    <li>Kafka, Spark, Iceberg 기반 람다 아키텍처 설계로 실시간 및 대용량 분석 파이프라인 구현</li>
    <li>Spark foreachBatch 및 증분 처리 적용을 통해 리소스 제약 환경 내 연산 성능 최적화</li>
    <li>Prometheus/Grafana 연동으로 파이프라인 핵심 메트릭(Latency, TPS) 실시간 모니터링 체계 구축</li>
    <li>LLM 기반 비정형 데이터 처리 자동화 및 동적 배칭을 통한 리뷰 데이터 3.6만 건 자산화</li>
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