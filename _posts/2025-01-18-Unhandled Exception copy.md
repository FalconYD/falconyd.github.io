---
title: Visual Studio Unhandled Exception
tags:
  - Debuging
  - C#
  - C++
---
## Unhandled Exception
<!--more-->
 프로그램을 작성하다 보면, 개발자가 놓친부분으로 인한 강제종료 되는 경우가 있을 수 있다.
 만약, 배포 후 문제가 발생한다면 로그를 분석하여 찾거나 메모리 덤프를 해볼 수 있다.

## C++ Memory Dump
 배포한 프로그램이 알 수 없는 문제로 중지 되는 상황이라고 한다면, 개발자는 미리 남겨놓았던 로그를 분석하여 정상 동작 여부, 혹은 중지된 시점을 유추 할 수 있다. 하지만 그것으로도 해결이 안되는 상황에는 메모리 덤프를 남겨 디버깅을 시도해 볼수 있다.
 메모리 덤프가 수행 되었다면 '.dmp'파일이 생성 되는데 이 파일을 Visual Studio로 열어 디버깅을 진행 할 수 있다.

  {% highlight c++ %}
    {% raw %}
    #include <DbgHelp.h>
    #pragma comment (lib, "DbgHelp")

    LONG CALLBACK TopLevelExceptionFilterCallBack(EXCEPTION_POINTERS* exceptionInfo)
    {
      MINIDUMP_EXCEPTION_INFORMATION dmpInfo = { 0 };
      dmpInfo.ThreadId = ::GetCurrentThreadId(); // Thread ID
      dmpInfo.ExceptionPointers = exceptionInfo; // Exception Info
      dmpInfo.ClientPointers = FALSE;
      SYSTEMTIME CurrentTime;
      GetLocalTime(&CurrentTime);

      std::stringstream Path;//저장 경로
      Path << ".\\DumpFile_" << CurrentTime.wYear << std::setfill('0') << std::setw(2) << CurrentTime.wMonth << std::setfill('0') << std::setw(2) << CurrentTime.wDay
        << "_" << std::setfill('0') << std::setw(2) << CurrentTime.wHour << std::setfill('0') << std::setw(2) << CurrentTime.wMinute << std::setfill('0') << std::setw(2) << CurrentTime.wSecond << ".dmp";


      HANDLE hFile = CreateFile(Path.str().c_str(), GENERIC_WRITE, FILE_SHARE_WRITE, NULL, CREATE_ALWAYS, FILE_ATTRIBUTE_NORMAL, NULL); // 덤프 생성
      //BOOL bWrite = ::MiniDumpWriteDump(::GetCurrentProcess(), ::GetCurrentProcessId(), hFile, MINIDUMP_TYPE::MiniDumpNormal, &dmpInfo, NULL, NULL);
      BOOL bWrite = ::MiniDumpWriteDump(::GetCurrentProcess(), ::GetCurrentProcessId(), hFile, MINIDUMP_TYPE::MiniDumpWithFullMemory, &dmpInfo, NULL, NULL);
      return 0L;
    }

    int main()
    {
      SetUnhandledExceptionFilter(TopLevelExceptionFilterCallBack); // dump callback 등록.
    }
    {% endraw %}
  {% endhighlight c++ %}
   
  위와 같은 코드로 덤프파일을 생성 할 수 있다.

## C# Unhandled Exception
  C#에서는 처리하지 않은 예외가 발생했을 경우 다음 코드와 같이 예외를 캐치할 수 있다.
  해당 부분에 로그를 작성하여 예외를 추적 할 수 있다.
  {% highlight c# %}
    {% raw %}
    public partial class App : Application
    {
        public App()
        {
            this.Dispatcher.UnhandledException +=
          new DispatcherUnhandledExceptionEventHandler(Dispatcher_UnhandledException);

            this.Dispatcher.UnhandledExceptionFilter +=
              new DispatcherUnhandledExceptionFilterEventHandler(Dispatcher_UnhandledExceptionFilter);
        }

        void Dispatcher_UnhandledExceptionFilter(object sender, DispatcherUnhandledExceptionFilterEventArgs e)
        {
            e.RequestCatch = true;
        }

        void Dispatcher_UnhandledException(object sender, DispatcherUnhandledExceptionEventArgs e)
        {
            MessageBox.Show(e.Exception.ToString());
            e.Handled = true;
        }
    }
    {% endraw %}
  {% endhighlight c# %}
 