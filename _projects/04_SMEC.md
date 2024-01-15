---
title: D사 (2019.10. ~ 2021.06.)
images:

---
## Wafer Polisher 1.8, 2.0 (2019.10. ~ 2021.06.)
- 목적 : 반도체 Wafer Polishing을 위한 Align, 측정 Module 개발
- 사용기술 : MIL, OpenCV, WPF, C++, Basler 카메라 제어, RS232 조명 제어.
- 개발환경 : Windows 10 / WPF / MIL / Basler SDK / RS232
- 담당업무 : 시료 Align SW 개발, Recipe UI 개발, 현장 셋업.
  
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
