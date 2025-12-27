---
title: NPP Signal Functions
tags:
  - Image Processing
  - CUDA
  - Palellel Processing
  - NPP
---

## Signal Functions
 신호처리를 위한 npp 라이브러리.
 기존 npp 영상처리 라이브러리와 유사한 함수가 다수 존재한다. 자주 사용하지 않아 나열만 해놓는다.
<!--more-->

### Arithmetic and Logical
 연산
 - Signal AddC
 - Signal AddProductC
 - Signal MulC
 - Signal SubC
 - Signal SubCRev
 - Signal DivC
 - Signal DivCRev
 - Signal Add
 - Signal AddProduct
 - Signal Mul
 - Signal Sub
 - Signal Div
 - Signal Div Round
 - Signal Abs
 - Signal Square
 - Signal Square Root
 - Signal Cube Root
 - Signal Exp
 - Signal Ln
 - Signal 10Log10
 - Signal SumLn
 - Signal ArcTan
 - Signal Normalize
 - Signal Cauchy, CouchyD, And CouchyDD2

 비트연산
 - Signal AndC
 - Signal And
 - Signal OrC
 - Signal Or
 - Signal XorC
 - Signal Xor
 - Signal Not
 - Signal LShiftC
 - Signal RShiftC

### Signal Conversion
 데이터 형식 변환 및 스레쉬 홀드.
 - Signal Convert
 - Signal Threshold

### Signal Filtering
 적분기.

### Signal Initialization
 - Signal Set
 - Signal Zero
 - Signal Copy

### Signal Statistical
 - Signal Min Every Or Max Every
 - Signal Sum
 - Signal Maximum
 - Signal Minimum
 - Signal Mean
 - Signal StdDev
 - Signal Mean And StdDev
 - Signal MinMax
 - Signal Norms
 - Signal Dot Product
 - Signal Count In Range
 - Signal Count Zero Crossings
 - Signal Maximum Error
 - Signal Average Error
 - Signal Maximum Relative Error
 - Signal Average Relative Error

### Signal Memory Management
 - Malloc
 - Free

 (https://docs.nvidia.com/cuda/npp/core_npp.html)  
출처 - CUDA NPP