---
title: NPP Computer Vision Filter
tags:
  - Image Processing
  - CUDA
  - Palellel Processing
  - NPP
---

## Computer Vision Filtering
 사용 가능한 컴퓨터 비전 함수들이다.
<!--more-->
 
### Distance Transform
 2019 08월 08일에 발표된 "GPU로 정확한 거리변호나을 계삲난 병렬 밴딩 알고리즘(PBA+)"를 사용하여 정확한 유클리드 거리 변환 함수를 수행한다.

### Canny
 알고리즘은 3단계로 구성된다.
 1단계는 부호있는 16비트 단일 채널 이미지와 해당 크기 값의 각 방향을 포함하는 단일 채널 32비트 부동 소수점 이미지로 구성된 두개의 출력 이미지를 생성한다.
 2단계는 이러한 크기 및 방향 이미지를 사용하여 3x3 주변 크기 픽셀 이웃에서 테스트 크기 픽셀과 동일한 방향으로 가장 가까운 두이웃의 값보다 낮은 비최대 크기값을 억제한다.
 3단계는 임계값 테스트중 허용 여부에 따라 0~255값의 단일 채널 8비트 비부호 이미지로 구성된다.

### Harris Corners
 단일 채널 8비트 그레이 스케일 이미지에서 해리스 코너 감지를 수행하고 이미지의 각 픽셀에서 코너 응답을 포함하는 단일 채널 32비트 부동 소수점 이미지를 출력한다.  
 알고리즘은 2단계로 구성된다.  
 1단계는 이미지의 각 픽셀에서 XX,YY,XY 그래디언트의 부동소수점 곱을 생성한다. 사용되는 그래디언트의 유형은 eFilterType 및 eMaskSize매개변수에 의해 제어된다.
 2단계는 중앙 픽셀 주변의 3x3 또는 5x5픽셀 창에서 이러한 곱을 평균화한 다음 해당 픽셀에서 해리스 코너 응답을 생성하여 대상 이미지에 출력한다. 해리스 응답값은 H=((XX*YY-XY*XY)-(nK*((XX+YY) * (XX+YY))))*nScale로 결정된다.

### Hough Line
 단일 채널 8비트 이진화(0,255) 소스 특징(캐니 엣지 등) 이미지에서 허프라인을 추출한다.

정규 분포의 원점에서 직성까지의 길이(rho)와 각도(theta)를 나타내는 점, 극 형식의 선 목록을 rho=xcos(theta)+ysin(theta)공식을 사용하여 출력한다. 이산화 수준인 nDelta는 입력 매개변수로 지정됩니다. 이 함수의 성능과 효과는 이 매개변수에 크게 의존하며, 숫자가 클수록 성능이 높고 숫자가 낮을수록 더 상세한 결과가 나타난다. 또한, 한 호출에서 다음 호출로 동일한 순서로 pDeviceLines목록에 선을 추가하는것을 보장하지 않는다. 그러나 nMaxLineCount가 모두 목록에 들어갈 수 있을만큼 충분히 크게 설정되어 있는 한 동일한 선은 모두 생성됩니다. 점 극형식의 선을 다시 데카르트 선으로 변환하려면 다음 공식을 사용한다.
{% highlight c++ %}
   {% raw %}
   Npp32f nHough = ((sqrt(2.0F) * static_cast<Npp32f>(oSizeROI.height > oSizeROI.width ? oSizeROI.height : oSizeROI.width)) / 2.0F); 
   int nAccumulatorsHeight = nDelta.rho > 1.0F ? static_cast<int>(ceil(nHough * 2.0F)) 
                                                : static_cast<int>(ceil((nHough * 2.0F) / nDelta.rho));
         int nCenterX = oSizeROI.width >> 1;
         int nCenterY = oSizeROI.height >> 1;
         Npp32f nThetaRad = static_cast<Npp32f>(deviceline.theta) * 0.0174532925199433F;
         Npp32f nSinTheta = sin(nThetaRad);
         Npp32f nCosTheta = cos(nThetaRad);
         int nX1, nY1, nX2, nY2;

         if (deviceline.theta >= 45 && deviceline.theta <= 135) // degrees
         {
            // y = (rho - x cos(theta)) / sin(theta)
            nX1 = minimum cartesian X boundary value;
            nY1 = static_cast<int>((static_cast<Npp32f>(deviceline.rho - (nAccumulatorsHeight >> 1)) - 
                                    ((nX1 - nCenterX) * nCosTheta)) / nSinTheta + nCenterY);
            nX2 = maximum cartesian X boundary value;
            nY2 = static_cast<int>((static_cast<Npp32f>(deviceline.rho - (nAccumulatorsHeight >> 1)) - 
                                    ((nX2 - nCenterX) * nCosTheta)) / nSinTheta + nCenterY);
         }
         else
         {
            // x = (rho - y sin(theta)) / cos(theta)
            nY1 = minimum cartesian Y boundary value;
            nX1 = static_cast<int>((static_cast<Npp32f>(deviceline.rho - (nAccumulatorsHeight >> 1)) - 
                                    ((nY1 - nCenterY) * nSinTheta)) / nCosTheta + nCenterX);
            nY2 = maximum cartesian Y boundary value;
            nX2 = static_cast<int>((static_cast<Npp32f>(deviceline.rho - (nAccumulatorsHeight >> 1)) - 
                                    ((nY2 - nCenterY) * nSinTheta)) / nCosTheta + nCenterX);
         }
      {% endraw %}
{% endhighlight c++ %}
### Histogram of Oriented Gradients
  소스 이미지에서 히스토그램 지향 그라디언트 작업을 수행하여 요청된 각 위치에 대한 히스토그램 설명자 창을 별도로 생성한다.

  이함수는 N.Dalal과 B.Triggs가 설명한 가장 간단한 형태의 기능을 구현한다.

 (https://docs.nvidia.com/cuda/npp/core_npp.html)  
출처 - CUDA NPP