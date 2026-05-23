---
title: Halide Lang
tags:
  - Image Processing
  - Palellel Processing
  - Cuda
  - SIMD
  - C++
---
<!--more-->

## 이미지처리에서의 최적화
 이미지 처리에서 최적화문제는 항상 대두되어왔다.
 요즘은 상용 라이브러리 (MIL, Halcon, VisionPro, eVision 등)를 통해서 해결하기도 하고, OpenSource 진영의 대표인 OpenCV도 많은 최적화를 이루어 충분한 성능을 보여주고 있다.
 그러나 산업계에서는 기존 라이브러리에서 제공하는 라이브러리가 아닌 알고리즘을 직접 작성해야하는 경우가 생기는데 이때 Cuda, Simd, Palellel Processing등을 한다.
 이러한 방법들은 알고리즘 혹은 로직의 고속화 및 최적화를 달성 할 수 있지만, 문제는 개발 공수의 증가와 진입장벽의 문제가 있다.
 각 최적화 비법들을 익히는 학습곡선도 높고, 각 언어마다 익혀야할 방법이 다르다.

## Halide Lang
 Halide는 이러한 문제를 해결하기 위한 방법을 제시한다.
 알고리즘과 스케쥴링을 분리하여, 같은 알고리즘을 다른 최적화 기법(cuda,simd, 병렬화 등)으로 컴파일 할 수 있도록 돕는 언어이다.

### Algorithm
Halide는 API 호출 형태로 C++의 문법 사용한다.
그러나 일반 c++로 작성하는 알고리즘과는 다르게 병렬화에 대한 지식이 조금 필요해 보인다.
예를 들어 Tiled기법(SIMD)이나 병렬화 기법등을 알고 있어야 스케쥴링이 용이하다.
조건문도 TBB와 cuda와는 다르게 SIMD와 비슷한 방식으로 알고리즘을 작성해야한다.
SIMD같은 경우 조건문으로 분기를 타는게 아니라, 조건에 따른 결과값(삼항연산자와 비슷하게)을 연산하여 처리하는 방식을 사용한다.
Halide도 그와 비슷한 접근으로 조건문을 설정한다.
  
또한, 루프문 안에 boxfiltering과 같은 경우 Range를 사용하여 알고리즘을 작성해야한다.

### 다음은 동일 알고리즘을 각 기법으로 벤치마크한 데이터들이다.
 - CPU : 7800x3d
 - RAM : 32GB
 - GPU : RTX 5080
 - OS : Windows 11
 - Cuda ver : cuda 13.2
 - IDE : visual studio 2022
 - LLM : Antigravity IDE 2.0 Gemini 3.5(high)모델로 작성 및 테스트.

| 순번 | 테스트 플랫폼 & 스케줄링 상세 | 반복 횟수 (회) | 평균 연산 속도 (ms) | 평균 연산 속도 (us) | 가속비 (vs 직렬) | 정합성 검증 여부 |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: |
| **1** | **CPU + 기본 연산 (직렬 루프)** | 3 | **726.67 ms** | 726,674 us | $1.0\times$ (기준) | **Pass** |
| **2** | **CPU + SIMD 최적화 (16-way Vectorize)** | 100 | **93.91 ms** | 93,910.5 us | **약 7.74배** | **Pass** |
| **3** | **CPU + 병렬화 최적화 (Multi-core Parallel)** | 100 | **84.72 ms** | 84,718.5 us | **약 8.58배** | **Pass** |
| **4** | **CPU + SIMD + 병렬화 복합 최적화 (Vector + Parallel)** | 100 | **12.21 ms** | 12,207.7 us | **약 59.53배** | **Pass** |
| **5** | **Halide CUDA 최적화 (GPU Tiling & Block/Thread)** | 100 | **4.50 ms** | 4,498.5 us | **약 161.54배** | **Pass** |
| **6** | **Halide OpenCL 최적화 (GPU Tiling & Work-group)** | 100 | **4.47 ms** | 4,472.66 us | **약 162.47배** | **Pass** |
| **7** | **Halide Vulkan 최적화 (GPU 타일링)** | - | *Pass* | *지원 안 함* | *Pass* | *Pass* |
| **8** | **수작업 네이티브 CUDA 최적화 (Constant Memory 사용)** | 100 | **3.92 ms** | 3,919.14 us | **약 185.42배** | **Pass** |

### 다음은 Halide 홈페이지에서 발췌한 영상처리 예시이다.
{% highlight c++ %}
  {% raw %}   
  // Halide tutorial lesson 2: Processing images

// This lesson demonstrates how to pass in input images and manipulate
// them.

// On linux, you can compile and run it like so:
// g++ lesson_02*.cpp -g -I <path/to/Halide.h> -I <path/to/tools/halide_image_io.h> -L <path/to/libHalide.so> -lHalide `libpng-config --cflags --ldflags` -ljpeg -lpthread -ldl -o lesson_02 -std=c++17
// LD_LIBRARY_PATH=<path/to/libHalide.so> ./lesson_02

// On os x:
// g++ lesson_02*.cpp -g -I <path/to/Halide.h> -I <path/to/tools/halide_image_io.h> -L <path/to/libHalide.so> -lHalide `libpng-config --cflags --ldflags` -ljpeg -o lesson_02 -std=c++17
// DYLD_LIBRARY_PATH=<path/to/libHalide.dylib> ./lesson_02

// If you have the entire Halide source tree, you can also build it by
// running:
//    make tutorial_lesson_02_input_image
// in a shell with the current directory at the top of the halide
// source tree.

// The only Halide header file you need is Halide.h. It includes all of Halide.
#include "Halide.h"

// Include some support code for loading pngs.
#include "halide_image_io.h"
using namespace Halide::Tools;

int main(int argc, char **argv) {

    // This program defines a single-stage imaging pipeline that
    // brightens an image.

    // First we'll load the input image we wish to brighten.
    Halide::Buffer<uint8_t> input = load_image("images/rgb.png");

    // See below for a smaller version.

     

    // Next we define our Func object that represents our one pipeline
    // stage.
    Halide::Func brighter;

    // Our Func will have three arguments, representing the position
    // in the image and the color channel. Halide treats color
    // channels as an extra dimension of the image.
    Halide::Var x, y, c;

    // Normally we'd probably write the whole function definition on
    // one line. Here we'll break it apart so we can explain what
    // we're doing at every step.

    // For each pixel of the input image.
    Halide::Expr value = input(x, y, c);

    // Cast it to a floating point value.
    value = Halide::cast<float>(value);

    // Multiply it by 1.5 to brighten it. Halide represents real
    // numbers as floats, not doubles, so we stick an 'f' on the end
    // of our constant.
    value = value * 1.5f;

    // Clamp it to be less than 255, so we don't get overflow when we
    // cast it back to an 8-bit unsigned int.
    value = Halide::min(value, 255.0f);

    // Cast it back to an 8-bit unsigned integer.
    value = Halide::cast<uint8_t>(value);

    // Define the function.
    brighter(x, y, c) = value;

    // The equivalent one-liner to all of the above is:
    //
    // brighter(x, y, c) = Halide::cast<uint8_t>(min(input(x, y, c) * 1.5f, 255));
    //
    // In the shorter version:
    // - I skipped the cast to float, because multiplying by 1.5f does
    //   that automatically.
    // - I also used an integer constant as the second argument in the
    //   call to min, because it gets cast to float to be compatible
    //   with the first argument.
    // - I left the Halide:: off the call to min. It's unnecessary due
    //   to Koenig lookup.

    // Remember, all we've done so far is build a representation of a
    // Halide program in memory. We haven't actually processed any
    // pixels yet. We haven't even compiled that Halide program yet.

    // So now we'll realize the Func. The size of the output image
    // should match the size of the input image. If we just wanted to
    // brighten a portion of the input image we could request a
    // smaller size. If we request a larger size Halide will throw an
    // error at runtime telling us we're trying to read out of bounds
    // on the input image.
    Halide::Buffer<uint8_t> output =
        brighter.realize({input.width(), input.height(), input.channels()});

    // Save the output for inspection. It should look like a bright parrot.
    save_image(output, "brighter.png");

    // See below for a small version of the output.

     

    printf("Success!\n");
    return 0;
}
  {% endraw %}
{% endhighlight c++ %}  
  [참고-Halide 사이트](https://halide-lang.org/) 