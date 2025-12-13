---
title: NPP Comparison
tags:
  - Image Processing
  - CUDA
  - Palellel Processing
  - NPP
---

## Comparison
 두 이미지의 픽셀 또는 한 이미지와 일정한 값의 픽셀을 비교하여 이진 결과 이미지를 만듭니다. 다중 채널 이미지 유형의 경우, 모든 채널에 대해 조건이 충족되어야 하며, 그렇지 않으면 비교가 거진으로 간주 됩니다. "이진"결과 이미지는 타입 8u_C1입니다. 거진은 0으로 참은 NPP_MAX_8U로 표현됩니다.
  
 
<!--more-->
### Comparison 함수들
  {% highlight c++ %}
   {% raw %}  
   NppStatus nppiCompare_8u_C1R_Ctx(const Npp8u *pSrc1, int nSrc1Step, const Npp8u *pSrc2, int nSrc2Step, Npp8u *pDst, int nDstStep, NppiSize oSizeROI, NppCmpOp eComparisonOperation, NppStreamContext nppStreamCtx)
   {% endraw %}
 {% endhighlight c++ %}  
  1channel 8-bit uchar 이미지를 비교합니다. pSrc1픽셀과 pSrc2픽셀을 비교합니다.

  다음은 Compare의 다른 버전들이다.
  - nppiCompareC : 상수 값으로 이미지의 픽셀을 비교하여 이진결과 이미지를 만든다.
  - nppiCompareEqualEps : 두이미지의 픽셀 값 차이와 엡실론 값을 비교하여 이진 결과 이미지를 만든다. pSrc1의 픽셀과 pSrc2의 해당 픽셀을 비교하여 엡실론 차이가 있어도 동일한지 판단합니다.
  - nppiCompareEqualEpsC : 이미지와 상수가 엡실론 내에서 같은비 비교합니다. PSrc의 픽셀들을 ㅇ리정한 값으로 비교하여 엡실론 차이 내에서 동일한지 판단합니다.

 (https://docs.nvidia.com/cuda/npp/core_npp.html)  
출처 - CUDA NPP