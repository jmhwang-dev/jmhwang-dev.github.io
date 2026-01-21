---
# the default layout is 'page'
icon: fas fa-info-circle
order: 4
---

<style>
@media print {
  /* 1. 브라우저 기본 페이지 마진 설정 (상하좌우 1.0cm로 동일하게 고정) */
  @page {
    margin: 1.0cm !important;
    size: A4;
  }

  /* 2. 불필요한 요소 숨기기 */
  body > *:not(#main-wrapper),
  #sidebar, #topbar, #panel-wrapper, #tail-wrapper, 
  footer, .post-navigation, .prompt-tip {
    display: none !important;
  }

  /* 3. 모든 레이아웃 컨테이너의 마진/패딩 완전 박멸 */
  html, body, #main-wrapper, #main-wrapper > div, 
  #main-wrapper > div > div.row.flex-grow-1, 
  main, article, .content, .container, .row, [class*="col-"] {
    margin: 0 !important;
    padding: 0 !important;
    width: 100% !important;
    max-width: 100% !important;
    display: block !important;
  }

  /* 4. 제목(.dynamic-title) 위치 정밀 조정 */
  .dynamic-title {
    display: block !important;
    /* 브라우저 기본 h1 마진 제거 */
    margin: 0 !important; 
    /* 폰트 상단 여백이 거슬린다면 살짝 음수 마진 (보통 -5px ~ -10px) */
    margin-top: -50px !important; 
    margin-bottom: 0.8cm !important;
    padding-bottom: 5px !important;
  }

  /* 5. 경력 사항(2단 레이아웃) 가로 정렬 고정 */
  /* 크롬에서 flex가 깨질 경우를 대비해 table 레이아웃 권장 */
  .row {
    display: table !important;
    width: 100% !important;
  }
  .col-lg-4 { display: table-cell !important; width: 30% !important; vertical-align: top; }
  .col-lg-8 { display: table-cell !important; width: 70% !important; vertical-align: top; }
}
</style>

> Add Markdown syntax content to file `_tabs/about.md`{: .filepath } and it will show up on this page.
{: .prompt-tip }

### Place
{:.mt-4 .mb-0}
desciption
{:.text-muted .small .mb-4}

<div class="row">
  <div class="col-lg-4">
    <div class="fw-bold">Postion1 &<br>Postion2</div>
    <div class="text-muted small">2015.01 - 2016.01</div>
  </div>

  <div class="col-lg-8">
    <ul class="ps-3">
      <li>contribution1</li>
      <li>contribution1</li>
    </ul>
  </div>
</div>