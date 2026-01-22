---
# the default layout is 'page'
icon: fas fa-info-circle
order: 4
---

<style>
@media print {
  /* [핵심 1] Box-Sizing 초기화: 패딩이 너비를 망치지 않도록 고정 */
  *, *:before, *:after {
    box-sizing: border-box !important;
  }

  /* [핵심 2] 페이지 여백 조정: 좌우 0.8cm로 안정적인 대칭 확보 */
  @page {
    /* 상 0.8cm, 좌우 0.8cm, 하 1.5cm */
    margin: 0.8cm 0.8cm 1.5cm 0.8cm !important;
    size: A4;
  }

  /* 불필요한 요소 숨기기 */
  body > *:not(#main-wrapper),
  #sidebar, #topbar, #panel-wrapper, #tail-wrapper, 
  footer, .post-navigation, .prompt-tip {
    display: none !important;
  }

  /* 기본 폰트 및 컬러 설정 */
  html, body {
    margin: 0 !important;
    padding: 0 !important;
    width: 100% !important;
    height: auto !important;
    -webkit-print-color-adjust: exact !important;
    print-color-adjust: exact !important;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica, Arial, sans-serif !important;
    font-size: 8.5pt !important; 
    line-height: 1.35 !important;
  }

  /* 컨테이너 너비 제한 해제 */
  #main-wrapper, .container, .content, article, .row {
    width: 100% !important;
    max-width: 100% !important;
    margin: 0 !important;
    padding: 0 !important;
  }

  /* 제목 스타일 */
  .dynamic-title, h1 {
    display: block !important;
    font-size: 10.5pt !important;  
    font-weight: 900 !important; 
    color: #000 !important;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica, Arial, sans-serif !important;
    
    border-bottom: 2px solid #000 !important;
    border-top: none !important; 
    
    margin-bottom: 0.25cm !important;
    padding-bottom: 2px !important;
    line-height: 1.2 !important;
    letter-spacing: normal !important;
  }
  
  .dynamic-title { margin-top: -35px !important; }
  h1 { margin-top: 0.4cm !important; }

  /* 회사명(h2) 스타일 */
  h2 { 
    font-size: 9.5pt !important; 
    font-weight: 700 !important;
    margin-top: 0.35cm !important; 
    margin-bottom: 2px !important;
    border-bottom: none !important; 
    border-top: none !important;   
  }
  
  h1 + h2 { margin-top: 0.2cm !important; }

  /* 링크 스타일 */
  a, a:hover, h2 a { 
    text-decoration: none !important;
    border: none !important;
    box-shadow: none !important;
  }

  /* 유틸리티 클래스 */
  .text-muted { color: #6c757d !important; }
  .small { font-size: 7.5pt !important; }
  .fw-bold { font-weight: 700 !important; }

  /* [핵심 3] 레이아웃 비율 조정 (30:70) */
  .row {
    display: table !important;
    table-layout: fixed !important;
    border-spacing: 0 !important;
    width: 100% !important;
    margin-bottom: 5px !important;
  }

  .col-lg-4 { 
    display: table-cell !important; 
    width: 30% !important; /* 30%로 복구하여 안정감 확보 */
    vertical-align: top !important;
    padding-right: 15px !important;
  }

  .col-lg-8 { 
    display: table-cell !important; 
    width: 70% !important; /* 나머지 70% */
    vertical-align: top !important;
    padding: 0 !important;
  }

  /* 리스트 정렬 */
  ul { 
    margin: 0 !important; 
    padding-left: 0 !important; 
    list-style-position: inside !important; 
  }
  
  li { margin-bottom: 0px !important; line-height: 1.35 !important; }

  .job-summary {
    margin-bottom: 2px !important;
    line-height: 1.35 !important;
    padding-left: 0 !important; 
    font-weight: 400 !important;
    color: #000 !important;
  }

  hr { display: none !important; }
}
</style>

# Work Experience

## [휴톰](https://hutom.io/ko)
{:.mt-4 .mb-0}
AI 수술 내비게이션 및 환자 토탈 케어 플랫폼 기업
{:.text-muted .small .mb-4}

<div class="row">
  <div class="col-lg-4">
    <div class="fw-bold">Data Engineer</div>
    <div class="text-muted small">2024.09 - 2025.01 (5개월)</div>
  </div>

  <div class="col-lg-8">
    <ul class="ps-3">
      <li>임상 데이터 분석을 위한 OLAP 설계</li>
      <li>데이터 흐름(Lineage) 및 스토리지 체계화</li>
      <li>데이터 정합성 리스크 식별 및 공유</li>
    </ul>
  </div>
</div>

<div class="row">
  <div class="col-lg-4">
    <div class="fw-bold">MLOps Engineer</div>
    <div class="text-muted small">2024.04 - 2024.08 (5개월)</div>
  </div>

  <div class="col-lg-8">
    <ul class="ps-3">
      <li>폐쇄망 환경 내 ML 추론 파이프라인 구축 및 기술 검증</li>
      <li>서비스 배포 파이프라인과의 호환성을 고려한 분석 환경 통합</li>
    </ul>
  </div>
</div>

## [이마고웍스](https://dentbird.kr)
{:.mt-4 .mb-0}
웹 기반 AI 치과 CAD 솔루션 기업
{:.text-muted .small .mb-4}

<div class="row">
  <div class="col-lg-4">
    <div class="fw-bold">Team Lead</div>
    <div class="text-muted small">2022.09 - 2023.06 (10개월)</div>
  </div>

  <div class="col-lg-8">
    <ul class="ps-3">
      <li>조직의 역할을 엔지니어링 중심 구조로 개편</li>
      <li>사내 데이터 프로덕트 개발 관련 의사결정 수행</li>
      <li>데이터 수집 매뉴얼 표준화</li>
    </ul>
  </div>
</div>

<div class="row">
  <div class="col-lg-4">
    <div class="fw-bold">Data Engineer</div>
    <div class="text-muted small">2022.04 - 2023.06 (1년 3개월)</div>
  </div>

  <div class="col-lg-8">
    <ul class="ps-3">
      <li>3D 치아 스캔 데이터 ETL 파이프라인 설계</li>
      <li>임상 허용 오차 기반 품질 검증 자동화</li>
      <li>엣지 케이스 격리 자동화</li>
    </ul>
  </div>
</div>

<div class="row">
  <div class="col-lg-4">
    <div class="fw-bold">AI Engineer</div>
    <div class="text-muted small">2020.07 - 2022.03 (1년 9개월)</div>
  </div>

  <div class="col-lg-8">
    <ul class="ps-3">
      <li>병렬 처리 기반 대규모 학습 데이터셋 생성 가속화</li>
      <li>Computer Vision 리서치 (Segmentation, GAN)</li>
    </ul>
  </div>
</div>

## [포항공과대학교 인공지능연구원](https://piai.postech.ac.kr)
{:.mt-4 .mb-0}
(구) 정보통신연구소
{:.text-muted .small .mb-4}

<div class="row">
  <div class="col-lg-4">
    <div class="fw-bold">인턴 연구원</div>
    <div class="text-muted small">2019.04 - 2019.06 (2개월)</div>
  </div>

  <div class="col-lg-8">
    <ul class="ps-3">
      <li>실시간 영상 인식 기반 자율주행 시스템 개발</li>
    </ul>
  </div>
</div>

# Project

<div class="row">
  <div class="col-lg-4">
    <a href="https://jmhwang-dev.github.io/posts/ecommerce">
      <div class="fw-bold">E2E 파이프라인 설계 및 개발</div>
    </a>
    <div class="text-muted small">개인 프로젝트</div>
    <div class="text-muted small">2025.04 - 2025.11 (8개월)</div>
  </div>

  <div class="col-lg-8">
    <ul class="ps-3">
      <li>람다 아키텍쳐 기반 데이터 파이프라인 구축</li>
      <li>배송 현황 및 판매 성과 대시보드 구성</li>
      <li>시스템 모니터링 대시보드 구축</li>
      <li>홈랩 클러스터 구성 (Mini-PC, Desktop, iMac, Raspberry Pi 5 * 2)</li>
    </ul>
    </div>
</div>

# Education
<div class="row">
  <div class="col-lg-4">
    <div class="fw-bold">세종대학교 일반대학원 (석사)</div>
    <div class="text-muted small">2014.09 - 2017.02 (졸업)</div>
  </div>

  <div class="col-lg-8">
    <ul class="ps-3">
      <li>전공: 컴퓨터 그래픽스</li>
      <li>학점: 4.47 / 4.5</li>
      <li>석사학위논문: 3차원 형태 특징의 다중 사전을 이용한 기하 복원</li>
    </ul>
  </div>
</div>

<div class="row">
  <div class="col-lg-4">
    <div class="fw-bold">세종대학교</div>
    <div class="text-muted small">2008.03 - 2014.08 (졸업)</div>
  </div>

  <div class="col-lg-8">
    <ul class="ps-3">
      <li>전공: 컴퓨터공학과</li>
    </ul>
  </div>
</div>

# Etc.
<div class="row">
  <div class="col-lg-4">
      <div class="fw-bold">ADsP (데이터분석 준전문가)</div>
    <div class="text-muted small">2019.12 (취득)</div>
  </div>

  <div class="col-lg-8">
    <ul class="ps-3">
      <li>한국데이터산업진흥원에서 주관하는 국가공인 자격증</li>
    </ul>
  </div>
</div>

<div class="row">
  <div class="col-lg-4">
      <a href="https://youth.posco.com/posco/edu/index.php?mod=academy&pag=aca01#khwhat">
      <div class="fw-bold">포스코 청년 AI·Big Data 아카데미</div>
    </a>
    <div class="text-muted small">2019.01 - 2019.04 (수료)</div>
  </div>

  <div class="col-lg-8">
    <ul class="ps-3">
      <li>포스코 주관 AI 및 빅데이터 전문가 양성 교육 프로그램</li>
    </ul>
  </div>
</div>