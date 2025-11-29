---
title: Visual Studio Version Management
tags:
  - C#
  - C++
  - Version
---

## Version Management
 근래에 필자의 솔루션은 WPF + Cpp DLL 형식으로 작업을 한다. 이러는 와중 프로그램 실행 전 DLL과 프로그램의 버전을 관리하기 위한 방법을 고민하여 정리한다.
 
<!--more-->
## 버전 정의
 - 버전은 혼자 관리하는 목적이기에 내가 보기 편한형태로 관리한다. (연월일_시분)
 - 버전은 빌드시간으로 관리하고, 빌드 할 떄 마다 자동 업데이트한다.
 - 버전은 파일-속성-자세히에서 확인이 가능하다.
 
## 자동 업데이트  
  자동 업데이트는 빌드 전에 C#의 경우는 AssemblyInfo.cs를 업데이트, C++의 경우 Version rc 파일을 파워쉘 스크립트로 업데이트 하여 빌드 때 마다 업데이트를 한다. Git을 사용하는 경우 ignore 목록에 해당 파일을 추가할 수 있다.

## 버전 정보 읽어오기
### C#
 - exe 어플리케이션의 경우, 'string GetAppProductVersion()'를 호출한다.
 - dll (cpp)의 버전을 확인할 경우 'string GetProductVersion(string dllPath)'를 호출한다.
  {% highlight c# %}
    {% raw %}
    internal class VersionManager
    {
        public static string GetAppProductVersion()
        {
            // 현재 실행 중인 어셈블리 객체를 가져옵니다.
            Assembly assembly = Assembly.GetExecutingAssembly();
    
            // 1. AssemblyInformationalVersionAttribute를 사용하여 '제품 버전'을 가져옵니다.
            // 이 속성은 보통 "1.0.0.0-beta" 또는 "ver.20251127_1508H"와 같이 상세한 문자열을 포함합니다.
            var informationalVersionAttribute = assembly.GetCustomAttribute<AssemblyInformationalVersionAttribute>();
    
            if (informationalVersionAttribute != null)
            {
                return informationalVersionAttribute.InformationalVersion;
            }
    
            // 2. InformationalVersion이 설정되지 않았다면, 기본 AssemblyVersion을 사용합니다.
            // 이는 Major.Minor.Build.Revision 형태입니다.
            return assembly.GetName().Version.ToString();
        }
        public static DateTime GetAppBuildDate()
        {
            try
            {
                // 1. 현재 실행 중인 어셈블리의 경로를 가져옵니다.
                string assemblyPath = Assembly.GetExecutingAssembly().Location;
    
                // 2. 파일 정보 객체를 사용하여 파일의 최종 쓰기 시간(LastWriteTime)을 가져옵니다.
                // 이 시간은 Visual Studio가 빌드를 완료하고 파일을 저장한 시간입니다.
                DateTime buildDate = File.GetLastWriteTime(assemblyPath);
    
                return buildDate;
            }
            catch (Exception)
            {
                // 오류 발생 시 현재 시간 반환 또는 기본값 설정
                return DateTime.Now;
            }
        }
    
        public static string GetProductVersion(string dllPath)
        {
            string strver = "";
            if (!File.Exists(dllPath))
            {
                Console.WriteLine($"오류: 파일 경로를 찾을 수 없습니다: {dllPath}");
                return strver;
            }
    
            try
            {
                // 1. FileVersionInfo 객체를 생성하여 파일 정보 로드
                FileVersionInfo versionInfo = FileVersionInfo.GetVersionInfo(dllPath);
    
                // 2. 원하는 속성 가져오기
                string productVersion = versionInfo.ProductVersion;
                string fileVersion = versionInfo.FileVersion;
                string companyName = versionInfo.CompanyName;
                string legalCopyright = versionInfo.LegalCopyright;
    
                Console.WriteLine($"--- DLL 정보: {Path.GetFileName(dllPath)} ---");
                Console.WriteLine($"제품 버전 (Product Version): {productVersion}");
                Console.WriteLine($"파일 버전 (File Version): {fileVersion}");
                Console.WriteLine($"회사명: {companyName}");
                Console.WriteLine($"저작권: {legalCopyright}");
                Console.WriteLine("--------------------------------------");
    
                strver = productVersion;
            }
            catch (Exception ex)
            {
                Console.WriteLine($"버전 정보를 읽는 중 오류 발생: {ex.Message}");
            }
    
            return strver;
        }
    }
    {% endraw %}
  {% endhighlight c# %}
### C++
 - 호출 방법은 다음과 같다.  
  {% highlight c++ %}
    {% raw %}
      wchar_t path[MAX_PATH];
      GetModuleFileNameW(NULL, path, MAX_PATH);
      //GetModuleFileNameW(g_Module, path, MAX_PATH); // dll 버전
      auto productver = vm_GetFileVersion(path);
      auto builddate = vm_GetBuildDate(path);
    {% endraw %}
  {% endhighlight c++ %}
  - dll의 경우 'DllMain'인자에 있는 'HModule' 을 이용하여 버전 정보를 받아올 수 있다.

  {% highlight c++ %}
    {% raw %}
    #ifndef VERSION_MANAGER_H
    #define VERSION_MANAGER_H
    
    #include <iostream>
    #include <string>
    #include <windows.h>
    #include <VersionHelpers.h> // IsWindowsVistaOrGreater 등 도움 함수 포함
    #include <shlwapi.h>      // PathFileExists 등 도움 함수 포함
    #pragma comment(lib, "Version.lib") // Version.lib 링크 지시
    
    static std::wstring vm_GetFileVersion(const std::wstring& filePath)
    {
      DWORD handle = 0;
      DWORD size = GetFileVersionInfoSizeW(filePath.c_str(), &handle);
    
      if (size == 0)
      {
        // 파일이 없거나 버전 정보가 없는 경우
        return L"버전 정보 없음";
      }
    
      // 1. 버전 정보를 저장할 버퍼 할당
      BYTE* versionInfo = new BYTE[size];
    
      // 2. 파일에서 버전 정보를 버퍼로 읽어옴
      if (!GetFileVersionInfoW(filePath.c_str(), handle, size, versionInfo))
      {
        delete[] versionInfo;
        return L"정보 읽기 실패";
      }
    
      // 3. 'Translation' 정보를 가져옴 (언어 코드와 코드 페이지)
      // 이 정보는 버전 문자열 리소스가 저장된 위치를 찾는 데 사용됩니다.
      struct LANGANDCODEPAGE {
        WORD wLanguage;
        WORD wCodePage;
      } *lpTranslate;
    
      UINT cbTranslate;
    
      // 이 문자열을 사용하여 버전 정보 내부의 언어 및 코드 페이지 정보를 얻습니다.
      if (!VerQueryValueW(versionInfo, L"\\VarFileInfo\\Translation", (LPVOID*)&lpTranslate, &cbTranslate))
      {
        // 기본값 사용 또는 오류 처리
        lpTranslate = new LANGANDCODEPAGE[1];
        lpTranslate[0].wLanguage = 0x0409; // English (미국)
        lpTranslate[0].wCodePage = 1200;  // Unicode (UTF-16)
      }
    
      // 4. 원하는 버전 정보 항목을 쿼리 (예: ProductVersion)
      // "ProductVersion" 항목은 C#의 AssemblyInformationalVersion에 해당합니다.
      wchar_t subBlock[50];
      // 언어/코드 페이지를 사용하여 검색 경로 문자열 생성
      swprintf_s(subBlock, L"\\StringFileInfo\\%04x%04x\\ProductVersion", lpTranslate[0].wLanguage, lpTranslate[0].wCodePage);
    
      LPWSTR lpBuffer = NULL;
      UINT dwBytes = 0;
      std::wstring versionString;
    
      // 5. ProductVersion 값 가져오기
      if (VerQueryValueW(versionInfo, subBlock, (LPVOID*)&lpBuffer, &dwBytes) && dwBytes > 0)
      {
        versionString = lpBuffer;
      }
      else
      {
        versionString = L"ProductVersion 항목 없음";
      }
    
      // 6. 메모리 해제
      delete[] versionInfo;
    
      return versionString;
    }
    
    static std::string vm_GetBuildDate(const std::wstring& filePath)
    {
      WIN32_FILE_ATTRIBUTE_DATA fileData;
    
      if (!GetFileAttributesExW(filePath.c_str(), GetFileExInfoStandard, &fileData))
      {
        return "파일 접근 오류";
      }
    
      // FILETIME 구조체를 SYSTEMTIME 구조체로 변환
      SYSTEMTIME sysTime;
      FileTimeToSystemTime(&fileData.ftLastWriteTime, &sysTime);
    
      // SYSTEMTIME 구조체를 문자열로 포맷팅
      char dateBuffer[60];
      sprintf_s(dateBuffer, "%04d-%02d-%02d %02d:%02d:%02d",
        sysTime.wYear, sysTime.wMonth, sysTime.wDay,
        sysTime.wHour, sysTime.wMinute, sysTime.wSecond);
    
      return std::string(dateBuffer);
    }
    
    #endif // VERSION_MANAGER_H
    {% endraw %}
  {% endhighlight c++ %}
## PowerShell 스크립트
### C#
  스크립트에 보이듯이 
  - '프로젝트 속성' - '빌드 전 이벤트'에 다음을 추가한다.  
  {% highlight c++ %}
    {% raw %}
    powershell -ExecutionPolicy Bypass -File "$(SolutionDir)UpdateVersion_CS.ps1" "$(ProjectDir)"
    {% endraw %}
  {% endhighlight c++ %}
 
  - 파일명은 'UpdateVersion_CS.ps1'로 저장을 한다.  
  {% highlight c++ %}
    {% raw %}
      # 스크립트 시작 부분
      $companyName = "TAEROO.Co.Ltd"
      try {
      
        # =======================================================
        # 1. 인자 처리 및 경로 설정
        # =======================================================
        # 전달받은 인수에서 불필요한 따옴표 및 공백을 제거하여 경로 오류를 방지
        $projectPath = $args[0].Trim('"').Trim()
        $filePath = Join-Path $projectPath "Properties\AssemblyInfo.cs"
      
        Write-Host "Resolved AssemblyInfo Path: $filePath"
      
        # =======================================================
        # 2. 새로운 버전 정보 생성
        # =======================================================
        # 현재 날짜 및 시간 형식 지정 (예: 20251127.1216)
        $date = Get-Date -Format "yyyyMMdd_HHmm"
          $newVersionString = "ver.$date"+"H"
        
        # 대체 또는 삽입할 새로운 [assembly: ...] 라인
        # (PowerShell에서 내부 따옴표를 위해 grave accent(`) 사용)
        $replacementLine = "[assembly: AssemblyInformationalVersion(`"$newVersionString`")]"
        $replacementLine2 = "[assembly: AssemblyCopyright(`"$companyName`")]"
        
        # 기존 InformationalVersion 라인을 찾기 위한 정규식 (강화)
          # (?m) 멀티라인 모드 활성화. \s*는 공백(줄 시작/끝 공백 포함)을 허용합니다.
          # .*?는 비탐욕적(non-greedy) 매칭으로, 속성 내용이 어떤 형태든 정확히 찾습니다.
          # 이 패턴은 [assembly: AssemblyInformationalVersion("...")] 전체를 확실히 찾습니다.
          $regexPattern = '(?m)^\s*\[assembly\s*:\s*AssemblyInformationalVersion\s*\(".*?"\)\]\s*$'
          $regexPattern2 = '(?m)^\s*\[assembly\s*:\s*AssemblyCopyright\s*\(".*?"\)\]\s*$'
      
        # =======================================================
        # 3. 파일 내용 읽기 및 처리
        # =======================================================
        # 파일 내용 읽기 (UTF8로 읽는 것은 유지)
        # 기존 파일이 다른 인코딩이어도 UTF8로 읽는 것이 대부분 안전합니다.
        $content = Get-Content $filePath -Raw -Encoding UTF8 
      
        # 3-1. 기존 라인이 있는지 확인 (대체 모드)
        if ($content -match $regexPattern) {
              Write-Host "기존 AssemblyInformationalVersion 라인을 찾았습니다. 새로운 정보로 대체합니다."
              # 정규식을 사용하여 해당 줄 전체를 대체
              $newContent = $content -replace $regexPattern, $replacementLine
          }
          # 3-2. 기존 라인이 없으면 삽입 (삽입 모드)
          else {
              Write-Host "AssemblyInformationalVersion 라인이 없어 삽입합니다."
              
              # 마지막 [assembly: ...] 라인을 찾기 위한 정규식
              $lastAssemblyLinePattern = '(?m)^\[assembly: .*\]$'
              
              # 마지막 매치된 [assembly: ...] 라인 찾기
              $lastMatch = $content | Select-String $lastAssemblyLinePattern -AllMatches
              $lastAssemblyLine = $lastMatch.Matches | Select-Object -Last 1
      
              if ($lastAssemblyLine) {
                  # 마지막 [assembly: ...] 라인 바로 뒤에 삽입
                  $insertionPoint = $lastAssemblyLine.Value
                  # 줄바꿈(\r\n)과 함께 새로운 라인을 삽입합니다.
                  # $insertionPoint에 포함된 특수문자 처리를 위해 [regex]::Escape를 사용합니다.
                  $newContent = $content -replace [regex]::Escape($insertionPoint), "$insertionPoint`r`n$replacementLine"
                  Write-Host "마지막 [assembly: ...] 줄 뒤에 성공적으로 삽입했습니다."
              } else {
                  # [assembly: ...] 라인이 아예 없으면 파일 끝에 추가
                  Write-Host "경고: 다른 [assembly: ...] 속성도 찾을 수 없어 파일 끝에 추가합니다."
                  $newContent = $content + "`r`n" + $replacementLine
              }
          }
      
        # 3-1. 기존 라인이 있는지 확인 (대체 모드)
        if ($newContent -match $regexPattern2) {
              Write-Host "기존 AssemblyCopyright 라인을 찾았습니다. 새로운 정보로 대체합니다."
              # 정규식을 사용하여 해당 줄 전체를 대체
              $newContent = $newContent -replace $regexPattern2, $replacementLine2
          }
          # 3-2. 기존 라인이 없으면 삽입 (삽입 모드)
          else {
              Write-Host "AssemblyCopyright 라인이 없어 삽입합니다."
              
              # 마지막 [assembly: ...] 라인을 찾기 위한 정규식
              $lastAssemblyLinePattern = '(?m)^\[assembly: .*\]$'
              
              # 마지막 매치된 [assembly: ...] 라인 찾기
              $lastMatch = $newContent | Select-String $lastAssemblyLinePattern -AllMatches
              $lastAssemblyLine = $lastMatch.Matches | Select-Object -Last 1
      
              if ($lastAssemblyLine) {
                  # 마지막 [assembly: ...] 라인 바로 뒤에 삽입
                  $insertionPoint = $lastAssemblyLine.Value
                  # 줄바꿈(\r\n)과 함께 새로운 라인을 삽입합니다.
                  # $insertionPoint에 포함된 특수문자 처리를 위해 [regex]::Escape를 사용합니다.
                  $newContent = $newContent -replace [regex]::Escape($insertionPoint), "$insertionPoint`r`n$replacementLine2"
                  Write-Host "마지막 [assembly: ...] 줄 뒤에 성공적으로 삽입했습니다."
              } else {
                  # [assembly: ...] 라인이 아예 없으면 파일 끝에 추가
                  Write-Host "경고: 다른 [assembly: ...] 속성도 찾을 수 없어 파일 끝에 추가합니다."
                  $newContent = $newContent + "`r`n" + $replacementLine2
              }
          }
        # =======================================================
        # 4. 파일 내용 저장 및 종료
        # =======================================================
        # ********** .NET 메서드를 사용하여 BOM이 있는 UTF-8로 저장 **********
          # C# 소스 파일에서 한글 깨짐을 방지하는 가장 확실한 방법입니다.
        $newContent = $newContent -replace "`r?`n", "`r`n"
          $Utf8WithBOM = New-Object System.Text.UTF8Encoding($false) # $true는 BOM 사용을 의미
        [System.IO.File]::WriteAllText($filePath, $newContent, $Utf8WithBOM)
      
        Write-Host "AssemblyInformationalVersion successfully set to $newVersionString"
        exit 0
      }
      catch {
       # 오류 발생 시
       Write-Error "버전 업데이트 스크립트 실행 중 오류 발생: $($_.Exception.Message)"
          Write-Host "Failed to process path: '$filePath'" 
      exit 1
      }
    
    {% endraw %}
  {% endhighlight c++ %}
 
### C++
  - 스크립트에 보이듯이 '프로젝트 속성' - '빌드 이벤트' - '빌드 전 이벤트'에 다음을 추가한다.  
  {% highlight c++ %}
    {% raw %}
    powershell -ExecutionPolicy Bypass -File "$(SolutionDir)UpdateVersion_CPP.ps1" "$(ProjectDir)"
    {% endraw %}
  {% endhighlight c++ %}

  - 프로젝트 목록에서 '추가 - 리소스 - 버전'을 추가한다.
 
  - 파일명은 'UpdateVersion_CPP.ps1'로 저장을 한다.  
  {% highlight c++ %}
    {% raw %}
        # C++ 프로젝트의 .rc 리소스 파일을 업데이트하는 스크립트입니다.
        
        # 핵심 변수 초기화: 예외 발생 시 변수가 null이 되는 것을 방지
        $rcFileName = ""
        $filePath = ""
        $content = "" # Get-Content 이전에도 변수를 초기화합니다.
        $companyName = "TAEROO.Co.Ltd"
        try {
        
          # =======================================================
          # 1. 인자 처리 및 경로 설정
          # =======================================================
            
            # 디버깅: 스크립트가 받은 원시 인자 출력 (VS 명령줄 디버깅에 필수)
            Write-Host "--- Debug: Raw Arguments Received ---"
            for ($i = 0; $i -lt $args.Count; $i++) {
                Write-Host "Arg[$i]: '$($args[$i])'"
            }
            Write-Host "-------------------------------------"
        
            # 인자 1: 프로젝트 경로
            if ($args.Count -lt 1) {
                Write-Error "오류: 프로젝트 경로 인자가 누락되었습니다."
                exit 1
            }
        
            # ********** 수정: 강력한 경로 정리 (VS 빌드 이벤트 인자 전달 오류 방어) **********
            try {
                # 1. 전달받은 인자에서 모든 따옴표를 제거하고 공백으로 분리합니다.
                # 공백으로 분리하여 첫 번째 토큰만 유효한 경로로 사용합니다.
                $rawPathInput = $args[0].Trim('"').Trim().Replace('"', '')
                $pathTokens = $rawPathInput -split '\s+'
                $cleanedPath = $pathTokens[0]
                
                # 2. .NET의 GetFullPath 메서드를 사용하여 경로를 표준화
                # GetFullPath는 상대 경로를 절대 경로로 바꾸고 경로 구분자를 표준화합니다.
                $projectPath = [System.IO.Path]::GetFullPath($cleanedPath)
                
                # 경로 끝의 백슬래시를 제거하여 Join-Path의 안정성을 높임
                $projectPath = $projectPath.TrimEnd('\')
                
            } catch {
                # 경로 표준화 과정에서 여전히 실패할 경우
                Write-Error "오류: 프로젝트 경로 인자 처리 및 표준화 실패. 전달된 인자: '$($args[0])'. 실패 원인: $($_.Exception.Message)"
                exit 1
            }
            
            # 인자 2: 프로젝트 이름 (새로 추가됨) - 인자 개수를 확인하여 안전하게 접근
            # 두 번째 인자도 공백을 기준으로 분리하여 첫 번째 토큰만 사용하도록 안전하게 처리
            $projectName = if ($args.Count -ge 2) { 
                $nameTokens = $args[1].Trim('"').Trim().Replace('"', '') -split '\s+'
                $nameTokens[0]
            } else { 
                "" 
            }
            
            # -------------------------------------------------------
            # RC 파일 이름 결정 로직 (프로젝트 이름을 우선 사용)
            # -------------------------------------------------------
            
            # $projectPath가 유효한지 확인합니다.
            if (-not (Test-Path $projectPath -PathType Container)) {
                Write-Error "오류: 확인된 프로젝트 경로 '$projectPath'가 존재하지 않거나 접근할 수 없습니다."
                exit 1
            }
            
            if (-not [string]::IsNullOrEmpty($projectName)) {
                # 1. 프로젝트 이름을 사용하여 RC 파일 이름 생성 (예: MyApp.rc)
                $rcFileName = "$projectName.rc"
                $filePath = Join-Path $projectPath $rcFileName
                
                # 해당 파일이 실제로 존재하는지 확인
                if (-not (Test-Path $filePath)) {
                    Write-Host "경고: 프로젝트 이름 기반 파일 '$rcFileName'을 찾을 수 없습니다. 디렉토리 검색으로 전환합니다."
                    
                    # Null 조건부 연산자(?.Name) 대신 PS 5.1 호환 방식으로 수정
                    $rcFileObject = Get-ChildItem -Path $projectPath -Filter "*.rc" | Select-Object -First 1
                    if ($rcFileObject) {
                        $rcFileName = $rcFileObject.Name
                    } else {
                        $rcFileName = $null
                    }
                }
            } else {
                # 프로젝트 이름이 인자로 전달되지 않은 경우 (기존 방식)
                
                # Null 조건부 연산자(?.Name) 대신 PS 5.1 호환 방식으로 수정
                $rcFileObject = Get-ChildItem -Path $projectPath -Filter "*.rc" | Select-Object -First 1
                if ($rcFileObject) {
                    $rcFileName = $rcFileObject.Name
                } else {
                    $rcFileName = $null
                }
            }
            
            if (-not $rcFileName) {
                Write-Error "프로젝트 경로 '$projectPath'에서 *.rc 파일을 찾을 수 없습니다. 파일 이름을 확인하세요."
                exit 1
            }
            
            # 최종 파일 경로 설정 (RC 파일 이름이 디렉토리 검색으로 변경되었을 경우)
            # 이 시점에는 $rcFileName이 반드시 유효한 값을 가집니다.
            $filePath = Join-Path $projectPath $rcFileName
        
            # ********** 수정: 프로젝트 이름 폴백 로직 추가 (ProjectName이 전달되지 않은 경우) **********
            if ([string]::IsNullOrEmpty($projectName) -and -not [string]::IsNullOrEmpty($rcFileName)) {
                # RC 파일 이름에서 확장자를 제거한 부분을 프로젝트 이름으로 사용합니다.
                # 예: "LPACore.rc" -> "LPACore"
                $projectName = [System.IO.Path]::GetFileNameWithoutExtension($rcFileName)
                Write-Host "경고: 프로젝트 이름 인자가 비어 있어 RC 파일 이름 '$rcFileName'에서 '$projectName'을 추출하여 사용합니다."
            }
        
          Write-Host "Resolved Resource File Path: $filePath"
        
          # =======================================================
          # 2. 새로운 버전 정보 생성
          # =======================================================
          # 날짜/시간 구성 요소 추출
          $year = Get-Date -Format "yyyy"
          $month = Get-Date -Format "MM"
          $day = Get-Date -Format "dd"
          $minute = Get-Date -Format "mm"
            
            # 숫자 버전: YYYY, MM, DD, MM (편의상 분을 네 번째 숫자로 사용)
            $fileVersionNum = "$year,$month,$day,$minute"
            
          # 문자열 버전: ver.YYYYMMDD.HHmmH 형식
          $date = Get-Date -Format "yyyyMMdd_HHmm"
            $newVersionString = "ver.$date" + "H"
          
          # =======================================================
          # 3. 파일 내용 읽기 및 처리
          # =======================================================
          # 인코딩을 Default에서 UTF8로 변경하여 안정성을 높임
          $content = Get-Content $filePath -Raw -Encoding UTF8 
        
            # -------------------------------------------------------
            # 3-1. 숫자 버전 필드 업데이트 (PRODUCTVERSION만 업데이트)
            # -------------------------------------------------------
            Write-Host "리소스 파일의 제품 버전 필드 (PRODUCTVERSION)를 업데이트합니다."
            
            # FILEVERSION 대체 로직은 제거 (파일 버전 고정 유지)
            # $fileVersionRegex = 'FILEVERSION\s+\d+,\s*\d+,\s*\d+,\s*\d+'
            # $content = $content -replace $fileVersionRegex, "FILEVERSION $fileVersionNum"
        
            # PRODUCTVERSION 대체
            $productVersionRegex = 'PRODUCTVERSION\s+\d+,\s*\d+,\s*\d+,\s*\d+'
            $content = $content -replace $productVersionRegex, "PRODUCTVERSION $fileVersionNum"
            
            # -------------------------------------------------------
            # 3-2. 문자열 버전 필드 업데이트 (SpecialBuild, ProductVersion, ProductName, CompanyName)
            # -------------------------------------------------------
            Write-Host "리소스 파일의 문자열 필드 (SpecialBuild, ProductVersion, ProductName, CompanyName)를 업데이트합니다."
            
            # 1. VALUE "SpecialBuild" 업데이트
            $stringSpecialBuildRegex = 'VALUE\s+"SpecialBuild",\s*".*?"'
            $replacementLine = "VALUE `"SpecialBuild`", `"$newVersionString`"" # RC 파일 구문에 맞게 따옴표 이스케이프 처리
            if ($content -match $stringSpecialBuildRegex) {
                $content = $content -replace $stringSpecialBuildRegex, $replacementLine
            } else {
                Write-Host "경고: SpecialBuild 필드를 찾을 수 없어 업데이트를 건너뜀. (RC 파일에 해당 필드가 없을 수 있음)"
            }
            
            # 2. VALUE "ProductVersion" 업데이트
            $stringProductVersionRegex = 'VALUE\s+"ProductVersion",\s*".*?"'
            $replacementLine = "VALUE `"ProductVersion`", `"$newVersionString`"" 
        
            if ($content -match $stringProductVersionRegex) {
                $content = $content -replace $stringProductVersionRegex, $replacementLine
            } else {
                # ProductVersion 필드가 없으면 FileVersion 필드를 차선책으로 업데이트
                Write-Host "경고: ProductVersion 필드를 찾을 수 없어 FileVersion 필드를 차선책으로 업데이트합니다."
                $stringFileVersionRegex = 'VALUE\s+"FileVersion",\s*".*?"'
                $replacementLine = "VALUE `"FileVersion`", `"$newVersionString`"" 
                
                if ($content -match $stringFileVersionRegex) {
                    $content = $content -replace $stringFileVersionRegex, $replacementLine
                } else {
                    Write-Host "경고: FileVersion 필드도 찾을 수 없어 문자열 버전 업데이트를 건너뜁니다."
                }
            }
            
            # 3. VALUE "ProductName" 업데이트
            if (-not [string]::IsNullOrEmpty($projectName)) {
                # 정규 표현식을 좀 더 관대하게 수정: 값 주변에 공백이 있어도 인식되도록 수정
                $stringProductNameRegex = 'VALUE\s+"ProductName"\s*,\s*".*?"'
                $replacementLine = "VALUE `"ProductName`", `"$projectName`"" 
        
                if ($content -match $stringProductNameRegex) {
                    $content = $content -replace $stringProductNameRegex, $replacementLine
                    Write-Host "ProductName 필드를 '$projectName'으로 업데이트했습니다."
                } else {
                    Write-Host "경고: ProductName 필드를 찾을 수 없어 업데이트를 건너뜀. RC 파일 내에 VALUE `"ProductName`", `"이전 이름`" 형식으로 존재하는지 확인하세요."
                }
                
                # 4. VALUE "CompanyName" 업데이트 (ProductName과 동일하게 업데이트)
                $stringCompanyNameRegex = 'VALUE\s+"CompanyName"\s*,\s*".*?"'
                $replacementLine = "VALUE `"CompanyName`", `"$companyName`"" 
                
                if ($content -match $stringCompanyNameRegex) {
                    $content = $content -replace $stringCompanyNameRegex, $replacementLine
                    Write-Host "CompanyName 필드를 '$projectName'으로 업데이트했습니다."
                } else {
                    Write-Host "경고: CompanyName 필드를 찾을 수 없어 업데이트를 건너_ㅁ."
                }
            }
        
        
          # =======================================================
          # 4. 파일 내용 저장 및 종료
          # =======================================================
          # C++ 소스 파일은 BOM이 있는 UTF-8이 가장 안전합니다.
            $Utf8WithBOM = New-Object System.Text.UTF8Encoding($true) # $true는 BOM 사용을 의미
            [System.IO.File]::WriteAllText($filePath, $content, $Utf8WithBOM)
        
          Write-Host "C++ Resource file '$rcFileName' successfully updated PRODUCTVERSION to $fileVersionNum ($newVersionString) and ProductName/CompanyName to '$projectName'"
          exit 0
        }
        catch {
            # 오류 발생 시: 문자열 연결(+)을 사용하여 파서 오류 가능성을 줄임
            $errorMessage = "C++ 버전 업데이트 스크립트 실행 중 오류 발생: " + $_.Exception.Message
            Write-Error $errorMessage
            # $filePath가 초기화되었기 때문에 이제는 빈 문자열 대신 올바른 값을 출력하거나, 오류 원인을 추적하는 데 도움이 됩니다.
            Write-Host "Failed to process path: '$filePath'" -ForegroundColor Red 
            exit 1 
      }
    
    {% endraw %}
  {% endhighlight c++ %}