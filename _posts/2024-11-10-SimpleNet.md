---
title: Simple Net
tags:
  - AI
  - DeepLearning
  - Anormaly Detection
---

## Anormaly Detection?
<!--more-->
 Anormaly Detection은 간단하게 정상을 학습시켜 비정상(결함)을 검출해내는 DeepLearning 방법이다.
 AutoEncorder등의 여러 방법이 있으나, 그 중 SimpleNet을 알아본다.

 논문 및 구현 : https://github.com/DonaldRR/SimpleNet
 구현 : https://github.com/jahongir7174/SimpleNet


## SimpleNet
 SimpleNet은 기존의 Anormaly Detection을 소개하고 문제점을 지적하고 해당 문제를 해결한 내용을 소개한다.
 
  1. Reconstruction-Based 
   - Auto-Encorder와 같은 방식으로, 입력 이미지를 "잘 재구성" 되서는 안된다.
   - "잘 재구성"된다는 의미는 결함 이미지를 입력 했을 때, 결함 이미지가 그대로 재구성 되는것을 의미한다.
   - 이유는 Diff(재구성 이미지, 입력 이미지)에서 결함이 검출되어야 하는데, 결함이미지가 그대로 재구성 될 경우, 결함을 감지하지 못한다.
   - 결함 이미지가 정상 훈련 이미지와 로컬에지등을 공유하거나, 디코더가 일부 결함 이미지 인코딩을 "잘 재구성"될정도로 강하다면 결함감지를 못한다.
  2. Synthsizing-Based
   - 결함이 없는 이미지에서 결함 이미지를 합성한다.
   - DR/EM, Cutpaste등을 소개한다. (해당 하는 모델을 잘 모르기에 패스)
   - 문제점은 합성 결함이 실제 결함과 밀접하지는 않는문제.
  3. Embedding-Based
   - Embedding 기반은 최근 SOTA를 달성하며 좋은 성능을 보인다.
   - 일반적으로 Image-Net에서 사전 훌련된 네트워크를 사용한다.
   - PaDiM, Patch-Core등을 소개하며 메모리 뱅크 기법을 언급한다.
   - Distillation을 소개하면 GAN의 계산량이 2배인 단점을 지적한다.

 SimpleNet은 
  - CNN의 편향을 완화하기위해 대상데이터세트에서 전이학습수행
  - 이미지에서 직접합겅하는 대신 특징 공간에서 비정상을 합성할 것을 제안
  - 추론시 Generator를 사용하지 않아 추론 성능 향상.

  등의 방법을 제안한다.

## SimpleNet 구현
 논문 저자는 Feature Extractor, Feature Adaptor, Anomalous Feature Generator, Discriminator로 소개하고 있다.
 - Feature Extractor : 집계함수를 사용하여 근접 특징을 집계하면 로컬 특징을 얻는다. 서로 다른 계층의 특징맵을 결합하기위해 모든 특징맵은 동일한 크기로 선형 조정된다. 특징맵을 채널별로 연결하기만 하면 특징맵이 제공된다.
 - Feature Adaptor : 산업용 이미지는 사전학습된 데이터 세트와 다른 분포를 가지므로 학습특징을 대상 도메인으로 전송하기 위해 Feature Adaptor G_0를 채택한다. 논문에서는 실험적 결과로 Single Fully-Connected가 나은 성능을 낸다고 하였다.

## Libtorch로 변환
 - 현재 https://github.com/jahongir7174/SimpleNet의 코드기반으로 변환 중이다.
 - https://github.com/DonaldRR/SimpleNet와 https://github.com/jahongir7174/SimpleNet가 정확히 같은 구현인지는 검증 중이다.
 - 현재 ResNet 특징 추출부분을 기존코드의 ResNet으로 대치하였는데, 해당부분이 정확한 구현인지는 검증중 이다.