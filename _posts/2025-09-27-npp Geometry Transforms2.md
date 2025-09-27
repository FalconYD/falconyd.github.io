---
title: NPP Geometry Transforms 2
tags:
  - Image Processing
  - CUDA
  - Palellel Processing
  - NPP
---

## Geometry Transforms 2
 
<!--more-->
### Affine Transforms
 아핀 변환을 기반으로 이미지를 변형(왜곡)합니다.

 아핀 변환은 행렬 c로 제공됩니다. 원본 이미지의 픽셀 위치는 대상 이미지의 위치에 매핑됩니다. 대상 이미지 좌표는 다음과 같이 계산됩니다. 

$$\textcolor{Black}{
  x'=c_{00}*x+c_{01}*y+c_{02}
}$$

$$\textcolor{Black}{
  y'=c_{10}*x+c_{11}*y+c_{12}
}$$

$$\textcolor{Black}{
  C=\begin{bmatrix}
  c_{00} && c_{01} && c_{02} \\
  c_{10} && c_{11} && c_{12}
  \end{bmatrix}
}$$

 아핀 변환은 선형 변환(전통적인 행렬 곱셉)과 이동 연산으로 이해될 수 있습니다. 

   $$\textcolor{Black}{
  L = \begin{bmatrix}
   c_{00} && c_{01}
  \\ c_{10} && c_{11} 
   \end{bmatrix} \quad 
}$$
2x2 행렬 L 은 아핀 변환의 선형 변환 부분을 나타냅니다. 
$$\textcolor{Black}{
  v=\begin{pmatrix}
    c_{02}
  \\  c_{12} 
   \end{pmatrix} \quad 
}$$
벡터 v는 변환 후 이동, 즉 픽셀 위치가 변환된 후에 의해 변환 됩니다.
### Perspective Transforms

 {% highlight c++ %}
   {% raw %}
   // 원본 직사각형 ROI와 대상 사각형 투영이 주어진 원근 변환 계수를 계산한다.
   // oSrcROI : 원본 사각형
   // quad : 목적지 사각형
   // aCoeffs : 원근 변환 계수
   NppStatus nppiGetPerspectiveTransform(NppiRect oSrcROI, const double quad[4][2], double aCoeffs[3][3])
   
   // 주어진 소스 직사각형 ROI의 원근 변환 투영을 계산합니다.
   // oSrcROI : 원본 사각형
   // quad : 목적지 사각형
   // aCoeffs : 원근 변환 계수
   NppStatus nppiGetPerspectiveQuad(NppiRect oSrcROI, double quad[4][2], const double aCoeffs[3][3])

   // 주어진 소스 직사각형 ROI의 원근 투영의 경계 상자를 계산한다.  
   // quad : 목적지 사각형  
   // aCoeffs : 원근 변환 계수  
   NppStatus nppiGetPerspectiveBound(NppiRect oSrcROI, double bound[2][2], const double aCoeffs[3][3])
   {% endraw %}
 {% endhighlight c++ %}

### Perspective Transform
 원근 변환을 기반으로 이미지를 변형(왜곡)한다.
 원근 변환은 행렬 3 x 3 C로 제공된다. 원본 이미지의 픽셀 위치(x,y)는 대상 이미지의 (x',y')위치에 매핑된다. 대상 이미지 좌표는 다음과 같이 계산된다.

 $$\textcolor{Black}{
  x'=\frac{c_{00}*x+c_{01}*y+c_{02}}
  {c_{20}*x+c_{21}*y+c_{22}}
}$$

$$\textcolor{Black}{
  y'=\frac{c_{10}*x+c_{11}*y+c_{12}}
  {c_{20}*x+c_{21}*y+c_{22}}
}$$

$$\textcolor{Black}{
  C=\begin{bmatrix}
  c_{00} && c_{01} && c_{02} \\
  c_{10} && c_{11} && c_{12} \\
  c_{20} && c_{21} && c_{22} \\
  \end{bmatrix}
}$$

{% highlight c++ %}
  {% raw %}
  // 단일 채널 8비트 부호없는 정수 원근 왜곡.
  // pSrc : 원본 이미지 포인터.
  // oSrcSize : 원본 이미지 픽셀의 크기
  // nSrcStep : 원본 이미지의 라인 스탭
  // oSrcROI : 원본 ROI
  // pDst : 목적 이미지 포인터.
  // nDstStep : 목적 이미지 라인 스탭
  // oDstROI : 목적 ROI
  // aCoeffs : 원근 변환 계수
  // eInterpolation : 보상 모드(NPPI_INTER_NN, NPPI_INTER_LINEAR or NPPI_INTER_CUBIC)
  // nppStreamCtx : 애플리케이션 관리 스트림 컨텍스트.
  NppStatus nppiWarpPerspective_8u_C1R_Ctx(const Npp8u *pSrc, NppiSize oSrcSize, int nSrcStep, NppiRect oSrcROI, Npp8u *pDst, int nDstStep, NppiRect oDstROI, const double aCoeffs[3][3], int eInterpolation, NppStreamContext nppStreamCtx)
  {% endraw %}
{% endhighlight c++ %}

 (https://docs.nvidia.com/cuda/npp/core_npp.html)  
출처 - CUDA NPP