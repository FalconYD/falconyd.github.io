---
title: Branchless Code
tags:
  - C++
  - Optimize
---

## Branchless
 영상처리 개발업무를 하며 속도 최적화에 대한 고민을 많이 한다.  
 전역 변수 할당, 행단위 접근, 포인터 접근, 참조 전달, PipeLine Architecture, SIMD기법, Parallel Processing (OpenCL, CUDA, TBB)등.
 기본 언어 최적화를 진행 하지만, 대규모 데이터에서 빠른 처리를 보이는 방법은 역시 Parallel Processing이다.
 하지만, 모든 방법에는 그에 따른 비용이 발생하고 (ex. Context Switching, Overhead), 성능 목표와 목적간의 비용을 조절하는 과정이 필요하다.
 SIMD와 CUDA 같은 병렬 기법은 상대적으로 개발기간이 길어지고, 적용할 수 있는 알고리즘이 제한적이다.
 다시 최적화에 대한 이야기로 돌아와서, "if문은 성능을 저하시킨다" 라는 말을 들어왔다.
 저 말의 진위는 분기문이 많아지면, 분기 예측을 더 많이하고, 조건에 따라 실패 확률이 증가 한다.
 실패 시, 대기 명령을 flush시키고 fetch단계부터 다시 작성할 때 성능 손실이 생긴다.
<!--more-->
 그래서 Switch-Case문 등이 성능을 올려준다고 나오는 이야기가 분기예측 실패로부터 나오는 말이라고 생각한다.
 고민해보니 이미 Branchless를 알고 있었다. 인지를 못하고 있었을뿐.
 SIMD의 조건문은 비트연산으로 "_mm256_cmpgt_epi32"와 같이 결과를 0과 1로 계산을 한다. 이 부분이 일반적인 C++코드와 다르게 Branchless로 동작을 한다고 한다.
 추가로 Switch-Case의 성능 이점을 얻기위해서는 연속된 값에 대해 점프 테이블을 만들어서 성능을 향상 시킨다고 한다.(분기예측과는 다른 실행코드를 미리 작성)  
 그래서 Switch-Case를 사용할 때 주의 할 점이 연속된 값으로 (0,1,2,3 과 같은) Switch-Case를 작성 해야한다고 한다.
 (그러면, C#에서 지원하는 string type의 Switch-Case는 내부적으로 If문으로 동작 하는걸까?)  
   
 자료를 찾다보니 Branchless는 단순 속도가 빠르다로 이해하기 보다는 "어떤 조건이든 같은 실행 속도를 보장"으로 이해하는게 맞는것 같다.
 최근 컴파일러는 예측 성능이 좋아 분기 예측이 성공하면, Branchless보다 빠르기 때문에.  
  
 Branchless는 영상 처리, 정렬 알고리즘, 물리엔진 등에 성능향상을 볼 수 있다.
  
 그래서 언제 써먹을지 모르는 Branchless 코드 몇가지를 정리해놓고자 한다.

### 큰값, 작은값 (https://soohwanpeterkim.tistory.com/241)
 {% highlight c++ %}
   {% raw %} 
   #include "stdlib.h"
   #include "stdio.h"

   int branch_max(int a, int b)
   {
    return a > b ? a : b;
   }

   int branchless_max(int a, int b)
   {
    return b - (b - a) & (b - a) >> 31;
   }

   int branchless_min(int a, int b)
   {
    return a + (b - a) & (b - a) >> 31;
   }
   {% endraw %}
{% endhighlight c++ %}  

 - (b-a)>>31 : a가 크면 -1 (0xffffffff), b가 크면 0 (0x00000000).
 - (b-a)&(b-a)>>31 : a가 크면 (b-a), b가 크면 0.
 - b-(b-a)&(b-a)>>31 : a가 크면 a [b-(b-a) -> b-b+a], b가 크면 b. [b-0 -> b]

### 절대값 구하기 (https://soohwanpeterkim.tistory.com/242)
 {% highlight c++ %}
   {% raw %} 
   #include "stdlib.h"
   #include "stdio.h"

   int branch_abs(int x)
   {
    return x < 0 ? -x : x;
   }

   int branchless_abs(int a, int b)
   {
    int mask = x >> 31;
    return (x ^ mask) - mask;
   }
   {% endraw %}
{% endhighlight c++ %}  

 - mask : 양수면 0, 음수면 -1
 - x ^ mask : 양수면 그대로, 음수면 반전.
 - (x ^ mask) - mask : 양수면 x, 음수면 -x [(x 반전)-(-1) = (x 반전) + 1 = x 보수]

### 조건에 따른 값 누산.
 {% highlight c++ %}
   {% raw %} 
   #include "stdlib.h"
   #include "stdio.h"

   int main()
   {
    std::array<int, 5> values = {1,2,3,4,5};
    uint32_t sum{};

    // branch
    for (auto val : values)
    {
      if (val % 2 == 1)
      {
        sum += val;
      }
    }

    // branchless 홀수만 누산.
    sum = 0;
    for (auto val : values)
    {
      sum += (val & 1) * val;
    }

    // branchless 짝수만 누산.
    sum = 0;
    for (auto val : values)
    {
      sum += ((val & 1)^1) * val;
    }
   }
   {% endraw %}
{% endhighlight c++ %}  

 - val & 1 : 홀수면 1, 짝수면 0.
 - (val & 1) * val : 홀수만 더하고 짝수는 더하지 않음.

### 삼항 연산자 대체.
{% highlight c++ %}
   {% raw %}
   int a = 0;
   int b = 0;
   bool condition = false;

   auto result = condition ? a : b;
   auto result2 = (a & -condition) | (b & ~(-condition));

   {% endraw %}
{% endhighlight c++ %}  

 - condition이 1이면 
  - a & -condition : a [-1 = 0xffffffff]
  - b & ~(-condition) : 0 [~ -1 = 0x00000000]
 - condition이 0이면
  - a & -condition : 0 [-0 = 0x00000000]
  - b & ~(-condition) : b [~ -0 = 0xffffffff]

### Clamp
{% highlight c++ %}
   {% raw %}
   int clamp(int x, int min, int max) {
    x = x < min ? min : x;
    x = x > max ? max : x;
    return x;
    }

    int clamp_branchless(int x, int min, int max) {
    x = max ^ ((x ^ max) & -(x < max)); // Max(x, max)와 반대 논리로 적용
    x = min ^ ((x ^ min) & -(x > min));
    return x;
    }
   {% endraw %}
{% endhighlight c++ %}  

 - xor의 성질.  
  a ^ 0 = a
  a ^ a = 0

### 부호판별
{% highlight c++ %}
   {% raw %}
   int sign = (x > 0) - (x < 0);
   {% endraw %}
{% endhighlight c++ %}  
 양수면 1, 음수면 -1, 0이면 0 반환.
 (https://docs.nvidia.com/cuda/npp/core_npp.html)  
출처 - CUDA NPP