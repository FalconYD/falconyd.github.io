---
title: NPP Threshold
tags:
  - Image Processing
  - CUDA
  - Palellel Processing
  - NPP
---

## Threshold
 픽셀별 임계값 및 비교 연산을 위한 방법들.
  
 
<!--more-->
### Threshold 함수들
  {% highlight c++ %}
   {% raw %}  
   NppStatus nppiThreshold_8u_C1R_Ctx(const Npp8u *pSrc, int nSrcStep, Npp8u *pDst, int nDstStep, NppiSize oSizeROI, const Npp8u nThreshold, NppCmpOp eComparisonOperation, NppStreamContext nppStreamCtx)  
    NppStatus nppiThreshold_8u_C1IR_Ctx(const Npp8u *pSrc, int nSrcStep, NppiSize oSizeROI, const Npp8u nThreshold, NppCmpOp eComparisonOperation, NppStreamContext nppStreamCtx)
   {% endraw %}
 {% endhighlight c++ %}  
  1channel 8-bit uchar threshold. 비교 연산 OP에서 술어가 참이면 그 픽셀은 nThreshold로 설정되고 그렇지 않으면 SourcePixel로 설정된다. C1R과 C1IR의 차이는 destination 버퍼의 유무, 즉 src데이터 변조 여부이다.

  다음은 Threshold의 간편한 버전들이다.
 - Threshold Greater Than : sourcePixel이 nThreshold보다 크면 해당 픽셀은 nThreshold로 설정되고, 그렇지 않으면 SOurcePixel로 설정된다.
 - Threshold Less Than : sourcePixel이 nThreshold보다 작으면 해당 픽셀은 nThreshold로 설정되고, 그렇지 않으면 SOurcePixel로 설정된다.
 - Threshold Value : 비교 연산 OP에서 술어가 참이면 nValue로 설정되고, 아니면 sourcePixel로 설정된다.
 - Threshold Greater Than Value : sourcePixel이 nThreshold보다 크면 해당 픽셀은 nValue로 설정되고, 아니면 sourcePixel로 설정된다.
 - Threshold Less Than Value : sourcePixel이 nThreshold보다 작으면 해당 픽셀은 nValue로 설정되고, 아니면 sourcePixel로 설정된다.
 - Fused AbsDiff Threshold Greater Than Value : 비교 연산에서 sourcePixels의 absdiff가 pThreshold보다 크면 출력 픽셀은 pValue로 설정되고, 그렇지 않으면 sourcePixels의 absdiff로 설정된다.
 - Threshold Less Than Value Greater Than Value : 비교 연산에서 sourcePixel이 nThresholdLT보다 작으면 nValueLT로 설정되고, 그렇지 않으면 sourcePixel이 nThresholdGT보다 크면 nValueGT로 설정되고, 그렇지 않으면 sourcePixel로 설정된다.
 

 (https://docs.nvidia.com/cuda/npp/core_npp.html)  
출처 - CUDA NPP