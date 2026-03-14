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

### 로그만 작성 ('26.03.04 Update)
  덤프파일을 남기지 않고 로그만 남기는 방법이다.
  {% highlight c++ %}
    {% raw %}
LONG CALLBACK TopLevelExceptionFilterCallBackLog(EXCEPTION_POINTERS* exceptionInfo)
{

	// 1. 예외 코드 및 주소 분석
	DWORD exceptionCode = exceptionInfo->ExceptionRecord->ExceptionCode;
	PVOID exceptionAddress = exceptionInfo->ExceptionRecord->ExceptionAddress;

	// 1. 디버깅 중 발생하는 중단점(Breakpoint) 등은 무시
	if (exceptionCode == EXCEPTION_BREAKPOINT || exceptionCode == EXCEPTION_SINGLE_STEP) {
		return EXCEPTION_CONTINUE_SEARCH;
	}

	// 2. C++ 소프트웨어 예외(try-catch로 잡히는 것들)는 무시하고 싶다면?
	// C++ 예외 코드인 0xE06D7363를 필터링합니다.
	if (exceptionCode == 0xE06D7363 || exceptionCode == 0x000006BA || exceptionCode == 0xE0434352) {
		return EXCEPTION_CONTINUE_SEARCH;
	}

	SYSTEMTIME CurrentTime;
	GetLocalTime(&CurrentTime);

	std::stringstream Path;//본인이 저장하고 싶은 경로
	Path << ".\\Crash_" << CurrentTime.wYear << std::setfill('0') << std::setw(2) << CurrentTime.wMonth << std::setfill('0') << std::setw(2) << CurrentTime.wDay
		<< "_" << std::setfill('0') << std::setw(2) << CurrentTime.wHour << std::setfill('0') << std::setw(2) << CurrentTime.wMinute << std::setfill('0') << std::setw(2) << CurrentTime.wSecond << ".txt";
	
	HANDLE hProcess = GetCurrentProcess();
	HANDLE hThread = GetCurrentThread();
	SymInitialize(hProcess, NULL, TRUE);

	// 스택 탐색을 위한 컨텍스트 설정
	STACKFRAME64 stackFrame = { 0 };
	DWORD machineType = IMAGE_FILE_MACHINE_AMD64; // 64비트 기준

	stackFrame.AddrPC.Offset = exceptionInfo->ContextRecord->Rip;
	stackFrame.AddrPC.Mode = AddrModeFlat;
	stackFrame.AddrFrame.Offset = exceptionInfo->ContextRecord->Rbp;
	stackFrame.AddrFrame.Mode = AddrModeFlat;
	stackFrame.AddrStack.Offset = exceptionInfo->ContextRecord->Rsp;
	stackFrame.AddrStack.Mode = AddrModeFlat;

	FILE* logFile;
	fopen_s(&logFile, Path.str().c_str(), "a");
	if (!logFile) return 0L;

	const char* reason = "Unknown Exception";
	switch (exceptionCode) {
	case EXCEPTION_ACCESS_VIOLATION:         reason = "Access Violation (잘못된 메모리 참조)"; break;
	case EXCEPTION_ARRAY_BOUNDS_EXCEEDED:    reason = "Array Bounds Exceeded (배열 범위 초과)"; break;
	case EXCEPTION_BREAKPOINT:               reason = "Breakpoint Hit"; break;
	case EXCEPTION_DATATYPE_MISALIGNMENT:    reason = "Datatype Misalignment"; break;
	case EXCEPTION_FLT_DIVIDE_BY_ZERO:       reason = "Floating Point Divide by Zero (부동소수점 0 나누기)"; break;
	case EXCEPTION_INT_DIVIDE_BY_ZERO:       reason = "Integer Divide by Zero (정수 0 나누기)"; break;
	case EXCEPTION_STACK_OVERFLOW:           reason = "Stack Overflow (스택 오버플로)"; break;
	case EXCEPTION_ILLEGAL_INSTRUCTION:      reason = "Illegal Instruction (잘못된 명령)"; break;
	}

	fprintf(logFile, "========================================\n");
	fprintf(logFile, "[EXCEPTION REASON] %s (Code: 0x%08X)\n", reason, exceptionCode);
	fprintf(logFile, "[FAULT ADDRESS]    0x%p\n", exceptionAddress);

	// Access Violation인 경우 읽기 시도였는지 쓰기 시도였는지 추가 정보 기록
	if (exceptionCode == EXCEPTION_ACCESS_VIOLATION && exceptionInfo->ExceptionRecord->NumberParameters >= 2) {
		auto type = exceptionInfo->ExceptionRecord->ExceptionInformation[0]; // 0: 읽기, 1: 쓰기
		auto addr = exceptionInfo->ExceptionRecord->ExceptionInformation[1]; // 접근하려던 주소
		fprintf(logFile, "[DETAIL] %s attempt at address 0x%p\n", (type ? "Write" : "Read"), (void*)addr);
	}

	fprintf(logFile, "--- Call Stack Trace ---\n");

	while (StackWalk64(machineType, hProcess, hThread, &stackFrame,
		exceptionInfo->ContextRecord, NULL,
		SymFunctionTableAccess64, SymGetModuleBase64, NULL)) {

		DWORD64 address = stackFrame.AddrPC.Offset;
		if (address == 0) break;

		// 1. 함수 이름 가져오기
		char symbolBuffer[sizeof(SYMBOL_INFO) + MAX_SYM_NAME * sizeof(TCHAR)];
		PSYMBOL_INFO pSymbol = (PSYMBOL_INFO)symbolBuffer;
		pSymbol->SizeOfStruct = sizeof(SYMBOL_INFO);
		pSymbol->MaxNameLen = MAX_SYM_NAME;

		DWORD64 displacement = 0;
		if (SymFromAddr(hProcess, address, &displacement, pSymbol)) {
			fprintf(logFile, "Func: %s ", pSymbol->Name);
		}

		// 2. 파일명 및 줄 번호 가져오기
		IMAGEHLP_LINE64 line;
		line.SizeOfStruct = sizeof(IMAGEHLP_LINE64);
		DWORD displacementLine = 0;
		if (SymGetLineFromAddr64(hProcess, address, &displacementLine, &line)) {
			fprintf(logFile, "(File: %s, Line: %d)\n", line.FileName, line.LineNumber);
		}
		else {
			fprintf(logFile, "(No Source Info)\n");
		}
	}

	fprintf(logFile, "------------------------\n\n");
	fclose(logFile);
	SymCleanup(hProcess);
	return EXCEPTION_CONTINUE_SEARCH;
}

int main()
{
  AddVectoredExceptionHandler(1, TopLevelExceptionFilterCallBackLog); // 1은 최우선 순위
}
   {% endraw %}
  {% endhighlight c++ %}

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

            AppDomain.CurrentDomain.UnhandledException += CurrentDomain_UnhandledException;
            TaskScheduler.UnobservedTaskException += TaskScheduler_UnobservedTaskException;
        }
        private void TaskScheduler_UnobservedTaskException(object sender, UnobservedTaskExceptionEventArgs e)
        {
            //throw new NotImplementedException();
            string strMsg = $"TaskScheduler UnHandled Error : {e.Exception}";
            G.WriteLog(strMsg, G.LOGTYPE_ERROR);
            MessageBox.Show(strMsg);
        }

        private void CurrentDomain_UnhandledException(object sender, UnhandledExceptionEventArgs e)
        {
            //throw new NotImplementedException();
            string strMsg = $"CurrentDomain UnHandled Error : {e.ExceptionObject.ToString()}";
            G.WriteLog(strMsg, G.LOGTYPE_ERROR);
            MessageBox.Show(strMsg);
        }
        void Dispatcher_UnhandledExceptionFilter(object sender, DispatcherUnhandledExceptionFilterEventArgs e)
        {
            MessageBox.Show(e.Exception.ToString());
            //e.RequestCatch = true; //예외 발생시 프로그램 종료 하지 않음.
            e.RequestCatch = false; //예외 발생시 프로그램 종료.
        }

        void Dispatcher_UnhandledException(object sender, DispatcherUnhandledExceptionEventArgs e)
        {
            MessageBox.Show(e.Exception.ToString());
            //e.Handled = true; //예외 발생시 프로그램 종료 하지 않음.
            e.Handled = false; //예외 발생시 프로그램 종료.
        }
    }
    {% endraw %}
  {% endhighlight c# %}
 