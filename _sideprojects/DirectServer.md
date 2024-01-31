---
title: DirectServer
imgtarget:
  - /img/sideproject/DirectServer_Target01.png
  - /img/sideproject/DirectServer_Target02.png
  - /img/sideproject/DirectServer_Target03.png
imgbroad:
  - /img/sideproject/DirectServer_Broadcast01.png
---

## 개요
 분산 처리 컴퓨팅을 위한 Direct 서버 구현.

## 특징
 - 1:1 전송 구조로 소규모 분산 처리 컴퓨팅 구현.
 - Client 작업 시 C++ DLL을 Import 하여 작업 가능.
 - 특정 Client에서 다른 Client Alive 기능.
 - Message Type을 BroadCast, Target 으로 분리하여 접속된 Client에 전부 전송 혹은 특정 Client에 메시지 전송 기능.

### Todo
- 메시지 구조체보다 큰 데이터 처리.
- 서버 부하 모니터링.
  
## Program Images

### BroadCast Message
<div id="carouselExampleControls" class="carousel slide mb-4" data-ride="carousel">
    <div class="carousel-inner">
        {% for img in page.imgbroad %}
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

### Target Message
<div id="carouselExampleControls2" class="carousel slide mb-4" data-ride="carousel">
    <div class="carousel-inner">
        {% for img in page.imgtarget %}
            <div class="carousel-item {% if forloop.first %}active{% endif %}">
                <img src="{{ img }}" class="d-block w-100" alt="" onclick="window.open(this.src)">
            </div>
        {% endfor %}
    </div>
    <a class="carousel-control-prev" href="#carouselExampleControls2" role="button" data-slide="prev">
        <span class="carousel-control-prev-icon" aria-hidden="true"></span>
        <span class="sr-only">Previous</span>
    </a>
    <a class="carousel-control-next" href="#carouselExampleControls2" role="button" data-slide="next">
        <span class="carousel-control-next-icon" aria-hidden="true"></span>
        <span class="sr-only">Next</span>
    </a>
</div>
