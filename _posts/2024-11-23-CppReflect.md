---
title: C++ Reflect
tags:
  - C++
  - Reflect
---

## Reflect란
<!--more-->
 Reflect는 변수의 형식과 이름을 코드상에서 논리 로직을 작성 할 수 있는 기능이다. 사용 예로 Reflect를 지원한다면 입력되는 구조체 혹은 클래스의 변수 목록을 조회하여 파일에 이름과 값을 저장하는등의 구현을 할 수 있다.

## 구현 목표
{% highlight c++ %}
  {% raw %}
  struct stTestStruct
  {
    int testvalue1;
    int testvalue2;
  };

  stTestStruct reflecttest;
  reflecttest.testvalue1 = 10;
  reflecttest.testvalue2 = 11;
  {% endraw %}
{% endhighlight %}

와 같은 코드가 있을 때, Reflect 기능을 이용하여 다음과 같은 파일로 저장을 한다.

{% highlight c++ %}
  {% raw %}
testvalue1 : 10
testvalue2 : 11
  {% endraw %}
{% endhighlight %}

## 사용 1 Reflect를 이용한 직접 구현.
  c++ 표준에서 Relection은 C++ 26에서 구현될 예정이다.
 그러나 c++ 20 컴파일러로 Reflection을 구현해놓은 라이브러리가 있다.
  C#에서 구현했던 것과 달리 C++에서는 Compile Time에 결정하는
 것이라 잘못된 구문이 있다면 컴파일 중에 에러가 난다.
 아래의 라이브러리는 헤더만 존재하여 간편하게 작성이 가능하다.

https://github.com/qlibs/reflect

※ Console에서 사용시 #include위치는 상관이 없으나, MFC에서 사용시 pch.h 혹은 stdafx.h에 헤더파일 가장 위에 배치해야한다.

## 사용 2 Reflect Cpp를 이용.
 c++20 컴파일러 기준으로 Json Library인 Reflect Cpp라는 라이브러리가 있다. 

 다음은 Refelct-Cpp의 예제에서 발췌한 예제 코드이다.
{% highlight c++ %}
  {% raw %}
  #include <rfl/json.hpp>
  #include <rfl.hpp>

  struct Person {
    std::string first_name;
    std::string last_name;
    int age;
  };

  const auto homer =
      Person{.first_name = "Homer",
            .last_name = "Simpson",
            .age = 45};

  // We can now write into and read from a JSON string.
  const std::string json_string = rfl::json::write(homer);
  auto homer2 = rfl::json::read<Person>(json_string).value();
  {% endraw %}
{% endhighlight %}

  
 라이브러리는 Cmake를 사용하여 빌드해야하고, 빌드 실패시 Visual Studio를 관리자 권한으로 실행해야한다. INSTALL 빌드위치는 C:\Program Files(x86)\에 설치된다.

 https://github.com/getml/reflect-cpp