---
title: NPP Morphological
tags:
  - Image Processing
  - CUDA
  - Palellel Processing
  - NPP
---

## Image Morphological
 형태학적 이미지 작업.  
 형태학적 연산은 이웃 연산으로 분류된다.  
 이러한 함수는 nppim 라이브러리에서 찾을 수 있다.
 
<!--more-->
## Npp Morph
 npp Morphological 연산은 다음과 같다. border형식은 NPP_BORDER_REPLICATE만 지원하고, border가 없는 경우 경계 샘플링은 유저가 확인해야한다.

### Dilation
 팽창연산. 
  - dilate
  - dilateborder
  - dilate3x3
  - dilate3x3border

### Erosion
 수축연산. 
  - Erosion
  - Erosionborder
  - Erosion3x3
  - Erosion3x3border

### 복잡한 형태
 기타 복잡한 형태는 다음과 같은 함수를 지원한다.
  - closeborder
  - openborder
  - tophatborder
  - blackhatborder
  - gradientborder

 함수를 호출 하기전 GetBufferSize를 호출하여 작업버퍼로 할당할 디바이스 메모리의 양을 결정해야한다.그런다음 응용프로그램 할당 장치 메모리는 해당 morphXXXBorder함수에 pBUffer매개변수로 전달된다.

 (https://docs.nvidia.com/cuda/npp/core_npp.html)  
출처 - CUDA NPP