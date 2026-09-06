---
title: FalconEyes Structure
tags:
  - Image Processing
  - Tool
---
<!--more-->
## .net 8.0
- Abi
    - FalconEyes.Abi.cs : C++ DLL P-Invoke
    - FalconEyes.Extensions.cs : C++ DLL P-Invoke
    - FalconEyes.Filters.cs : C++ DLL P-Invoke
    - FalconEyes.ImageIO.cs : C++ DLL P-Invoke
    - FalconEyes.ImageOps.cs : C++ DLL P-Invoke
    - FalconEyes.Separator.cs : C++ DLL P-Invoke
    - FalconEyes.SafeFEImageHandle.cs : Native 객체 수명 주기 관리. (GC 수집)
- Service
    - AbiService : Abi 호출 서비스
    - ExtensionsService : Extensions 호출 서비스
    - FalconEyesService : 서비스 호출 Single-Tone 객체.
    - FiltersService : Filters 호출 서비스
    - ImageHelper : SafeFEImageHandle <-> WriteableBitmap 변환기.
    - ImageIOService : ImageIO 호출 서비스
    - ImageOpsService : ImageOps 호출 서비스
    - ParameterHelper : SafeNamedParameter <-> List 변환기.
    - SeparatorService : Separator 호출 서비스
- Document
    - IDocumentManager : Document Interface.
    - AvalonDockDocumentManager : AvalonDock 문서 관리.
    - ImageDocumentContext : 문서 관리 객체.
- Operation
    - ImageOperationRunner : Service 실행기. (비동기)
    - OperationRequest : Service 요청 객체.
- SubWindowManager
    - SubWindowManager : Sub-Window 관리자.

## FalconWpf.Contracts : FalconWpf 계약들.
- MVVMBase : PropertyChagned 인터페이스 구현, Command 구현 등.
- INIMangaer : INI 파일 입출력. (클래스 <-> 파일)
- XMLManager : XML 파일 입출력. (클래스 <-> 파일)
- Interfaces : 기타 인터페이스 포함.

## FalconWpf.Controls : FalconWpf 컨트롤들.
- UserParam : Title, Param, Unit 으로 이루어진 유저 컨트롤.
- UserSlider : Title, Slider, Value 로 이루어진 유저 컨트롤.
- UserButton : CornerRadius 설정 가능하고 Toggle기능이 포함된 유저 컨트롤.
- UserCombo : Title, Combo 로 이루어진 유저 컨트롤.

## FalconWpf.ImageViewer : 이미지 뷰어.
- ImageViewer : 기본 이미지 뷰.
    - Overlay : Overlay Draw 객체 (Thumb Base Overlay Control.)

## C++ dll modules
- FalconEyes.Abi : Assembly Binary Interface.
- FalconEyes.Common : 공통 기능. 이미지 정의, 인터페이스 자료 형등.
- FalconEyes.Extensions : 확장 기능 (cuda등 기타 라이브러리)
- FalconEyes.Filter : 이미지 필터 기능. (gaussian,canny,sobel,hough...)
- FalconEyes.ImageIO : 이미지 입출력 (open,save)
- FalconEyes.ImageOps : 이미지 동작. (create, roiimage, concat,gamma,flip...)
- FalconEyes.Separator : 이미지 분리 기능. (threshold,contour,sift,patternmatching...)
- Module.Logger : 로그 모듈.