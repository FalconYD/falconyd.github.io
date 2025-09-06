---
title: C++ Dll C# Interface
tags:
  - C++
  - C#
---

## C#, C++
 요즘 프로젝트에서 자주 사용하는 구성은 WPF UI에 C++ DLL로 프로세싱 구성을 한다.
 이 방법이 장비 프로그램의 유연성과 호환성은 좋지만 C#에서 C++ DLL을 호출하는 인터페이스를 구성할 때 자료 구조를 매칭시켜줘야한다.  
 또한 C#과 C++간의 호출 시 마샬링의 오버헤드가 0는 아니기에 가급적 적은 횟수의 DLL 함수를 호출하는게 어플리케이션 성능 최적화에 도움이 된다. 단일 값을 받아오기보다는 데이터를 묶어서 한번에 받아오는 방식이 유리한 것 같다.
<!--more-->
 
### 반환이 없고 매개변수가 없을 때

 {% highlight c++ %}
   {% raw %}
    //----------------------------------------------------------------------
    // C#
    //----------------------------------------------------------------------
    [DllImport(strDllName, CharSet = CharSet.Ansi, CallingConvention = CallingConvention.Cdecl)]
    public static extern void TestFunc();
    //----------------------------------------------------------------------

    //----------------------------------------------------------------------
    // cpp Header
    //----------------------------------------------------------------------
    #pragma once
    #ifdef DLLEXPORT
    #define DLLTYPE extern "C" __declspec(dllexport)
    #else
    #define DLLTYPE extern "C" __declspec(dllimport)
    #endif
    DLLTYPE void TestFunc();
    //----------------------------------------------------------------------
    
    //----------------------------------------------------------------------
    // Source
    //----------------------------------------------------------------------
    #include "pch.h"
    #define DLLEXPORT
    #include "dllmain.h"
    DLLTYPE void TestFunc()
    {
      // do something.
    }
    //----------------------------------------------------------------------
   {% endraw %}
 {% endhighlight c++ %}
 
### 반환이 없고 매개변수가 있을 때 (기본형)
 소스 부분 생략.  

 {% highlight c++ %}
   {% raw %}
    //----------------------------------------------------------------------
    // C#
    //----------------------------------------------------------------------
    [DllImport(strDllName, CharSet = CharSet.Ansi, CallingConvention = CallingConvention.Cdecl)]
    public static extern void TestFunc(short val1, int val2, long val3, float val4, double val5);
    //----------------------------------------------------------------------

    //----------------------------------------------------------------------
    // cpp Header
    //----------------------------------------------------------------------
    #pragma once
    #ifdef DLLEXPORT
    #define DLLTYPE extern "C" __declspec(dllexport)
    #else
    #define DLLTYPE extern "C" __declspec(dllimport)
    #endif

    DLLTYPE void TestFunc(short val1, int val2, int64_t val3, float val4, double val5);
    //----------------------------------------------------------------------
   {% endraw %}
 {% endhighlight c++ %}


### 반환이 있고 매개변수가 있을 때
 소스 부분 생략.  
 반환값을 받아서 사용 할 수도 있으나, 일관성과 안정성을 위해 out타입으로 반환값을 받아온다.
 메모리 제어 주체를 C#으로 하기 위함도 있다.

 {% highlight c++ %}
   {% raw %}
    //----------------------------------------------------------------------
    // C#
    //----------------------------------------------------------------------
    [DllImport(strDllName, CharSet = CharSet.Ansi, CallingConvention = CallingConvention.Cdecl)]
    public static extern void TestFunc(double input, out double output);
    //----------------------------------------------------------------------

    //----------------------------------------------------------------------
    // cpp Header
    //----------------------------------------------------------------------
    #pragma once
    #ifdef DLLEXPORT
    #define DLLTYPE extern "C" __declspec(dllexport)
    #else
    #define DLLTYPE extern "C" __declspec(dllimport)
    #endif

    DLLTYPE void TestFunc(double input, double* output);
    //----------------------------------------------------------------------
   {% endraw %}
 {% endhighlight c++ %}

### 반환이 없고 매개변수가 있을 때 (문자열)
 소스 부분 생략.  

 {% highlight c++ %}
   {% raw %}
    //----------------------------------------------------------------------
    // C#
    //----------------------------------------------------------------------
    [DllImport(strDllName, CharSet = CharSet.Ansi, CallingConvention = CallingConvention.Cdecl)]
    public static extern void TestFunc(StringBuilder strMsg);
    //----------------------------------------------------------------------

    //----------------------------------------------------------------------
    // cpp Header
    //----------------------------------------------------------------------
    #pragma once
    #ifdef DLLEXPORT
    #define DLLTYPE extern "C" __declspec(dllexport)
    #else
    #define DLLTYPE extern "C" __declspec(dllimport)
    #endif

    DLLTYPE void TestFunc(const char* strMsg);
    //----------------------------------------------------------------------
   {% endraw %}
 {% endhighlight c++ %}

### 반환값을 배열로 받아와야 할 때
  소스 부분 생략.  
  출력 받아야 하는 값을 배열로 받아야 할 때.  
  C#에서 메모리를 생성해서 넘겨주고, C++에서는 그 메모리를 채워 넣는다.
  C코드에서 메모리의 크기를 알아야 할 경우 배열의 길이와 함께 넘겨준다.  
  TestFunc2의 경우는 C#에서 미리 입력된 값을 넘겨 결과를 받아야 할 때 In,Out을 붙여 입출력을 명시하여준다.

 {% highlight c++ %}
   {% raw %}
    //----------------------------------------------------------------------
    // C#
    //----------------------------------------------------------------------
    [DllImport(strDllName, CharSet = CharSet.Ansi, CallingConvention = CallingConvention.Cdecl)]
    public static extern void TestFunc(double input, int size, [Out]double[] output);

    [DllImport(strDllName, CharSet = CharSet.Ansi, CallingConvention = CallingConvention.Cdecl)]
    public static extern void TestFunc2(int size, [In, Out]double[] output);
    //----------------------------------------------------------------------

    //----------------------------------------------------------------------
    // cpp Header
    //----------------------------------------------------------------------
    #pragma once
    #ifdef DLLEXPORT
    #define DLLTYPE extern "C" __declspec(dllexport)
    #else
    #define DLLTYPE extern "C" __declspec(dllimport)
    #endif

    DLLTYPE void TestFunc(double input, int size, double* output);
    DLLTYPE void TestFunc2(int size, double* output);
    //----------------------------------------------------------------------
   {% endraw %}
 {% endhighlight c++ %}

 
### 구조체
  소스 부분 생략.  
  구조체를 넘겨야 할 경우, C++과 C#에 양쪽에 정의 하여 넘겨야한다. C#에서 IntPtr로 받아오는 방법과 직접 형식을 넘기는 경우가 있다.  
  C# 구조체와 C++ 구조체의 차이를 확인해야한다. C++의 boolean형은 1byte이지만 C#에서는 아닌것 같다. 그래서 byte로 받아오면 0,1로 값이 변환된다. 또한, 배열의 경우 ByValArray로 크기를 명시해주면 마샬링이 성공한다.  
  TestFunc2의 경우 결과를 구조체 형식으로 받고 싶을때 사용한다. 리턴 값으로 받을 수 있지만, out타입으로 받는게 조금더 안정적이라고 한다.

 {% highlight c++ %}
   {% raw %}
    //----------------------------------------------------------------------
    // C#
    //----------------------------------------------------------------------
    [Serializable]
    [StructLayout(LayoutKind.Sequential)]
    public struct ST_PARAM
    {
      byte val1;
      short val2;
      int val3;
      float val4;
      double val5;
      [MarshalAs(UnmanagedType.ByValArray, SizeConst = 128)]
      byte[] val2;
    }

    [DllImport(strDllName, CharSet = CharSet.Ansi, CallingConvention = CallingConvention.Cdecl)]
    public static extern void TestFunc(ST_PARAM param);
    public static extern void TestFunc2(out ST_PARAM param);
    //----------------------------------------------------------------------

    //----------------------------------------------------------------------
    // cpp Header
    //----------------------------------------------------------------------

    #pragma once
    #ifdef DLLEXPORT
    #define DLLTYPE extern "C" __declspec(dllexport)
    #else
    #define DLLTYPE extern "C" __declspec(dllimport)
    #endif

    struct ST_PARAM
    {
      bool val1;
      short val2;
      int val3;
      float val4;
      double val5;
      char val2[128];
    };

    DLLTYPE void TestFunc(ST_PARAM param);
    DLLTYPE void TestFunc2(ST_PARAM* param);
    //----------------------------------------------------------------------
   {% endraw %}
 {% endhighlight c++ %}