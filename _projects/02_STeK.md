---
title: STeK (2017.11. ~ 2018.12.)
images:

---
## AOI Demo기 Inspector (2017.12. ~ 18.04.)
- 목적 : Bare Film에서 Particle, Scretch등의 이물을 검사하는 장치.
- 사용기술 : Vieworks사 라인 스캔 카메라 제어, Euresys Grablink Full 제어, 영상 처리(Grab처리, Cutting처리, OverScan처리, Result 처리), 마스터간 TCP/IP 통신, 로그 관리.
- 개발환경 : Windows 7 EN / MFC / Vieworks SDK / Euresys SDK
- 담당업무 : Inspector 개발

## EDO-AOI 6GH Inspector(2018.04. ~ 18.11.)
- 목적 : EDO 설비의 Bare Film에서 Particle, Scretch등의 이물을 검사하는 장치.
- 사용기술 : Vieworks사 라인 스캔 카메라 제어, Euresys Grablink Full 제어, 영상 처리(Grab처리, Cutting처리, OverScan처리, Result 처리), 마스터간 TCP/IP 통신, 로그 관리, OpenGL 렌더링, PPL 병렬 처리.
- 개발환경 : Windows 7 EN / MFC / Vieworks SDK / Euresys SDK
- 담당업무 : Inspector 개발 및 현장 셋업
  
### Program Images

<div id="carouselExampleControls" class="carousel slide mb-4" data-ride="carousel">
    <div class="carousel-inner">
        {% for img in page.images %}
            <div class="carousel-item {% if forloop.first %}active{% endif %}">
                <img src="{{ img }}" class="d-block w-100" alt="">
            </div>
        {% endfor %}
    </div>
    <a class="carousel-control-prev" href="#carouselExampleControls" role="button" data-slide="prev">
        <span class="carousel-control-prev-icon" aria-hidden="true"></span>
        <span class="sr-only">Previous</span>
    </a>
    <a class="carousel-control-next" href="#carouselExampleControls" role="button" data-slide="next">
        <span class="carousel-control-next-icon" aria-hidden="true"></span>
        <span class="sr-only">Next</span>
    </a>
</div>
