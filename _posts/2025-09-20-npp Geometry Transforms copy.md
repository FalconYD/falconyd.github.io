---
title: NPP Geometry Transforms
tags:
  - Image Processing
  - CUDA
  - Palellel Processing
  - NPP
---

## Geometry Transforms
 
<!--more-->

### Resize
 크기 조정 함수는 입력 및 출력 ROI(관심 영역)의 너비 및 높이 비율에 의해 자동으로 결정되는 배율 계수를 사용합니다.
 이 간소화된 함수는 이전 릴리스에서 더 이상 사용되지 않는 이전 버전을 대체합니다. 이 함수에서 크기 조정 비율은 oSrcRectROI 및 oDstRectROI의 너비 및 높이 비율에 의해 자동으로 결정됩니다. 이러한 매개변수 중 하나가 해당 이미지 크기와 교차하면 이미지 크기, 너비 및 높이를 벗어난 픽셀은 처리되지 않습니다.

 다음과 같은 보간모드를 지원합니다.
 NPPI_INTER_NN  
 NPPI_INTER_LINEAR  
 NPPI_INTER_CUBIC  
 NPPI_INTER_SUPER  
 NPPI_INTER_LANCZOS  

### Remap
 Remap 기능을 제공하는 루틴.  
 NPPI_INTER_NN  
 NPPI_INTER_LINEAR  
 NPPI_INTER_CUBIC  
 NPPI_INTER_CUBIC2P_BSPLINE  
 NPPI_INTER_CUBIC2P_CATMULLROM  
 NPPI_INTER_CUBIC2P_B05C03  
 NPPI_INTER_LANCZOS  

 Remap은 pXMap 및 pYMap 포인터가 가리키는 두 개의 2D 디바이스 메모리 이미지 배열에 명시적으로 제공된 픽셀 좌표를 사용하여 원본 픽셀을 선택합니다. pXMap 배열에는 X좌표가 포함되어 있고 pYMap 배열에는 입력으로 사용할 해당 원본 이미지 픽셀의 Y좌표가 포함되어 있습니다. 이러한 좌표는 부동 소수점 형식이므로 분수 픽셀 위치를 사용할 수 있습니다. 샘플링할 소스 픽셀의 좌표는 다음과 같이 결정됩니다.  
 nSrcX = pxMap[nDstX,nDstY] nSrcY = pyMap[nDstX,nDstY]
 아래의 Remap 기능에서 소스 이미지 클립 검사는 다음과 같이 처리됩니다.
 소스 픽셀의 소수 x및 y좌표가 oSizeROI.x보다 크거나 같고 oSizeROI.x + oSizeROI.width 보다 작고 oSizeROI.y보다 크거나 같고 oSizeROI.y + oSizeROI.height보다 작으면 소스 픽셀이 소스 이미지 클립 사각형 내에 있는것으로 간주되고 소스 이미지가 샘플링 됩니다. 그렇지 않으면 원본 이미지가 샘플링되지 않고 대상 픽셀이 대상 이미지에 기록되지 않습니다.

### Rotate
 이미지를 0,0주위로 회전, 이동 한다.

### Mirror
 X축, Y축 Flip 동작을 한다.

 (https://docs.nvidia.com/cuda/npp/core_npp.html)  
출처 - CUDA NPP