---
title: NPP Label Markers
tags:
  - Image Processing
  - CUDA
  - Palellel Processing
  - NPP
---

## Label Markers
 npp의 Label 마커는 라벨링 까지만 수행 하는것 같다. 통상의 블랍에서 사용하는 위치, 폭, 높이, 무게중심 등은 추가 처리를 해야하는 것 같다. 
<!--more-->
 나중에 이미지 분할에 사용할 이미지 연결 영역 레이블 마커를 생성한다.
 연결된 영역은 영역의 모든 픽셀이 동일한 픽셀값을 갖는 모든 픽셀 영역이다. 마커레이블 id는 일반적으로 이미지 왼쪽에서 오른쪽으로, 위에서 아래로 값이 증가하며 특정 순서로 생성되지 않으며 순차적 마커 ID사이에 숫자 간격이 있을 수 있다. 생성된 마커 ID의 수를 제한하려면 애플리케이션은 이 기능을 호출하기 전에 임계값 필터를 통해 이미지를 전달해야 한다. 그러나 이렇게 한다고 해서 이 함수에서 생성된 최대 마커 ID값이 반드시 제한 되는 것은 아니다. 이러한 함수는 현재 최대 4기가픽셀의 이미지 ROI크기만 지원한다. 또한 이러한 함수는 oSizeROI.width * sizeof(Npp32u)와 정확히 같지 않ㅇㄴ 대상 이미지 피치를 지원하지만 피치가 정확히 oSizeROI.width * sizeof(Npp32u)인 경우 작업 버퍼에서 대상 이미지로 최종 결과를 복사하는 cudaMemCpyAsync()호출을 피할 수 있다.  
  
 LabelMarkersUF함수를 호출하기 전에 애플리케이션은 먼저 LabelMarkersUFGetBufferSize를 호출하여 작업 버퍼로 할당할 디바이스 메모리 양을 결정해야 한다. 그런 다음 할당된 디바이스 메모리는 해당 LabelMarkersUF함수에 pBuffer 매개 변수로 전달된다.
 이 구현에 사용된 알고리즘은 Jun Chen등이 쓴 "An Optimized Union-Find Algorithm for Connected Components Labeling Using GPUs"에 설명된 알고리즘을 기반으로 한다.
 이러한 함수의 대상 임지니느 cudaMallocPitch()가 아닌 cudaMalloc()으로 할당되어야 한다.  
 또한 출력 이미지의 피치는 oSizeROI.width*sizeof(Npp32u)로 설정해야 한다.
 {% highlight c++ %}
   {% raw %}
   NppStatus nppiLabelMarkersUF_8u32u_C1R_Ctx(Npp8u *pSrc, int nSrcStep, Npp32u *pDst, int nDstStep, NppiSize oSizeROI, NppiNorm eNorm, Npp8u *pBuffer, NppStreamContext nppStreamCtx)
   // 1채널 8비트에서 32비트의 부호 없는 정수 레이블 마커 이미지 생성.
   // 
   // 매개 변수
   // pSrc – 소스 이미지 포인터.
   // 
   // nSrcStep – 소스 이미지 라인 단계.
   // 
   // pDst – 대상 이미지 포인터.
   // 
   // nDstStep – 대상 이미지 라인 단계.
   // 
   // oSizeROI – 관심 지역(ROI).
   // 
   // eNorm – 사용할 픽셀 연결 테스트 유형, nppiNormInf는 8방향    연결을 // 사용하고 nppiNormL1은 4방향 연결을 사용합니다.
   // 
   // pBuffer – 해당 LabelMarkersUFGetBufferSize 호출에서 반환된 값만큼 큰 디바이스 메모리 스크래치 버퍼에 대한 포인터입니다.
   // 
   // nppStreamCtx – 애플리케이션 관리형 스트림 컨텍스트입니다.
   // 
   // 반환
   // 이미지 데이터 관련 오류 코드, ROI 관련 오류 코드
   {% endraw %}
 {% endhighlight c++ %}
 (https://docs.nvidia.com/cuda/npp/core_npp.html)  
출처 - CUDA NPP