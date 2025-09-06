---
title: NPP FloodFill
tags:
  - Image Processing
  - CUDA
  - Palellel Processing
  - NPP
---

## Flood Fill
 연결된 영역을 특정 새로운 값으로 채운다.
<!--more-->
 
### FloodFillGetBufferSize
 FloodFill 함수 호출전 앺ㄹ리케이션은 먼저 FloodFillGetBufferSize함수를 호출하여 작업 버퍼로 할당할 디바이스 메모리양을 결정해야한다. 그런 다음 애플리케이션 할당 디바이스 메모리는 pBUffer 매개 변수로 해당 FloodFill 함수에 전달된다.
 {% highlight c++ %}
   {% raw %}
   // 대상 이미지 oSizeROI 너비 및 높이를 기반으로 FloodFill 함수에 필요한 스크래치 버퍼 크기를 계산한다.
   // oSizeROI : 관심영역
   // hpBufferSize : 요구 버퍼 크기 (바이트)
   NppStatus nppiFloodFillGetBufferSize(NppiSize oSizeROI, int* hpBufferSize)
   {% endraw %}
 {% endhighlight c++ %}
 
### FloodFill
 새 픽셀 값을 사용하여 이미지의 시드 픽셀 위치에 있는 픽셀에 연결된 픽셀 영역의 제자리 채우기.
 FloodFill 함수를 호출하기 전에 애플리케이션은 먼저 FloodFillGetBufferSize 함수를 호출하여 작업 버퍼로 할당할 디바이스 메모리의 양을 결정해야한다. 그런 다음 할당된 디바이스 메모리는 pBuffer 매개변수로 해당 FloodFill 함수에 전달된다.
 필요에 따라 함수는 호스트 메모리 pConnectedRegion NppiConnectedRegion 구조체에서 채워진 영역에 대한 연결된 영역 정보를 반환할 수 있다. oBoundingBox x 및 y는 왼쪽 및 상단 좌표로 설정되고 너비와 높이는 pSrcDst를 기준으로 경계 상자의 오른쪽 하단 좌표로 설정된다. 필요하지 않은 경우 pConnectedRegion을 NULL로 설정한다. pConnectedRegion 정보를 요청하면 성능이 약간 저하 될 수 있다.

### Flood Fill Boundary
 새 픽셀 값을 사용하여 이미지 시드 픽셀 위치에 있는 픽셀에 연결된 픽셀 영역의 제자리 채우기. 또한 채워진 영역의 경계를 지정된 색상으로 채운다.

### Flood Fill Range
 새 픽셀 값을 사용하여 이미지 시드 픽셀 위치에 있는 픽셀에 연결된 픽셀 영역의 제자리 채우기. (In Place : Min <= Pixel Value <= Max)

### Flood Fill Range Boundary
 새 픽셀 값을 사용하여 이미지 시드 픽셀 위치에 있는 픽셀에 연결된 픽셀 영역의 제자리 채우기. (In Place : Min <= Pixel Value <= Max) 또한 채워진 영역의 경계를 색상으로 채운다.
### Flood Fill Gradient
 시드 픽셀 위치에서 픽셀에 연결된 픽셀 영역의 제자리 채우기와 새 픽셀값이 있는 이미지에서 시드- 최소에서 시드+최대를 포함한 원래 픽셀값을 사용한다.

### Flood Fill Gradient Boundary
 시드 픽셀 위치에서 픽셀에 연결된 픽셀 영역의 제자리 채우기와 새 픽셀값이 있는 이미지에서 시드- 최소에서 시드+최대를 포함한 원래 픽셀값을 사용한다. 또한 채워진 영역의 경계를 색상으로 채운다.

 (https://docs.nvidia.com/cuda/npp/core_npp.html)  
출처 - CUDA NPP