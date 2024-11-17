---
title: AI 평가 지표 (SimpleNet)
tags:
  - AI
  - DeepLearning
  - Evalution
---

## Evalution?
<!--more-->
 AI 모델을 학습을 할 때, 모델의 가중치가 얼마나 학습이 되었는지 평가가 필요하다.
 Python의 경우 sklearn 라이브러리의 metric을 사용하여 항목을 평가한다.
 작업 중인 SimpleNet Test항목에서는 다음의 지표가 사용된다.
  - roc_auc_score
  - precision_recall_curve
  - auc
  - f1_score

 상기 지표는 이진 분류에 사용되는 평가지표이다.

## F1 Score와 재현율/정밀도 관계
 
 $$\textcolor{Black}{
    Precision = \frac{TP}{FP+TP} \\
    Recall =  \frac{TP}{FN+TP}
}$$
  정밀도와 재현율은 완벽한 평가 지표가 아니다.

  $$\textcolor{Black}{
    F1 Score = \frac{2}{\frac{1}{Recall} + \frac{1}{Precision}} = \frac{2*Precision*Recall}{Prcision + Recall} \\
}$$

## ROC Curve와 AUC
 ROC 곡선은 FPR가 변할 때, TPR가 어떻게 변하는지 나타나는 곡선.
 AUC는 ROC 적분값이다. (ROC 아래 면적 값.)
 
 FPR (False Positive Rate) : 실제 음성인 것중에서, 양성으로 잘못 예측한 비율. - 낮을수록 좋음.

 TPR (True Positive Rate) : 재현율/민감도, 실제 양성인 것 중에서 양성으로 맞게 예측한 비율. - 높을수록 좋음.

  $$\textcolor{Black}{
    FPR = \frac{FP}{FP+TN} \\
    TPR =  \frac{TP}{FN+TP}
}$$

참고1 : https://velog.io/@gangjoo/ML-%ED%8F%89%EA%B0%80-F1-Score%EC%99%80-ROC-AUC#auc

참고2 : https://jennainsight.tistory.com/entry/F1-Score-Roc%EA%B3%A1%EC%84%A0-Auc-%EA%B3%84%EC%82%B0%EB%B0%A9%EB%B2%95-scikit-learn-%EC%BD%94%EB%93%9C%EB%A1%9C-%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0
