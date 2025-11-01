---
title: NPP Static Functions 2
tags:
  - Image Processing
  - CUDA
  - Palellel Processing
  - NPP
---

## Static Functions 2
 이미지의 통계적 속성을 계산하기 위한 기본형식. 일부 통계 기본 요소는 계산 중에 스크래치 버퍼도 필요하다.
  (https://docs.nvidia.com/cuda/npp/introduction.html#general_conventions_lb_1general_scratch_buffer)
 
<!--more-->
## 함수들
 - Image Square Distance Full Norm : 전체 모드로 두 이미지 사이의 정규화된 유클리드 거리를 계산하기 위한 기본 형식.
 - Image Square Distance Same Norm : 동일한 모드를 가진 두 이미지 사이의 정규화된 유클리드 거리를 계산하기 위한 기본 형식.
 - Image Square Distance Valid Norm : 유효한 모드로 두 이미지 사잉의 정규화된 유클리드 거리를 계산하기 위한 기본 형식.
 - Image Cross Correlation Full Norm : 전체 모드를 사용하여 두 이미지 간의 정규화된 상호 상관 관계를 계산하기 위한 기본 형식.
 - Image Cross Correlation Same Norm : 동일한 모드를 사용하는 두 이미지 간의 정규화된 상호 관계를 계산하기 위한 기본 형식.
 - Image Cross Correlation Valid Norm : 유효한 모드를 사용하여 두 이미지간의 정규화된 상호 상관 관계를 계산하기 위한 기본 형식.
 - Image Cross Correlation Valid : 유효한 모드로 두 이미지 간의 상호 상관 관계를 계산하기 위한 기본 요소.
 - Image Cross Correlation Full Norm Level : 전체 모드로 두 이미지 간의 정규화된 상호 상관 계수를 계산하기 위한 기본 형식.
 - Image Cross Correlation Same Norm Level : 동일한 모드를 가진 두 이미지 간의 정규화된 상호 상관 계수를 계산하기 위한 기본 요소.
 - Image Cross Correlation Valid Norm Level : 유효한 모드로 두 이미지 간의 정규화된 상호 상관 계수를 계산하기 위한 기본요소.
 - Image Cross Correlation Full Norm Level Advanced : 이미지 템플릿 크기가 큰 전체 모드의 두 이미지 간의 정규화된 상호 상관 계수를 계산하기 위한 기본 형식.
 - Image Cross Correlation Same Norm Level Advanced : 이미지 템플릿 크기가 큰 동일한 모드를 가진 두 이미지 간의 정규화된 상호 상관 계수를 계산하기 위한 기본 형식.
 - Image Cross Correlation Valid Norm Level Advanced : 템플릿 크기가 큰 유효한 모드를 사용하여 두 이미지 간의 정규화된 상호 상관 계수를 계산하기 위한 기본 형식.
 - Image Quality Index : 두 이미지의 이미지 품질 지수를 계산하기 위한 기본 형식.
 - Image Maximum Error : 두 이미지 pSrc1과 pSrc2 사이의 최대 오류를 계산하기 위한 기본형식. 두 개의 이미지가 주어지고 너비 W와 높이 H가 모두 주어지면 최대 오류는 두 이미지의 픽셀간의 가장 큰 절대차이로 정의된다. 이미지가 복소수 형식인 경우 복소수의 절대값이 제공된다.
 - Image Average Error : 두 이미지 사이의 평균 오류를 계산하기 위한 기본형식. 두개의 이미지 pSrc1, pSrc2가 주어지고 너비 W와 높이 H가 모두 주어지면 평균 오류는 다음과 같이 정의.
 $$\textcolor{Black}{
  Average Error = \frac{1}{W * H * N} \sum_{n=0}^{N-1}\sum_{j=0}^{H-1}\sum_{i=0}^{W-1} \begin{vmatrix}pSrc1(j,i)-pSrc2(j,i)\end{vmatrix}
}$$
  여기서 N은 채널 수를 나타낸다. 이미지가 복잡한 형식인 경우 절대값이 계산에 사용.
 - Image Maximum Relative Error : 두 이미지 간의 최대 상대 오차를 계산하기 위한 기본 형식. 두 개의 이미지 pSrc1, pSrc2와 너비 W와 높이 H가 모두 주어지면 최대 상대 오차는 다음과 같이 정의된다.
  $$\textcolor{Black}{
  MaximumRelativeError = max\frac{\begin{vmatrix}pSrc1(j,i)-pSrc2(j,i)\end{vmatrix}}{max\big(\begin{vmatrix}pSrc1(j,i)-pSrc2(j,i)\end{vmatrix}\big)}
}$$
 이미지가 복잡한 형식인 경우 절대값이 계산에 사용된다. 다중 채널의 경우 모든 채널의 최대 상대 오차가 리턴된다.
 - Image Average Relative Error : 두 이미지 간의 평균 상대 오류를 계산하기 위한 기본 형식. 두 개의 이미지 pSrc1, pSrc2와 너비 W와 높이 H가 모두 주어지면 최대 상대 오차는 다음과 같이 정의된다.
$$\textcolor{Black}{
  Average Error = \frac{1}{W * H * N} \sum_{n=0}^{N-1}\sum_{j=0}^{H-1}\sum_{i=0}^{W-1} \frac{\begin{vmatrix}pSrc1(j,i)-pSrc2(j,i)\end{vmatrix}}{max\big(\begin{vmatrix}pSrc1(j,i)-pSrc2(j,i)\end{vmatrix}\big)}
}$$
 여기서 N은 채널 수이다. 이미지가 복잡한 형식인 경우 절대값이 계산에 사용된다.
 - Image Quality Assessment IQA : MES, PSNR, SSIM 및 MS-SSIM과 같은 두 이미지 간의 이미지 품질을 계산하기 위한 기본 형식.
 - Image Batch Quality Assessment : 모든 입력 이미지 쌍에 대해 단일 ROI(관심 영역)를 사용하여 MSE,PSNR,SSIM 및 MS-SSIM과 같은 이미지 쌍 배치에 대한 이미지 품질을 계산하기 위한 기본 형식.
 - Image Advanced Batch Quality Assessment : 이미지별 ROI(관심 영역)를 사용하여 MSE,LPSNR,SSIM 및 MS-SSIM과 같은 이미지 쌍 배치에 대한 이미지 품질을 계산하기 위한 기본 형식.
 

 (https://docs.nvidia.com/cuda/npp/core_npp.html)  
출처 - CUDA NPP