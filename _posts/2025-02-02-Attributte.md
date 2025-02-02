---
title: C++ Attribute
tags:
  - C++
---
## Attribute
<!--more-->
 컴파일러에 추가 정보를 전달하는 구문으로 [[attributes]]라고 쓴다.
 컴파일러에 의한 최적화 경고, 경고 추가 등을 할 수 있다.

### C++11
 - noreturn : 함수가 결코 반환 하지 않는것을 표시하는 속성. 예외 송출, std::exit, std::abort의 wrapper함수에 붙임.
  
  {% highlight c++ %}
    {% raw %}
    [[noreturn]] void exit()
    {
      std::exit(0);
    }
    {% endraw %}
  {% endhighlight c++ %}
   
  - carries_dependency : 병렬 프로그래밍의 atomic 조작에 있어서 값에 의존하는 순서를 함수 전반에 전파하는 것을 나타내는 속성이다.
  
{% highlight c++ %}
    {% raw %}
    [[carries_dependency]] Hoge* is_load()
    {
        return x.load(std::memory_order_consume);
    }

    int main()
    {
        std::thread th([]() {
            x.store(new Hoge{ 100 }, std::memory_order_release);
        });

        Hoge* pHoge = is_load();
        if (pHoge)
        {
            std::cout << pHoge->hoge;
        }
        th.join();
        return 0;
    }    
    {% endraw %}
  {% endhighlight c++ %}

### C++14
 - deprecated : 해당 기능이 비추천을 경고. deprecated("reason") 으로 경고 메시지 출력.

### C++17
 - fallthrough : switch - case 문에서 의도적인 통과를 명시.
  
 {% highlight c++ %}
    {% raw %}
    void func(int i)
    {
      switch (i)
      {
      case 1: 
          ret += 1;
          [[fallthrough]]; // 의도적인 통과
      case 2:
          ret += 2; 
          break;
      }
    }
    {% endraw %}
  {% endhighlight c++ %}
  
  - maybe_unused : 파라메터가 사용되지 않는 경고를 억제.
  
 {% highlight c++ %}
    {% raw %}
    void func([[maybe_unused]]int param)
    {
        // param는 사용되지 않는다
    }
    {% endraw %}
  {% endhighlight c++ %}
  
  - nodiscard : 반환값을 사용하지 않을 경우 경고 발생.
    
  {% highlight c++ %}
    {% raw %}
    [[nodiscard]]int * create()
    {
        return new int(0);
    }

    int main()
    {
        create(); // warning
        return 0;
    }
    {% endraw %}
  {% endhighlight c++ %}

### C++20
 - no_unique_address : 데이터 멤버가 이 클래스의 다른 모든 비 정적 데이터 멤버와 서로 다른 주소를 가질 필요가 없다는 것을 나타내는 특성.
 - likely, unlikely : 조건문 최적화 힌트. 선택 빈도가 높은 조건문에 likely를 적용하여 최적화 힌트를 준다.

 참고 : C++ - 표준 attribute 정리 https://jacking75.github.io/cpp_modern_cpp_attribute/