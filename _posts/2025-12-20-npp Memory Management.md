---
title: NPP Memory Management
tags:
  - Image Processing
  - CUDA
  - Palellel Processing
  - NPP
---

## Memory Management
 피치 이미지 저장 할당 및 해제를 위한 루틴.  
 이러한 방법들은 편의를 위해 제공된다. 각 픽셀 행 끝에 추가 패딩 바이트를 포함할 수 있는 메모리를 할당한다. NPP이미지 처리 원시 요소들이 제대로 작동하려면 패딩이 필수는 아니지만, 패딩이 없으면 제대로 패딩된 이미지에 비해 성능 저하가 심각할 수 있다.  
  
<!--more-->
### 이미지 메모리 할당
 2D 데이터 배열을 위한 ImageAllocator 기법.
 할당자는 할당되는 이미지 데이터의 크기를 지정하는 너비와 높이 매개변수를 가지고 있다. 이들은 새로 생성된 메모리에 대한 포인터를 반환하고, 연속된 줄 사이의 바이트 수를 반환한다.  
 메모리 할당이 장치 메모리 부족이나 장치 메모리 단편화로 실패하면 루틴은 0으로 반환된다.  
 모든 할당기는 성능에 도움이 되는 라인 스트라이드로 메모리를 반환한다. 이 할당기를 반드시 사용해야하는 것은 아니다. 유요한 CUDA장치 메모리 포인터는 NPP원시함수에 사용할 수 있으며 라인 스트라이드에 대한 제한이 없다.
  {% highlight c++ %}
   {% raw %}  
   // 메모리 할당.
   Npp8u *nppiMalloc_8u_C1(int nWidthPixels, int nHeightPixels, int *pStepBytes);  
   // 메모리 해제.
   void nppiFree(void *pData);  
   {% endraw %}
 {% endhighlight c++ %}  

 (https://docs.nvidia.com/cuda/npp/core_npp.html)  
출처 - CUDA NPP