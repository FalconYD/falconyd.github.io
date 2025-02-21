---
title: Shape Analysis
tags:
  - Image Processing
  - Shape Analysis
---
## 형상 분석
<!--more-->
 영상 처리 에서 전처리 이후 blob 과정을 거친 이후 형상을 분석할 수 있는 지표들을 정리한다.

### 자주 쓰이는 용어
- **경계 박스(Bounding Box)**: 개체를 둘러싸는 직사각형 경계 박스
  {% highlight c++ %}
    {% raw %}
    // Find the minimum area enclosing bounding 
    cv::RotatedRect box = cv::minAreaRect(points);

    // Find Bouding box
    cv::Rect box = cv::boundingRect(points);
        {% endraw %}
  {% endhighlight c++ %}

- **볼록 껍질(Convex Hull)**: 개체를 포함하는 가장 작은 볼록 도형

  {% highlight c++ %}
    {% raw %}
      vector<vector<Point> > contours;
    findContours( canny_output, contours, RETR_TREE, CHAIN_APPROX_SIMPLE );
    vector<vector<Point> >hull( contours.size() );
    for( size_t i = 0; i < contours.size(); i++ )
    {
        convexHull( contours[i], hull[i] );
    }
    {% endraw %}
  {% endhighlight c++ %}

### 거리(Distance) 관련 형상 지표
- 유클리드(Euclidean) 거리: 
  

$$\textcolor{Black}{
  d = \sqrt{(x_1 - x_2)^2 + (y_1 - y_2)^2}
}$$


- 체비쇼프(Chebyshev) 또는 체스판(Chessboard) 거리: 
  

$$\textcolor{Black}{
  d = \max(|x_1 - x_2|, |y_1 - y_2|)
}$$


- 맨해튼(Manhattan) 또는 도시블록(City-block) 거리: 
  

$$\textcolor{Black}{
  d = |x_1 - x_2| + |y_1 - y_2|
}$$


  
### 면적(Area) 관련 형상 지표
- 면적: 개체를 구성하는 픽셀 개수
- 볼록(Convex) 면적: 개체를 둘러싸는 볼록 껍질의 면적
  {% highlight c++ %}
    {% raw %}
    bool oriented = false; // false : 절대값, true : 윤곽선 진행방향에 따른 부호호
    double dArea = cv::contourArea(points, oriented);
    {% endraw %}
  {% endhighlight c++ %}

### 축(Axis) 관련 형상 지표
- 장축(Major Axis): 개체를 통해 그릴 수 있는 가장 긴 선의 끝점
- 단축(Minor Axis): 장축과 수직이면서, 개체를 통해 그릴 수 있는 가장 짧은 선의 끝점

### 길이(Length) 관련 형상 지표
- 둘레(Perimeter): 개체의 경계를 구성하는 픽셀 개수
- 볼록(Convex) 둘레: 개체를 둘러싸는 볼록 껍질의 둘레
- 장축 길이 또는 개체 길이: 장축 끝점 사이의 픽셀 거리
- 단축 길이 또는 개체 너비: 단축 끝점 사이의 픽셀 거리
- Fiber 길이: 
  
$$\textcolor{Black}{
  Fiber Length = Perimeter - \sqrt{Perimeter^2 - 16 \cdot {Area}}
}$$
  {% highlight c++ %}
    {% raw %}
    // Perimeter
    bool isclosed = true; // 폐곡선 여부.
    double len = cv::arcLength(points, isclosed);

    // Major, Minor Axis
    // 직선 객체에 적합.
    RotatedRect rect = cv::minAreaRect(points);
    // 원형 객체에 적합.
    RotatedRect rect = cv::fitellipse(points);
    {% endraw %}
  {% endhighlight c++ %}

### 각도(Angle) 관련 형상 지표
- 장축 각도 또는 개체 방향: 개체의 장축과 X축 사이의 각도
  

$$\textcolor{Black}{
  Major Axis Angle = \arctan\left(\frac{y_2 - y_1}{x_2 - x_1}\right)
}$$

### Compactness
- 개체와 동일한 둘레를 갖는 원의 면적에 대한 개체 면적의 비율
  

$$\textcolor{Black}{
  Compactness = \frac{4 \pi \cdot {Area}}{Perimeter^2}
}$$



### 신장률(Elongation)
- 개체 경계 상자(bounding box)의 길이와 너비 간 비율
  
$$\textcolor{Black}{
    Elongation = \frac{Length}{Bounding Box Width}
}$$

### 이심률(Eccentricity)
- 개체의 단축 길이에 대한 장축 길이의 비율
  

$$\textcolor{Black}{
  Eccentricity = \frac{Major Axis Length}{Minor Axis Length}
}$$



### 원형도(Circularity or Roundness)
- 개체와 동일한 볼록 둘레를 갖는 원의 면적에 대한 개체 면적의 비율
  

$$\textcolor{Black}{
  Circularity or Roundness = \frac{4 \pi \cdot Area}{Convex Perimeter^2}
}$$



### 구형도(Sphericity)
- 개체가 구 모양에 가까운 정도
  

$$\textcolor{Black}{
  Sphericity = \frac{R_{Inscribing}}{R_{circumscribing}}
}$$

 {% highlight c++ %}
    {% raw %}
    cv::Point2f center;
    float radius;
    cv::minEnclosingCircle(points, center, raidus);
    {% endraw %}
  {% endhighlight c++ %}

### 볼록도(Convexity)
- 개체가 볼록한 개체와 얼마나 다른지 나타내는 상대적인 비율
  

$$\textcolor{Black}{
  Convexity = \frac{Convex Perimeter}{Perimeter}
}$$



### 종횡비(Aspect Ratio)
- 개체 높이와 너비의 비율
  

$$\textcolor{Black}{
  Aspect Ratio = \frac{Height}{Width}
}$$



### Curl
- 개체가 말려있는 정도
  

$$\textcolor{Black}{
  Curl = \frac{Length}{Fiber Length}
}$$



### 견고성(Solidity)
- 개체의 밀도를 나타냄
  

$$\textcolor{Black}{
  Solidity = \frac{Area}{Convex Area}
}$$


 참고 : 형상 분석과 측정 https://data-science-note.tistory.com/88