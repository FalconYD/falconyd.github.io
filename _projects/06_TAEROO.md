---
title: TAEROO (2021.11. ~ .)
images:

---
## Glass Laser Dummy Cutter & Film Cutter (2023.06. ~ 2023.12.)
- 목적 : 건설 현장에 사용되는 Glass 코팅 필름 절단 설비.
- 사용기술 : Ajin Motion Controller, RS232, CIS Cam Ctrl, Basler Cam Ctrl, OpenCV, MFC, C++, 시퀀스 운용.
- 개발환경 : Windows 10 / MFC / OpenCV / CameraLink / RS232
- 담당업무 : 프로젝트 설계 및 개발, 셋업.

## 세탁기 Front AI Inspector (2022.06. ~ 2023.03.)
- 목적 : 세탁기 전면 판넬 표면 AI 검사.
- 사용기술 : AiDitron SDK, Master Program(Result Mapping, 검사시작), Client Program (AI처리 Result Merge, Camera 19EA)
- 개발환경 : Windows 10 / WPF / AiDitron AI / Coaxial Camera/ RS232 / TCP/IP
- 담당업무 : 프로그램 설계 및 개발, 셋업.

## Roll to Roll Label Print Inspector (2021.11. ~ 2022.08.)
- 목적 : Roll to Roll 인쇄기 라벨 인쇄 검사
- 사용기술 : Golden Sample Matching(CUDA), WPF, 영상처리 (Grab-Cut-Align-PreProc-PostPorc-Result 구조), PDF 로드, USB License, Line Scan Camera Color) 제어.
- 개발환경 : Windows 10 / WPF / OpenCV / CUDA / CameraLink / RS232
- 담당업무 : 프로그램 설계 및 개발, 셋업.
  
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
