---
title: NPP Linear Transforms
tags:
  - Image Processing
  - CUDA
  - Palellel Processing
  - NPP
---

## Image Linear Transforms Functions
선형 이미지 변환.

 함수는 nppist 라이브러리에 있다. 사용하는 하위 라이브러리에만 연결하면 동적 라이브러리를 사용할때 링크시간, 애플리케이션 로드 시간 및 CUDA 런타임 시작 시간을 크게 절약할수 있다.
 
<!--more-->
### Function

{% highlight c++ %}
  {% raw %}
  NppStatus nppiMagnitude_32fc32f_C1R_Ctx(const Npp32fc *pSrc, int nSrcStep, Npp32f *pDst, int nDstStep, NppiSize oSizeROI, NppStreamContext nppStreamCtx)
  {% endraw %}
{% endhighlight c++ %}
32비트 복소수 부동소수점을 32비트 크기 부동소수점으로.

복소수 픽셀 이미지를 단일 채널 이미지로 변환하여 결과 픽셀을 복소수 값의 크기로 계산한다.

{% highlight c++ %}
  {% raw %}
  NppStatus nppiMagnitudeSqr_32fc32f_C1R_Ctx(const Npp32fc *pSrc, int nSrcStep, Npp32f *pDst, int nDstStep, NppiSize oSizeROI, NppStreamContext nppStreamCtx)
  {% endraw %}
{% endhighlight c++ %}  
 32비트 복소수 부동소수점을 32비트 크기 부동소수점으로.  
  
 복소수 픽셀 이미지를 단일 채널 이미지로 변환하여 결과 픽셀을 복소수 값의 제곱 크기로 계산한다.

  제곱 크기는 크기 계산의 중간 결과이므로 실제 크기보다 빠르게 계산할 수 있습니다. 정렬/비교에만 크기가 필요한 경우 nppiMagnitude_32fc32f_C1R 대신 이함수를 사용하는 것이 가치 있는 성능 최적화가 될 수 있다.

 (https://docs.nvidia.com/cuda/npp/core_npp.html)  
출처 - CUDA NPP