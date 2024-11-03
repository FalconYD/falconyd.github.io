---
title: OpenGL Image Viewer
tags:
  - 3D
  - OpenGL
  - Image Viewer
---

## OpenGL이란?
<!--more-->
 OpenGL은 gpu가속으로 Visualize를 하는 Open Library 이다.

## ImageViewer
 MFC로 ImageViewer를 일반적인 방법으로 작성하면, 확대하여 Scroll 할 때 끊김이 발생한다.
 Viewer의 코드를 커스텀하여 끊김을 없앨수 있으나, OpenGL로 작성하여 회피할 수 있다.

## OpenGL ImageViewer 
 방식은 이미지 크기에 해당하는 사각형 (삼각형 두개)를 그린 후 텍스처를 그려 이미지 뷰어를 구현한다.
 Shader에서는 이미지의 Scale을 계산하여 사용자의 상호작용에 작용한다.

 https://github.com/FalconYD/MFC-OpenGL-ImageViewer

![Program](/img/post/20241102/ImageViewer.png)

### ImageViewer : https://www.opengl-tutorial.org/
### Font :  https://learnopengl.com/In-Practice/Text-Rendering
### Shape :  https://stackoverflow.com/questions/3484260/opengl-line-width