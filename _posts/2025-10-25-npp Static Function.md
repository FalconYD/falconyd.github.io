---
title: NPP Static Functions
tags:
  - Image Processing
  - CUDA
  - Palellel Processing
  - NPP
---

## Static Functions
 이미지의 통계적 속성을 계산하기 위한 기본형식. 일부 통계 기본 요소는 계산 중에 스크래치 버퍼도 필요하다.
  (https://docs.nvidia.com/cuda/npp/introduction.html#general_conventions_lb_1general_scratch_buffer)
 
<!--more-->
## 함수들
 - Image Sum : 이미지의 모든 픽셀 값의 합
 - Image Min : 이미지의 최소 픽셀 값.
 - Image Min Index : 이미지의 최소 픽셀 값 인덱스. (X,Y 좌표)
 - Image Max : 이미지의 최대 픽셀 값.
 - Image Max Index : 이미지의 최대 픽셀 값 인덱스. (X,Y 좌표)
 - Image Mean : 이미지의 모든 픽셀값의 산술 평균.
 - Image Mean StdDev : 이미지의 모든 픽셀값의 산술 평균, 산술 표준 편차.
 - Image Norm : 이미지의 밝기 정보.
 - Image DotProd : 두 이미지의 내적.
 - Image Count In Range : 특정 강도 범위에 속하는 픽셀의 양을 계산.
 - Image MaxEvery : 두 이미지에서 픽셀 쌍의 최대값을 계산. (두번째 이미지에 저장됨.)
 - Image MinEvery : 두 이미지에서 픽셀 쌍의 최소값을 계산. (두번째 이미지에 저장됨.)
 - Image Integral : 주어진 이미지의 적분 이미지를 계산.
 - Image Square Intergral : 주어진 이미지의 적분 이미지와 제곱 적분 이미지를 모두 계산.
 - Image RectStdDev : 적분 이미지의 표준편차를 계산.
 - Image Histogram Even : bin(값의 범위)이 고르게 분산된 이미지의 히스토그램을 계산.
 - Image Histogram Range : 지정된 범위 내에서 이미지의 히스토그램을 계산.
 - Image Proximity : 원본 이미지와 템플릿 이미지 간의 근접 측정값을 계산. 
 
 (https://docs.nvidia.com/cuda/npp/core_npp.html)  
출처 - CUDA NPP