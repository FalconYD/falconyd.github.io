---
title: Decorator Pattern
tags:
  - Decorator
  - Python
  - C++
---
## Python에서의 Decorator
<!--more-->
 Python에서는 '@'로 시작하는 문법이 있다.  
  {% highlight python %}
    {% raw %}
    import datetime

    def datetime_decorator(func):
      def decorated():
        print datetime.datetime.now()
        func()
        print datetime.datetime.now()
      return decorated

    @datetime_deco
    def function_1():
      print "func1"
    {% endraw %}
  {% endhighlight python %}

 동작 방식은 'def decorated()' 안의 내용을 그대로 실행한다.  
 즉 'functino_1'을 실행하기전 datetime을 출력, 실행 후 datetime을 출력한다.  
 이 동작의 의미는 함수단위일 경우 함수의 기능을 유지한채 일련의 추가작업을 정갈하게 수행해내는데 있다.
 (Class동작도 있다)

## C++에서의 Decorator
 C++에서는 @같은 문법은 없으나, 디자인 패턴이 존재한다.
 
  {% highlight c++ %}
    {% raw %}
struct Logger {
public:
  Logger(const std::function<void()> &func, const std::string &name) : m_func(func), m_name(name) {}

  void operator()() const {
    std::cout << "Entering " << m_name << std::endl;
    m_func();
    std::cout << "Exiting " << m_name << std::endl;
  }

public:
  std::function<void()> m_func;
  std::string m_name;
};

int main(void) {
  Logger([]() {
    std::cout << "Hello" << std::endl;
      }, 
      "HelloFunction")();
  /** 출력 결과
  * Entering HelloFunction
  * Hello
  * Exiting HelloFunction
  */
}
    {% endraw %}
  {% endhighlight c++ %}

  위 코드는 '반환 타입이 없는 함수'의 Decorator Pattern이다. Class(struct) 생성자에 'std::function' 인자, 즉 함수포인터를 받아 함수 전,후에 문자열을 출력한다.

참고 : C++ Decorator https://suri78.tistory.com/350  
참고 : Python Decorator https://bluese05.tistory.com/30