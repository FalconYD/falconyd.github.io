---
title: Cpp Optimize
tags:
  - C++
  - Optimize
---
## C++ Optimize
<!--more-->
 Photometric Stereo를 테스트 중에 특이한 현상을 확인 하였다.
 같은 구현 이지만, 실행 속도가 다른 현상.

### 기존 코드
   [https://github.com/NewProggie/Photometric-Stereo](https://github.com/NewProggie/Photometric-Stereo)
     
   {% highlight c++ %}
    {% raw %}
    matI.setTo(cv::Scalar::all(0));
    for (int i = 0; i < m_matSrc.size(); i++)
    {
            matI.at<float>(i) = m_matSrc[i].at<uchar>(y, x);
    }

    cv::Mat n = lightinv * matI;

    float p = cv::sqrt(cv::Mat(n).dot(n)); // albedo?

    if (p > 0) { n = n / p; }
    if (n.at<float>(2, 0) == 0) { n.at<float>(2, 0) = 1.0; }

    m_matAlbedo.at<float>(y, x) = p;
    m_matNormal.at<cv::Vec3f>(y, x) = n;
    m_matP.at<float>(y, x) = n.at<float>(0, 0) / n.at<float>(2, 0);
    m_matQ.at<float>(y, x) = n.at<float>(1, 0) / n.at<float>(2, 0);
    {% endraw %}
  {% endhighlight c++ %}

### 수정 코드

{% highlight c++ %}
    {% raw %}
    n[0] =
            lightinv.at<float>(0) * m_matSrc[0].at<uchar>(y, x) +
            lightinv.at<float>(1) * m_matSrc[1].at<uchar>(y, x) +
            lightinv.at<float>(2) * m_matSrc[2].at<uchar>(y, x) +
            lightinv.at<float>(3) * m_matSrc[3].at<uchar>(y, x);
    n[1] =
            lightinv.at<float>(4) * m_matSrc[0].at<uchar>(y, x) +
            lightinv.at<float>(5) * m_matSrc[1].at<uchar>(y, x) +
            lightinv.at<float>(6) * m_matSrc[2].at<uchar>(y, x) +
            lightinv.at<float>(7) * m_matSrc[3].at<uchar>(y, x);
    n[2] =
            lightinv.at<float>(8) * m_matSrc[0].at<uchar>(y, x) +
            lightinv.at<float>(9) * m_matSrc[1].at<uchar>(y, x) +
            lightinv.at<float>(10) * m_matSrc[2].at<uchar>(y, x) +
            lightinv.at<float>(11) * m_matSrc[3].at<uchar>(y, x);

    float p = std::sqrt(n[0] * n[0] + n[1] * n[1] + n[2] * n[2]);

    if (p > 0)
    {
            n[0] = n[0] / p;
            n[1] = n[1] / p;
            n[2] = n[2] / p;
    }
    if (n[2] == 0) { n[2] = 1.0; }

    {
      m_matAlbedo.at<float>(y, x) = p;
      m_matNormal.at<cv::Vec3f>(y, x)[0] = n[0];
      m_matNormal.at<cv::Vec3f>(y, x)[1] = n[1];
      m_matNormal.at<cv::Vec3f>(y, x)[2] = n[2];
      m_matP.at<float>(y, x) = n[0] / n[2];
      m_matQ.at<float>(y, x) = n[1] / n[2];
    }
    {% endraw %}
  {% endhighlight c++ %}
 상기 두 코드는 같은 동작을 하는 코드이다.

 기존 코드는 약 700ms이 걸리고, 수정 코드는 170ms이 걸린다.
 왜일까하고 수정 코드를 작성하며 고민하였을 때,  
  - 상수를 사용하는 코드
  - Raw 코드(수정 코드)가 라이브러리 코드 보다 최적화의 여지가 더 있음.  
    
  정도로 정리를 해보았다.
 SIMD로 같은 코드를 작성하였을 때, 단일 쓰레드로는 160ms정도 걸렸다.  
 수정 코드의 경우는 OpenMP를 사용하였을 때, 연산이 잘 안되었으나 SIMD는 정상적으로 처리 하였다.

 ※ 컴파일은 Visual Studio 2022로 하였다.