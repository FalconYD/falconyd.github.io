---
title: NPP Core Function
tags:
  - Image Processing
  - CUDA
  - Palellel Processing
  - NPP
---

## Core Function
<!--more-->
 라이브러리 관리를 위한 기본 기능, 특히 라이브러리 버전 및 장치 속성 쿼리 가능.

### 함수
{% highlight c++ %}
    {% raw %}
    // NPP 라이브러리 버전.
    const NppLibraryVersion *nppGetLibVersion(void)

    // 활성 CUDA 디바이스의 SM수를 가져온다.
    int nppGetGpuNumSMs(void)

    // 활성 CUDA 디바이스에서 블록당 최대 스레드 수 를 가져온다.
    int nppGetMaxThreadsPerBlock(void)
    
    // 활성 GPU에 대한 SM당 최대 스레드 수를 가져온다.
    int nppGetMaxThreadsPerSM(void)

    // SM당 최대 스레드 수, 블록당 최대 스레드 수 및 활성 GPU에 대한 SM수를 가져온다.
    int nppGetGpuDeviceProperties(int *pMaxThreadsPerSM, int *pMaxThreadsPerBlock, int *pNumberOfSMs)

    // 활성 CUDA 장치의 이름을 가져온다.
    const char *nppGetGpuName(void)

    // NPP CUDA 스트림을 가져온다.
    cudaStream_t nppGetStream(void)

    // nppSetStream() 호출로 설정된 현재 NPP관리형 CUDA 스트림 컨텍스트를 가져온다.
    NppStatus nppGetStreamContext(NppStreamContext *pNppStreamContext)

    // 현재 NPP CUDA 스트림과 연결된 디바이스의 SM수를 가져온다.
    unsigned int nppGetStreamNumSMs(void)

    // 현재 NPP CUDA 스트림과 연결된 디바이스에서 SM당 최대 스레드 수를 가져온다.
    unsigned int nppGetStreamMaxThreadsPerSM(void)

    // NPP CUDA 스트림을 설정한다.
    NppStatus nppSetStream(cudaStream_t hStream)
  {% endraw %}
{% endhighlight c++ %}

 
 (https://docs.nvidia.com/cuda/npp/core_npp.html)
출처 - CUDA NPP