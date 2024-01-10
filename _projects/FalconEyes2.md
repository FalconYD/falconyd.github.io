---
title: Test Pjt
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

## Test Pjt
Test Pjt

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
