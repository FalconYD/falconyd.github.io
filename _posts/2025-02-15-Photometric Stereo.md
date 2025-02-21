---
title: Photometric Stereo
tags:
  - Photometric Stereo
  - Light Concept
---
## Photometric Stereo
<!--more-->
 영상의 품질을 개선하는 아이디어. 2차전지와 같은 산업에서 조명을 N개(3개이상, 보통 4개?) 사용하여 동일한 대상을 측정 후 개선된 이미지로 Rule-Base 검사 혹은 AI Inference를 진행한다.
  
$$\textcolor{Black}{
  I = k(L \cdot n)
}$$
  
$$\textcolor{Black}{
  I
  }$$ 
  는 밝기, 
  $$\textcolor{Black}{
  L
  }$$ 
  은 조명 위치를 x,y,z로 표현한다.
  현재까지 파악한 것으로는 영상 결과 I와 조명의 방향 L을 알고 있을 때, Norm Vector와 Albedo(반사맵)을 얻을 수 있다.

  현재 NewProggie코드를 참고하여 Single, CUDA, SIMD 작업을 진행 함.
  참고 코드에는 albedo가 없어서 찾아보니, 코드내의 p값이 Albedo를 표현하는것 같아서 해당 값을 각 좌표에 대입하여 출력해보니 Albedo와 비슷하여 반사맵을 정의 하였다.

## 설명
 https://en.wikipedia.org/wiki/Photometric_stereo
 https://hygenie-studynote.tistory.com/28
 https://velog.io/@gawon1224/9.-Photometric-Stereo

## 코드 참고
 https://github.com/NewProggie/Photometric-Stereo

## 결과
 개인 프로젝트 툴에서 작업하였고, SIMD, CUDA 테스트용으로 3552 * 3288 4장 을 처리하였다.
  
 - OpenMP : 850ms
 - CUDA : 30ms
 - SIMD : 47ms
   
   [CPU : 7800x3d, GPU : 3080ti, DDR5 : 6000MHz]  
 ![Src](/img/post/20250215/PhtometricStereo.png){: width="50%" height="50%"}