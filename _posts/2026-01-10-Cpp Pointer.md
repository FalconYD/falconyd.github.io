---
title: Cpp Pointer
tags:
  - C++
  - Optimize
---

## Pointer
 Mordern C++ 메모리 할당 방법.
<!--more-->
### Traditional
 C스타일에서 힙메모리의 할당 해제는 new,delete를 쌍으로 구성하여 메모리 할당과 해제를 수행한다. 
 delete가 누락 되면 메모리 릭이 발생 한다. 메모리 해제시에는 포인터에 nullptr을 명시적으로 입력하여 해제를 확인해야한다.

{% highlight c++ %}
   {% raw %}
   // 할당
   T* value = new T();

   // 대입
   *value = 10;

   // 이동
   T* value_new = value;

   // 해제
   if (value != nullptr)
   {
    delete T;
    T = nullptr;
   }

   // 배열
   T* value2[10] = new T[10];

   // 할당
   value2[0] = new T();
   value2[0] = 11;

   // 해제
   if (value2[0] != nullptr)
   {
    delete value2[0];
    value2[0] = nullptr;
   }

   if (value2 != nullptr)
   {
    delete[] value2;
    value2 = nullptr;
   }
   {% endraw %}
{% endhighlight c++ %}  

### unique_ptr<T>
 스코프 종료시 메모리 해제를 보장하는 함수이다. 메모리의 소유권 개념을 도입하여 다른 포인터에서 해제를 방지한다. 소유권을 이전 할 때에는 std::move함수를 사용하여 소유권을 이전하고, 이전 한 소유권의 메모리 해제 시점에 같이 해제 하기도 한다.
 
{% highlight c++ %}
   {% raw %}
   {
    // 할당 
    // auto value = std::make_unique<T>(); // 할당 2
    std::unique_ptr<T> value = std::make_unique<T>();

    // 대입
    value = 1;

    // 이동 (소유권 이전)
    std::unique_ptr<T> value_new = std::move(value);

   }// 해제
   {% endraw %}
{% endhighlight c++ %}  

### shared_ptr<T>, weak_ptr<T>
 스코프 종료시 메모리를 해제를 하지만, 참초 카운터가 0일 때 메모리를 해제한다. 순환참조시 해제를 못할 수 있어 참조용 pointer가 필요할 때는 weak_ptr을 사용하여 운용한다.  
 shared_ptr을 사용할 때에는 Control Block을 유의 하여야한다. 메모리 자동 해제를 위해 참초 카운터를 증감시키는데, shared_ptr을 생성할 때 Control Block을 생성한다.

{% highlight c++ %}
   {% raw %}
   {
    // 할당 
    // auto value = std::make_shared<T>(); // 할당 2
    std::shared_ptr<T> value = std::make_shared<T>();

    // 대입
    value = 1;

    // weak ptr은 shared ptr로부터만 유효. ex. new로 생성한 애는 받을수 없음 -> Ctrl Block이 없으므로.
    std::weak_ptr<T> ref_value = value;

   }// 해제
   {% endraw %}
{% endhighlight c++ %}  