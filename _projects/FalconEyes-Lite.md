---
title: FalconEyes Lite
images:
 - /img/FalconEyes_lite_00.jpg
 - /img/FalconEyes_lite_01.png
 - /img/FalconEyes_lite_02.png
 - /img/FalconEyes_lite_03.png
 - /img/FalconEyes_lite_04.png
 - /img/FalconEyes_lite_05.png
 - /img/FalconEyes_lite_06.png
---

## FalconEyes-Lite
영상처리 공부를 처음 하면서 만든 프로그램이다. 영상처리를 하는데 있어 영상처리를 테스트해볼 수 있는 Tools의 중요성을 느꼈던 프로젝트. 현재는 2.0버전으로 Up하며 lite버전의 기능 대부분은 Porting하였다.

- Image Processing Tool.
- Visual C++ 영상 처리 프로그래밍 책 기반 구현.
  (IPP 이미지 클래스를 OpenCV Mat으로 맞게 변환.)
- MFC MDI 기반 프로그램.
- Undo 10개까지.(Image Buffer 저장 방식)
- Image Open, Close, Capture.
- Image : 그리기, 측정(Histogram 측정), 그레이스케일, 나누기, 합치기 , 명암변환, 연산, 기하변환.
- Filter : 스무딩, 강조, 잡음, 특수필터, 주파수영역, 엣지, 검출
- Segment : 이진화, 검출, 이진모폴로지, 그레이스케일, 모폴로지, 영상특징 (OpenCV SIFT, SURF)
- Video : 재생, 프레임, 스킵, 모드 (AVI)
- OpenCV 430 Static Build.
  
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