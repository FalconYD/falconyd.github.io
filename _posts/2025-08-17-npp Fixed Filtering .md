---
title: NPP Fixed Filtering
tags:
  - Image Processing
  - CUDA
  - Palellel Processing
  - NPP
---

## Fixed Filtering
<!--more-->
 고정 필터는 고정 크기의 미리 정의된 커널을 사용하여 선형 필터링 작업을 수행한다. 이 섹션에는 GaussAdvanced 및 Bilateral과 같은 몇가지 동적 커널 필터도 포함되어 있다.
 일부 고정 필터에는 경계 제어 기능이 있는 버전이 있다. 이러한 기능의 경우 마스크의 일부가 소스 이미지 경계와 겹치는 경우 요청된 경계유형 연산이 소스 이미지 외부에 잇는 모든 마스크 픽셀에 적용됩니다.
 현재 이러한 기능에 대해서는 NPP_BORDER_REPLICATE경계 유형의 작업만 지원됩니다.

 ### Prewitt (Border Type)
  - FilterPrewittHoriz
   $$\textcolor{Black}{
  \begin{pmatrix}
   1 && 1 && 1 
  \\ 0 && 0 && 0
  \\ -1 && -1 && -1
   \end{pmatrix} \quad 
}$$
  - FilterPrewittVert
  $$\textcolor{Black}{
  \begin{pmatrix}
   -1 && 0 && 1 
  \\ -1 && 0 && 1
  \\ -1 && 0 && 1
   \end{pmatrix} \quad 
}$$
 ### Scharr (Border Type)
  - FilterScharrHoriz
  $$\textcolor{Black}{
  \begin{pmatrix}
   3 && 10 && 3 
  \\ 0 && 0 && 0
  \\ -3 && -10 && -3
   \end{pmatrix} \quad 
}$$
  - FilterScharrVert
  $$\textcolor{Black}{
  \begin{pmatrix}
   -3 && 0 && 3 
  \\ -10 && 0 && 10
  \\ -3 && 0 && 3
   \end{pmatrix} \quad 
}$$
 ### Sobel (Border Type)
   - FilterSobelHoriz
   $$\textcolor{Black}{
  \begin{pmatrix}
   1 && 2 && 1 
  \\ 0 && 0 && 0
  \\ -1 && -2 && -1
   \end{pmatrix} \quad 
}$$
   $$\textcolor{Black}{
  \begin{pmatrix}
   1 && 4 && 6 && 4 && 1 
  \\ 2 && 8 && 12 && 8 && 2 
  \\ 0 && 0 && 0 && 0 && 0 
  \\ -2 && -8 && -12 && -8 && -2 
  \\ -1 && -4 && -6 && -4 && -1 
   \end{pmatrix} \quad 
}$$
   - FilterSobelVert
   $$\textcolor{Black}{
  \begin{pmatrix}
   -1 && 0 && 1 
  \\ -2 && 0 && 2
  \\ -1 && 0 && 1
   \end{pmatrix} \quad 
}$$
   $$\textcolor{Black}{
  \begin{pmatrix}
     -1 && -2 && 0 && 2 && 1
  \\ -4 && -8 && 0 && 8 && 2
  \\ -6 && -12 && 0 && 12 && 6
  \\ -4 && -8 && 0 && 8 && 4
  \\ -1 && -2 && 0 && 2 && 1
   \end{pmatrix} \quad 
}$$
 ### Roberts (Border Type)
   - Roberts Down
   $$\textcolor{Black}{
  \begin{pmatrix}
   0 && 0 && 0 
  \\ 0 && 1 && 0
  \\ 0 && 0 && -1
   \end{pmatrix} \quad 
}$$
   - Roberts Up
   $$\textcolor{Black}{
  \begin{pmatrix}
   0 && 0 && 0 
  \\ 0 && 1 && 0
  \\ -1 && 0 && 0
   \end{pmatrix} \quad 
}$$
 ### Laplace (Border Type)
   $$\textcolor{Black}{
  \begin{pmatrix}
   -1 && -1 && -1 
  \\ -1 && 8 && -1
  \\ -1 && -1 && -1
   \end{pmatrix} \quad 
}$$
   $$\textcolor{Black}{
  \begin{pmatrix}
     -1 && -3 && -4 && -3 && -1
  \\ -3 && 0 && 6 && 0 && -3
  \\ -4 && 6 && 20 && 6 && -4
  \\ -3 && 0 && 6 && 0 && -3
  \\ -1 && -3 && -4 && -3 && -1
   \end{pmatrix} \quad 
}$$
 ### Gauss (Border Type)
  - FilterGauss : 현재 최대 15x15의 마스크 크기를 지원함. 이러한 함수의 필터 커널은 0.4f + (MaskWidth.2)*0.6f의 시그마 값을 사용하여 계산됨.
  - FilterGaussAdvanced : 사용자가 제공한 부동 소수점 계수가 있는 분리 가능한 가우시안 필터 커널을 사용하여 이미지를 필터링함.
  FilterTaps는 2*ceil(radius) + 0.5f + 1인 필터 탭수이다. pKernel은 합계가 1.0f인 nFilterTaps커널 계수 배열을 가르키는 포인터.
  - FilterGaussPyramidLayerUp, FilterGaussPyramidLayerDown : 다운 샘플링 속도가 정수 값과 같으면 불필요한 소스 픽셀은 그냥 건너 뛸수 있다. 정수속도가 아닌 경우 소스 이미지는 이중 선형 보간된다. (1.0F < nRate <= 10.0F)
  
 ### Bilateral Gauss
  $$\textcolor{Black}{
  d=\frac{\sum_{h=-nRadius}^{nRadius}{\sum_{h=-nRadius}^{nRadius}{W1(h,w)W2(h,w)S(h,w)}}}
  {\sum_{h=-nRadius}^{nRadius}{\sum_{h=-nRadius}^{nRadius}{W1(h,w)W2(h,w)}}}
  }$$
  $$\textcolor{Black}{
    W1 = func(nValSquareSigma, (h,w) - S(0,0))\\
    W2 = func(nPosSquareSigma, sqrt(h*h+w*w))\\
    S = SourceImage Pixel Value.

    }$$
  $$\textcolor{Black}{
    func(S,I) = exp(-\frac{I^2}{2.0FS^2})
    }$$
 ### High Pass
  $$\textcolor{Black}{
  \begin{pmatrix}
   -1 && -1 && -1 
  \\ -1 && 8 && -1
  \\ -1 && -1 && -1
   \end{pmatrix} \quad 
}$$
  $$\textcolor{Black}{
  \begin{pmatrix}
     -1 && -1 && -1 && -1 && -1
  \\ -1 && -1 && -1 && -1 && -1
  \\ -1 && -1 && 24 && -1 && -1
  \\ -1 && -1 && -1 && -1 && -1
  \\ -1 && -1 && -1 && -1 && -1
   \end{pmatrix} \quad 
}$$
 ### Low Pass
 $$\textcolor{Black}{
  \begin{pmatrix}
   1/9 && 1/9 && 1/9 
  \\ 1/9 && 1/9 && 1/9
  \\ 1/9 && 1/9 && 1/9
   \end{pmatrix} \quad 
}$$
$$\textcolor{Black}{
  \begin{pmatrix}
     1/25 && 1/25 && 1/25 && 1/25 && 1/25
  \\ 1/25 && 1/25 && 1/25 && 1/25 && 1/25
  \\ 1/25 && 1/25 && 1/25 && 1/25 && 1/25
  \\ 1/25 && 1/25 && 1/25 && 1/25 && 1/25
  \\ 1/25 && 1/25 && 1/25 && 1/25 && 1/25
   \end{pmatrix} \quad 
}$$
 ### Sharpen
  $$\textcolor{Black}{
  \begin{pmatrix}
   -1/8 && -1/8 && -1/8 
  \\ -1/8 && 16/8 && -1/8
  \\ -1/8 && -1/8 && -1/8
   \end{pmatrix} \quad 
}$$
 ### Unsharp
  원본 이미지를 가우시안 필터로 부드럽게하고, 너비는 nRadius에 의해 제어 된다. 원본에서 평활화된 이미지를 빼서 고역 통과 필터링 된 이미지를 만든다. nThreshold에 의해 제어되는 고역 통과 이미지에 필요한 클리핑을 적용한다. 고역 통과 필터링된 이미지의 일정 비율을 원본 이미지에 추가하고, 비율은 nWeight에 의해 제어된다. HighPass = Image-Gaussian(Image)
 결과 = Image + nWeight * HighPass * (|HighPass| >=threshold) 여기서 nWeight는 양, nThreshold는 임계값, >=는 부울 연산, (1, 0)
 ### Wiener (Adaptive Filtering)
 노이즈 제거를 위한 평균필터링을 부분적으로 적용한다.
 처리할 ROI의 분산값으로 에지 포함 여부를 판단하여 에지인경우 평균값에 원래 에지의 가중치를 적용하여 처리하고, 아니면 평균값을 사용하여 DeNoise를 수행한다.
 ### Gradient
  - GradientVectorPrewitt
  - GradientVectorScharr
  - GradientVectorSobel  
  X,Y 고주파 처리 결과로 Gradient를 구한다. 합방식과 최소거리 방식을 지원한다.
  
 (https://docs.nvidia.com/cuda/npp/core_npp.html)  
출처 - CUDA NPP