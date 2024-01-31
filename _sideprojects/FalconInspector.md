---
title: FalconInspector
images:
 - /img/sideproject/FalconInspector.png
---

## 개요
Falcon Eyes에 작성하였던 Pattern Inspector 고속처리 프로젝트.

## 특징
- WPF UI.
- Processing 고속화 작업.
  - SIMD OpenMP : SSE 기반 OpenMP 가속.
  - SIMD TBB : SSE 기반 TBB 가속.
  - TBB : TBB 가속. (Thread Building Block)
  - OpenCL : GPGPU 가속. (OpenCL 2.0 지원 디바이스 전용)
  - CUDA : GPGPU 가속. (Nvidia Device 전용)
- OpenCV 480 DLL.
- CvBlob.
- 처리속도 모니터링. (MB/s)
- 시스템 자원 모니터링.
  
## Program Images

<div id="carouselExampleControls" class="carousel slide mb-4" data-ride="carousel">
    <div class="carousel-inner">
        {% for img in page.images %}
            <div class="carousel-item {% if forloop.first %}active{% endif %}">
                <img src="{{ img }}" class="d-block w-100" alt="" onclick="window.open(this.src)">
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
