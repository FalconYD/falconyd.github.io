---
title: Canny Edge
tags:
  - ImageProcessing
  - Book
  - Edge
---

## Canny가 제시한 좋은 에지 알고리즘이 갖추어야할 세가지
  1. 최소 오류율 : 거짓 긍정과 거짓 부정이 최소여야 한다. 즉, 없는 에지가 생성되거나 있는 에지를 못 찾는경우를 최소로 유지해야 한다.
  2. 위치 정확도 : 검출된 에지는 실제 에지의 위치와 가급적 가까워야 한다.
  3. 에지 두께 : 실제 에지에 해당하는 곳에는 한 두께의 에지만 생성해야 한다.

{% highlight c++ linenos%}
    {% raw %}
// 알고리즘 3-3 캐니 에지 검출(스케치버전)
//입력 : 영상 f(j,i), 0≤ j ≤ M-1, 0 ≤ I ≤ N-1, 가우시안의 편차
//출력 : 에지 영상 e(j,i), 0 ≤ j ≤ M-1, 0 ≤ I ≤ N-1 // 에지는 1, 비에지는 0인 이진 영상
 입력 영상 f에 σ 크기의 가우시안 스무딩을 적용한다.
 결과 영상에 소벨 연산자를 적용하여 에지 강도와 에지 방향 맵을 구한다.
 비최대 억제를 적용하여 얇은 두께 에지 맵을 만든다.
 이력 임계값을 적용하여 거짓 긍정을 제거한다.
   {% endraw %}
{% endhighlight %}

## Cany Edge에서 Tlow와 Thigh의 의미
   - 캐니 알고리즘은  두 개의 임계값  Tlow와 Thigh를 쓰는 이력 임계값hysteresis thresholding 방법을 적용한다. 에지 추적은 Thigh를 넘는 화소에서 시작한다. 즉 신뢰도가 높은 화소만 에지 추적을 시작할 권리를 얻는다. 시작 화소가 정해지면 Thigh를 넘는 화소를 대상으로 에지를 추적한다. 즉, 이웃 화소가 추적 이력이 있으면 자신의 신뢰도가 낮더라도 에지로 간주되는 것이다. 캐니는 Thigh를 Tlow의 2~3배로 할 것을 권고한다.
![그림](/img/post/20201125/01.png){: width="100%" height="100%"}

{% highlight html linenos%}
    {% raw %}
//알고리즘 3-4 캐니 에지 검출
//입력 : 영상 f(j,i), 0<=j<=M-1, 0<=i<=N-1, 가우시안의 표준편차 σ, 이력 임계값 Thigh, Tlow
//출력 : 에지 영상 e(j,i), 0<=j<=M-1, 0<=i<=N-1 // 에지는 1, 비에지는 0인 이진 영상
//  f에 크기  σ인 가우시안을 적용하여 g를 얻는다.
//  g에 소벨 연산자를 적용하여, 에지 강도맵 S와 에지 방향 맵 D를 얻는다. // D는 8-방향 양자화
 
  //5~9행:비최대 억제
 for(y=1 to M-2)
   for(x=1 to N-2){
    (y1,x1)과 (y2,x2)를 (y,x)의 두 이웃 화소라 하자. // [그림 3-17] 참고
    if(S(y,x)<=S(y1,x1) or S(y,x)<=S(y2,x2)) S(y,x)=0; //비최대 억제
  }
 
 //12~16행:이력 임계값을 이용한 에지 추적
 e(y,x) = 0, 0<=y<=M-1,0<=x<=N-1;
 visited(y,x)=0,0<=y<=M-1,0<=x<=N-1; // 모든 화소가 아직 방문 안됨을 표시
 for(y=1 to M-2)
  for(x=1 to N-2)
   if(S(y,x)>Thigh and visited(y,x)=0) follow_edge(y,x);
 
 // 에지를 추적하는 재귀함수(배열은 모두 전역변수라 가정)
 function flow_edge(y,x) {
  visited(y,x) = 1; // 방문했음을 표시
  e(y,x) = 1; // 에지로 판정
  for(y,x)의 8이웃 (ny,nx) 각각에 대해)
   if(S(ny,nx>Tlow and visited(y,x)=0) follow_edge(ny,nx)
 }
   {% endraw %}
{% endhighlight %}

## SPTA (Safe Point Thinning Algorithm) - 세선화
  - 그림 3-23식에서 ni가 1(에지)이면 참, 0(비에지)이면 거짓을 뜻하며 ni'는 부정, +는 or,  · 은 and이다. 논리식 s4가 참이면 네 개 마스크 어느것과도 매치가 안된셈이다. 다시 말해 n4가 0이고 s4가 참이면, p는 제거 대상으로 표시한다. 이러한 판단을 n4가 0인 그룹뿐 아니라 n0가 0인 그룹, n2가 0인 그룹, 그리고 n6가 0인그룹 각각에 적용하여 어느 그룹에서 라도 제거 대상이라고 표시되면 p를 비에지로 바꾼다.
![그림](/img/post/20201125/02.png){: width="100%" height="100%"}

{% highlight c++ linenos%}
    {% raw %}
//알고리즘 3-5 SPTA(껍질을 한 번 벗기는 연산)
//입력 : 에지 영상 e(j, i), 0 ≤ j ≤ M - 1, 0 ≤ i ≤ N - 1 // 에지는 1, 비에지는 0인 이진 영상
//출력 : 한 두께 얇아진 에지 영상 eout(j, i), 0 ≤ j ≤ M - 1, 0 ≤ i ≤ N - 1
  e를 eout에 복사한다.
  for (j = 1 to M - 2)
  for (i = 1 to N - 2) {
  	if (e(j, i) = 1 and
  		((n0' and(n4 and(n5 or n6 or n2 or n3) and (n6 or n'7) and (n2 or n'1))) // n0=비에지, s0=참
  		or (n'4 and (n0 and (n1 or n2 or n6 or n7)and(n2 or n'3) and (n6 or n'5))) // n4=비에지, s4=참
  		or (n'2 and(n6 and(n7 or n0 or n4 or n5)and(n0 or n'1) and (n4 or n'3))) // n2=비에지, s2=참
  		or (n'6 and (n2 and (n3 or n4 or n0 or n1)and(n4 or n'5) and (n0 or n'7))))) // n6=비에지, s6=참
  			eout(j, i) = 0;
 }
   {% endraw %}
{% endhighlight %}

## 에지 추적

![그림](/img/post/20201125/03.png){: width="100%" height="100%"}
  - [그림 3-25] 분기점을 골라내는 규칙 : 시계방향으로 조사한다고 할 때 에지에서 비에지로 전환하는 곳을 화살표로 표시하였다. 그러면 a와c는 두번의 전환이 있고 b는 세번의 전환이 발생한다. [알고리즘 3-6]은 세 번 이상 전환이 생기는 점을 분기점으로 간주한다. 따라서 b가 분기점이 된다. 한편, 끝점은 전환 횟수가 1인점으로 하면 된다.

![그림](/img/post/20201125/04.png){: width="100%" height="100%"}
  - [그림 3-26] 22행 전방 화소 조사 규칙 : 예를 들어 방향 dir이 0이라면 앞에 있는 세 화소가 전방 화소가 된다.

{% highlight c++ linenos%}
    {% raw %}
//알고리즘 3-6 에지 토막 검출
//입력 : 에지 영상 e(j, i), 0 ≤ j ≤ M - 1, 0 ≤ i ≤ N - 1
//출력 : segment(k), 1 ≤ k ≤ n(n개의 에지 토막)
 Q = Φ; // 큐를 생성하고 공집합으로 초기화
 for (j = 1 to M - 2)
 for (i = 1 to N - 2) {
  (j, i)의 전환 횟수를 세어 c라 한다.
  if (c = 1 or c >= 3) // 끝점 또는 분기점
   for (dir = 0 to 7) // 8-방향을 조사하여 추적 방향을 정하고 큐에 저장
    if (dir 방향의 이웃 화소(y, x)가 e(y, x) = 1) addQueue(Q, (j, i, dir));
 }
 
 // 큐에 들어있는 요소에서 에지 추적을 시작(에지 토막 추적 시작)
 n = 0; // 에지 토막의 개수
 visited(j, i) = 0, 0 <= j <= M - 1, 0 <= i <= N - 1; // 이중 추적을 방지하기 위해 사용(방문하면 1로 바꿈)
 while (Q≠Φ) {
 	(y, x, dir) = popQueue(Q);
 	(y, x)의 dir방향의 이웃을(cy, cx)라 하자.
 	if visited(cy, cx) continue; // 반대쪽에서 이미 추적했음.
 	n++;
 	segments(n)을 생성하고(y, x)와(cy, cx)라 하자.
 	visited(y, x) = visited(cy, cx) = 1; // 방문했음을 표시
 	if ((cy, cx)가 끝점 또는 분기점) continue; // 두 점으로 구성되는 짧은 에지 토막임
 	while (true) {
 		(cy, cx)의 dir 방향의 전방 화소를 조사한다. // [그림 3-26]에서 f 표시된 화소들
 		if (끝점 또는 분기점(y, x)가 있으면) {
 			segments(n)에(y, x)를 추가한다.
 			visited(y, x) = 1;
 			break; // segments(n)의 추적이 끝났음
 		}
 		else { // 전환 횟수가 2인 통과점
 			전방 화소 중 e(y, x) = 1인 화소를 찾는다.
 			segments(n)에(y, x)를 추가한다.
 			visited(y, x) = 1;
 			dir을(cy, cx)에서(y, x)로 진행하는 방향으로 갱신하다.
 			(cy, cx) = (y, x); // 앞으로 이동
 		}
 	}
 }
   {% endraw %}
{% endhighlight %}

## 선분근사
  - [알고리즘 3-6]으로 검출한 에지 토막edge segment은 직선으로 근사화 하여 선분(직선 토막)line segment으로 변환할 수 있다.[ 그림 3-27]은 간단한 재귀 알고리즘을 설명한다. 양 끝점을 연결한 직선으로부터 에지 토막의 가장 먼점까지의 거리 h를 계산한다. 이것이 임계값보다 크면 가장 먼 점을 중심으로 두 토막으로 분할하고, 두 토막 각각에 같은 과정을 재귀적으로 적용한다. 임계값이내가 된 토막은 분할을 멈춘다.
![그림](/img/post/20201125/05.png){: width="100%" height="100%"}

![출처](/img/post/Feature_00.jpg){: width="200" height="100%"}

출처 - Computer Vision
