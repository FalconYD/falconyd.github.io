---
title: FalconEyes 2.0
images:
 - /img/project/FalconEyes_2_01.png
 - /img/project/FalconEyes_2_02.png
 - /img/project/FalconEyes_2_03.png
 - /img/project/FalconEyes_2_04.png
 - /img/project/FalconEyes_2_05.png
 - /img/project/FalconEyes_2_06.png
 - /img/project/FalconEyes_2_07.png
 - /img/project/FalconEyes_2_08.png
 - /img/project/FalconEyes_2_09.png
---

## FalconEyes2.0
기존 MFC MDI기반 FalconEyes를 WPF기반 Dock UI로 Porting한 프로그램.

- Image Processing Tool.
- Visual C++ 영상 처리 프로그래밍 책 기반 구현 -> 2.0으로 바꾸면서 C++ DLL로 변경.
  (IPP 이미지 클래스를 OpenCV Mat으로 맞게 변환.)
- C++ C#간 커맨드 형식 인터페이스 구현.
- WPF Dock 기반.
- Image Open(bmp, png, webp, jpg, RAW), Close, Capture.
- Image : 그리기(WPF 기반), 측정(Histogram 측정), 그레이스케일, 나누기, 합치기, 투시변환, 명암변환, 논리연산, 기하변환.
- Filter : 스무딩, 강조, 잡음, 특수필터, 주파수영역, 엣지, 검출
- Segment : 이진화, 검출, 이진모폴로지, 그레이스케일, 모폴로지, 영상특징 (OpenCV SIFT, SURF)
- Extension
  - USB Camera : 공유 메모리를 통한 Image Grab.
  - Repeatability Precision : 반복성 검사를 위한 별도 프로세스. WPF MVVM Style로 작성됨. OpenCV Pattern Matching 사용.
  - QR Bar : Zxing Lib을 이용한 QR 코드 및 Barcode 인식. (OpenCV 4.5.1 버전업으로 OpenCV QRCodeReader로 변경.)
  - OCR : Tesseract Lib을 이용한 OCR 문자 인식.
  - OpenCL : OpenCL을 이용한 Sobel, Pattern Matching 구현.
  - 3D Profile : WPF 3D Visual을 기반으로 한 3D Profile -> Helix 3D로 대치.
  - Algorithm Test : 영상처리 알고리즘 테스트용 Window.
  - Deep Learning : torchlib을 MFC 단독 프로세스로 MNIST 숫자인식 구현.
- Inspect
  - AutoSearch : 패턴 검사 알고리즘 테스트.
  - Inspector : 패턴 검사 알고리즘 별도 프로세스 (FalconInspector)
  - Detect Rectangle : 사각형 검출.
  - Measure Thick : 라인 두께 검사.
- OpenCV 451 Static Build -> 480 DLL.
  
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
