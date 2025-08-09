---
title: NPP Arithmetic And Logical
tags:
  - Image Processing
  - CUDA
  - Palellel Processing
  - NPP
---

## Arithmetic And Logical
<!--more-->
 nppial 라이브러리에 있고, 이미지 처리 산술 연산 집합이다.
 함수는 "기능_데이터형식_채널정보_Ctx"형식으로 호출한다.
 Ctx는 Context의 약자로 보이며, 쿠다 스트림을 입력한다.

### Note
 - 기기 상수 버전(DeviceC) 사용하는 함수 앞에 호출된 함수가 해당 기기 상수를 생성하는 경우, cudaStreamSynchronize()나 cudaDeviceSynchronize()를 호출해서 상수를 업데이트 해야한다.
  (ex. nppiAddDeviceC_8u_C1RSFs_Ctx 와 같은 deviceC가 붙은 함수들.)

### Add
 - AddC : 이미지의 각 픽셀에 상수 값을 추가.
 - Add : 두 이미지 더하기.
 - AddSquare : 원본 이미지에서 대상 이미지의 부동 소수점 픽셀값에 대한 제곱 픽셀의 픽셀 단위 추가.
 - AddProduct : 두 원본 이미지의 픽셀 곱을 대상 이미지의 부동 소수점 픽셀 값에 픽셀 단위로 추가.
 - AddWeighted : 원본 이미지의 알파 가중 픽셀 값을 대상 이미지의 부동 소수점 픽셀 값에 픽셀 단위 추가.

### Sub
 - SubC : 이미지의 각 픽셀에 상수 값을 뺌.
 - Sub : 두 이미지 빼기.

### Div
 - DivC : 이미지의 각 픽셀에 상수 값을 나눔.
 - Div : 두 이미지 나누기.
 - Div_Round : 반올림 모드를 사용하여 두 이미지를 픽셀별로 나눔.

### Mul
 - MulC : 이미지의 각 픽셀에 상수 값을 곱함.
 - MulCScale : 이미지의 각 픽셀에 상수값을 곱한 다음 데이터 비트 너비의 최대값만큼 결과를 배율 조정.
 - Mul : 두 이미지 곱하기.
 - MulScale : 두 이미지 각 픽셀을 곱한 다음 데이터 비트 너비의 최대값만큼 결과를 배율 조정.

### AbsDiff
 - AbsDiff : 두 이미지 차이의 절대값.

### etc
 - abs : 이미지의 각 픽셀에 대한 절대값.
 - sqr : 이미지의 각 픽셀에 대한 제곱.
 - sqrt : 이미지의 각 픽셀에 대한 제곱근.
 - ln : 이미지의 각 픽셀에 대한 자연로그 값.
 - exp : 이미지의 각 픽셀에 대한 지수 값.


### And
 - AndC : 이미지의 각 픽셀에 상수값에 대한 논리 And.
 - And : 두 이미지의 각 픽셀에 대한 논리 And.

### Or
 - OrC : 이미지의 각 픽셀에 상수값에 대한 논리 Or.
 - Or : 두 이미지의 각 픽셀에 대한 논리 Or.

### Xor
 - XorC : 이미지의 각 픽셀에 상수값에 대한 논리 Xor.
 - Xor : 두 이미지의 각 픽셀에 대한 논리 Xor.

### Not
 - Not : 이미지의 각 픽셀에 대한 논리 Not

### Shift
 - RShiftC : 이미지의 각 픽셀에 상수값에 대한 우측 Shift.
 - LShiftC : 이미지의 각 픽셀에 상수값에 대한 좌측 Shift.
 
 (https://docs.nvidia.com/cuda/npp/core_npp.html)  
출처 - CUDA NPP