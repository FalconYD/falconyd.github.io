---
title: FalconDL
images:
 - /img/project/FalconDL_01.png
 - /img/project/FalconDL_02.png
 - /img/project/FalconDL_03.png
 - /img/project/FalconDL_04.png
 - /img/project/FalconDL_05.png
---

## FalconDL
Pytorch C++ 버전인 libtorch기반 AI 테스트 프로젝트.
Github에 구현된 libtorch 기반 프로젝트 UI추가.

### Done
- WPF UI.
- 프로젝트 로드, 언로드
- 여러 이미지 로드
- 라벨링 수정 및 작성 (Segment).
- AI 트레이닝 모델 파일 생성. (Segment)

### Todo
- Classify 모델 Trainning, Inference
- Location Detect 모델 Trainning, Infrence
  
## Program Images

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
