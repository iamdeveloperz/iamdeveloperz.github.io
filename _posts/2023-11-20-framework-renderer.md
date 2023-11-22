---
title: C# Hexa Village - Framework Renderer
author: HuiSung
categories: 
  - Bootcamp
  - TIL
tags:
  - TIL
  - Framework
image:
  path: /assets/img/CS_TIL_11-20.png
---

기본적인 프레임워크(Scene)을 구성하고 팀프로젝트를 진행하는 도중에 문제가 발생했다.<br>
각 운영체제(플랫폼)별로 UI해상도가 맞지 않고 사용자가 임의로 콘솔을 재정의할 수 있는 점이다.<br>
이를 보완하기 위한 플랫폼에 대응하는 프레임워크를 제작했다.<br>
<br>
## 플랫폼에 대응하는 프레임워크
### <mark style="background: #FFB8EBA6;"></mark>&nbsp;기존 프레임워크의 구성 형태

![Old Framework Composition](https://i.imgur.com/OyoIkwj.png)

- 기존에 프레임워크는 아주 간단하게 Scene만 관리하는 형태로 구성되어 있다.
- 이 형태는 그대로 유지하되, `운영체제(플랫폼)별로 콘솔창이 대응`되게 만들어야 했다.
	+ 첫 방법으로는 C#에서 기본적으로 제공하는 Console 명령어들을 이용하는 것이 있었는데 실패했다.
	+ 이유인 즉슨 위 방법으로는 한계점이 명확했는데, 최대화 최소화 같은 메뉴바에 접근할 수 없다.
	+ <span style="color:#c00000">[!]</span> 윈도우즈 응용프로그램을 제어할 수 있는 API가 필요하다.
- Windows는 응용프로그램을 명확하게 다룰 수 있는 방법으로 Windows API가 제공된다.
***
### <mark style="background: #BBFABBA6;"></mark>&nbsp;💫Windows API - Framework
- Windows API를 활용해서 콘솔 애플리케이션에 창들을 조작할 수 있다.
- 필요한 기능은 다음과 같다.
	+ 사용자 조작 불가 : 창크기 변경, 최소화, 최대화
	+ 창 크기 조절 : 버퍼크기 [X 180] [Y 40]
- 위 내용을 토대로 해서 다음과 같은 프레임 워크를 구성했다.<br>
```cs

using System.Runtime.InteropServices;

namespace HexaCoreVillage.Framework;

public class WindowsAPI
{
    #region Member Variables

	// ...

    private const string KERNEL = "kernel32.dll";
    private const string USER = "user32.dll";

	// ...

    /* Variables */
    //public static IntPtr hCursor = GetForegroundWindow();
    public static IntPtr hConsole = GetConsoleWindow();
    public static IntPtr hOuput = GetStdHandle(STD_OUTPUT_HANLDE);

    private static readonly IntPtr HWND_TOP = IntPtr.Zero;

    #endregion




    #region Extern Function

    /* Get Handlers */
    [DllImport(USER, SetLastError = true)] public static extern IntPtr GetForegroundWindow();
    [DllImport(KERNEL, SetLastError = true)] public static extern IntPtr GetConsoleWindow();
    [DllImport(KERNEL, SetLastError = true)] public static extern IntPtr GetStdHandle(int nStdHandle);


    // Setting Console Title Text
    [DllImport(USER, CharSet = CharSet.Auto, SetLastError = true)] public static extern bool SetWindowText(IntPtr hWnd, String lpText);

    // Get Console Menus
    [DllImport(USER, SetLastError = true)] private static extern int GetWindowLong(IntPtr hWnd, int nIndex);
    // Setting Console Menus
    [DllImport(USER, SetLastError = true)] private static extern int SetWindowLong(IntPtr hWnd, int nIndex, int dwNewLong);




    // Setting Console Buffers
    [DllImport(KERNEL, SetLastError = true)] private static extern bool SetConsoleScreenBufferSize(IntPtr hConsoleOutput, COORD dwSize);

    // Setting Window ConsoleSize by Buffers
    [DllImport(KERNEL, SetLastError = true)]
    private static extern bool SetConsoleWindowInfo(IntPtr hConsoleOutput, bool bAbsolute, ref RECT lpConsoleWindow);




    // Windows 콘솔의 사이즈 또는 위치 지정하는 함수
    [DllImport(USER, SetLastError = true)]
    private static extern bool SetWindowPos(IntPtr hWnd, IntPtr hWndInsertAfter, int X, int Y, int cx, int cy, uint uFlags);

    // 현재 화면의 해상도를 구하는 함수
    [DllImport(USER)] private static extern int GetSystemMetrics(int nIndex);

    // 현재 윈도우 콘솔창의 크기를 구하는 함수
    [DllImport("user32.dll")] [return: MarshalAs(UnmanagedType.Bool)]
    private static extern bool GetWindowRect(IntPtr hWnd, out INT_RECT lpRect);
    
    // 현재 윈도우 콘솔 버퍼의 정보를 구하는 함수

    [DllImport("kernel32.dll")]
    public static extern bool GetConsoleScreenBufferInfo(IntPtr hConsoleOutput, out CONSOLE_SCREEN_BUFFER_INFO lpConsoleScreenBufferInfo);

    #endregion




    #region User Function
    // ...
    #endregion
}
```
<br>핵심은 `user32.dll`과 `kernel32.dll`을 이용한 <span style="color:#ffff00">P/Invoke</span> 기법이다.<br>
C#에서는 Windows API를 사용할 수 있는 헤더들이 존재하지 않아 사용할 수 없을 줄 알았지만,<br>
[P/Invoke(플랫폼 호출)](https://learn.microsoft.com/ko-kr/dotnet/standard/native-interop/pinvoke) 방식을 이용해서 비관리형 라이브러리를 액세스 할 수 있다.<br><br>
핸들러들은 Windows API에서 제공하는 응용프로그램에 대한 핸들로서 제어할 수 있게 해준다.<br>

---

### <mark style="background: #FFB8EBA6;"></mark> &nbsp; Windows API 사용 문제점
`C#`에서 제공되는 공용 라이브러리가 아닌 외부 라이브러리이다 보니 사용하는데에 있어 불편함도 많고<br>
동작이 되지 않는 부분들이 너무 많았다. 특히 `GetConsoleWindow()`부분에서 에로사항이 많았다.<br>
<br>
분명히 정상적으로 작동이 되어야 하는 부분인데 해당 핸들러를 이용하면 내내 동작하지 않는 것이다.<br>
그래서 디버깅을 통해 해당 핸들러에 올바른 값이 들어가는 것을 확인헀다.
- 핸들 주소값은 명확하게 할당 되어 있음
- `ConsoleHandle`을 사용하면 디버깅 창이 동작하지 않음.
	+ `Foreground`를 이용하면 정상적으로 동작함.

이 부분에서 너무 많은 시간을 소요했었는데, 이유인 즉슨 StackOverflow에 자료 중 하나로,<br>
Windows11에서는 기본 터미널이 `ConsoleHost`가 아닌 `Terminal`로 되어 있었기 때문이다.<br><br>
![](https://i.imgur.com/FCPDWY9.png)<br>
이 부분을 `ConsoleHost`로 바꾸고 나니 모든 것이 정상적으로 동작했다.<br>
혹여나 싶어 터미널에서도 실행을 해보았지만 터미널에서만 동작하지 않는 것을 확인했다.<br>
<br>
보다 자세한 내용이 궁금해 계속 인터넷을 뒤졌는데 그 중 다음 내용을 확인할 수 있었다.<br>
<br>
![](https://i.imgur.com/LPTGObS.png)<br>
콘솔 호스트로 바꾸고 나서는 창에 대한 제어를 모두 정상적으로 할 수 있었다.


---

## 🎥<span style="color:#00b0f0">Renderer</span>
- 렌더러 클래스를 이용해 이제 Windows와 MAC에 대한 해상도 대응을 해야한다.
	+ 렌더러는 Windows API와 MAC과 관련된 라이브러리를 지녀야한다.
	+ MAC은 기본 해상도를 처리는 했지만 명확하게 다른 부분에 접근하기가 힘들었다.
	+ MAC은 터미널 환경이 응용프로그램이 아닌 내부적으로 돌아가기 때문이라고 한다.

<br>

### <mark style="background: #FFB8EBA6;"></mark>&nbsp; 🔨Renderer.cs
```cs

using System.Runtime.InteropServices;

namespace HexaCoreVillage.Framework;

public class Renderer : Singleton<Renderer>
{
    #region Member Variables

    /* Literals */
    // 버퍼 및 해상도 조절을 위한 리터럴
    public const int FixedXColumnBuffer = 180;
    public const int FixedYRowsBuffer = 40;

    // 실제 문자를 쓸 때 계산되는 X, Y
    public const int FixedXColumn = FixedXColumnBuffer - 1;
    public const int FixedYRows = FixedYRowsBuffer - 1;

    // 창 크기 제어 대기시간 (ms)
    public const int INIT_RESIZE_TIME_MS = 1000;

    /* Variables */
    private WindowsAPI? _winAPI = null;
    private MacAPI? _macAPI = null;

    #endregion



    #region Init Console(Terminal)

    /// <summary>
    /// 
    /// 현재 OS에 맞게 해상도를 조절해주는 메서드
    /// 
    /// # 윈도우
    /// ## 창 크기 설정
    /// ### 타이틀, 메뉴바, 스크롤, 버퍼, 포지셔닝
    /// 
    /// # 맥
    /// ## 창 크기 설정
    /// ### 내용 추가되면 추가바람
    /// </summary>
    public void InitalizeRenderer()
    {
        if (RuntimeInformation.IsOSPlatform(OSPlatform.Windows))
        {
            _winAPI = new WindowsAPI();
            _winAPI.ProgressConsoleSetting();
        }
        else if (RuntimeInformation.IsOSPlatform(OSPlatform.OSX))
        {
            _macAPI = new MacAPI();
            _macAPI.ProgressConsoleSetting();
        }

        WriteLine("창을 재설정 중입니다. 조금만 기다려주세요");
        Thread.Sleep(INIT_RESIZE_TIME_MS);
        WriteLine("설정이 완료됐습니다. 아무키나 누르면 시작됩니다.");
        ReadKey(true); Clear();
    }

    #endregion



    #region Main Methods
    public void DrawConsoleBorder()
    {
        CursorVisible = false;
        ForegroundColor = ConsoleColor.Green;
        // FixConsole();

        // 콘솔 상단을 그리는 로직
        SetCursorPosition(0, 0);
        Write('┌'); Write(new string('─', FixedXColumn - 2)); Write('┐');

        for(int yAxis = 1; yAxis < FixedYRows; ++yAxis)
        {
            SetCursorPosition(0, yAxis); Write('│');
            SetCursorPosition(FixedXColumn - 1, yAxis); Write('│');
        }

        // 콘솔 하단을 그리는 로직
        SetCursorPosition(0, FixedYRows);
        Write('└'); Write(new string('─', FixedXColumn - 2)); Write('┘');
        ResetColor();
    }
    #endregion
}
```
`RuntimeInformation.IsOSPlatform`을 통해 해당 플랫폼에 맞는 <span style="color:#ffc000">조건부 컴파일</span>을 진행했다.<br>
렌더러 클래스는 콘솔 애플리케이션에 대한 버퍼 및 사이즈 전체 정보를 들고 있다.<br><br>
여기서 핵심은 렌더러는 그려주는 콘솔 화면을 제어만 하는 역할이지 UI를 그리는 매니저가 아니다.<br>
`클래스는 하나의 기능을 담당해야한다.`라는 원칙을 지키기 위해서 노력했다.

---

이로써 팀 프로젝트에서 사용할 콘솔 애플리케이션 기초 뼈대 작업은 끝이 났다.<br>
하면서 어렵고 막히는.. 특히 윈도우11의 터미널...문제는 크게 시간을 잡아먹었다.<br><br>
그래도 관련된 내용들을 찾으면서 얻은 정보들은 유용하게 사용할 것 같다.