---
title: LobbyScene Implement, Project QA
author: HuiSung
date: 2023-11-22
categories:
  - Bootcamp
  - TIL
tags:
  - TIL
  - QA
image:
  path: /assets/img/CS_TIL_11-22.png
---

로비 씬은 다른 팀원들에 비해 기능이 굉장히 적은편에 속하는데 그래서 UI적인 요소<br>또는  팀원들이 사용할 수 있는 프레임워크에 집중할 수 있었다.

---

## 로비 씬의 구성

- 메인 타이틀 텍스트 `(ASCII ART)`
- 메뉴 박스 및 메뉴 아이템
	- `ReadKey()`를 이용한 위/아래 메뉴 선택
- 컬럼 박스 및 설명란
- `Scene Change` - 씬 전환은 씬 매니저가 있기에 다른 기능을 쓸 필요가 없다.

### <mark style="background: #BBFABBA6;"></mark>&nbsp;💫Lobby.cs

- 아스키 아트를 이용해 타이틀 텍스트를 출력할 때 네온사인같은 구현을 하고 싶었다.
- 위에 내용처럼 구현하기 위해서는 백그라운드 쓰레드에서 다른 실행을 해야한다.

```cs

using HexaCoreVillage.Utility;

namespace HexaCoreVillage.Lobby;

public class Lobby : Scene
{
    #region Member Variables
    public override SCENE_NAME SceneName => SCENE_NAME.LOBBY;

    // .. Variables

    #endregion



    #region Flow Methods
    public override void Start()
    {
        base.StartCommon();

		// .. Initialize
    }

    public override void Update()
    {
        base.UpdateCommon();

        this.DrawUpdate();
    }

    public override void Stop()
    {
        base.StopCommon();
        _isRunning = false;
        _titleUpdateThread.Join();
    }
    #endregion



    #region Initalize
    // .. Init Methods

    private void InitThread()
    {
        _titleUpdateThread = new Thread(TitleUpdateLoop);
        _titleUpdateThread.Start();
    }
    #endregion



    #region Logic Methods
    private void DrawUpdate()
    {
        while (!KeyAvailable)
            this.PrintMenuItems();

        if (KeyAvailable)
            Managers.UI.ClearRowsColSelect(StartPosY, 32, 60, 116);

        this.InputSelectMenuItem();
    }

    private void PrintMenuItems()
    {
        string[] menuItemTexts =
        {
            "개발자 스펙/기기 - (스탯/인벤)",
            "헥사 IT 스토어 - (상점)",
            "디버깅 미궁 - (던전)",
            "월차 내러 가기 - (휴식)",
            "처음으로 - (타이틀 화면)",
            "종료"
        };

        lock(_lock)
        {
            Managers.UI.PrintMsgAlignCenter("< H E X A   M E N U >", StartPosY - 3, ConsoleColor.Blue);
            for(int idx = 0; idx < menuItemTexts.Length; ++idx)
            {
                ConsoleColor color = ConsoleColor.Gray;
                if(idx == ((int)_menuSelector))
                {
                    string newItemText = new string(">         " + menuItemTexts[idx] + "         <");
                    menuItemTexts[idx] = newItemText;
                    color = ConsoleColor.Blue;
                }

                Managers.UI.PrintMsgAlignCenter(menuItemTexts[idx], (StartPosY+2) + (idx * 3), color);
            }
        }
    }

    private void InputSelectMenuItem()
    {
        var keyInfo = ReadKey(true);

        switch(keyInfo.Key)
        {
            case ConsoleKey.W:
            case ConsoleKey.UpArrow:
                _menuSelector = (_menuSelector - 1 < 0) ?
                    (LobbySelectMenu)(Enum.GetValues(typeof(LobbySelectMenu)).Length - 1) :
                    _menuSelector - 1;
                break;
            case ConsoleKey.S:
            case ConsoleKey.DownArrow:
                _menuSelector = (_menuSelector + 1 >= (LobbySelectMenu)Enum.GetValues(typeof(LobbySelectMenu)).Length) ?
                    LobbySelectMenu.Status :
                    _menuSelector + 1;
                break;
            case ConsoleKey.Spacebar:
            case ConsoleKey.Enter:
                this.EnterSpacebarProcess();
                break;
        }
    }

    private void EnterSpacebarProcess()
    {
        switch(_menuSelector)
        {
            case LobbySelectMenu.Status:
                Managers.Scene.LoadScene(SCENE_NAME.STATUS);
                break;
            case LobbySelectMenu.Store:
                Managers.Scene.LoadScene(SCENE_NAME.STORE);
                break;
            case LobbySelectMenu.Dungeon:
                Managers.Scene.LoadScene(SCENE_NAME.BATTLE);
                break;
            case LobbySelectMenu.Rest:
                break;
            case LobbySelectMenu.GoTitle:
                Managers.Scene.LoadScene(SCENE_NAME.LOGIN);
                break;
            case LobbySelectMenu.Exit:
                Clear();
                Stop();
                Thread.Sleep(1000);
                Environment.Exit(0);
                break;
        }
    }

    // .....

    #region Thread Methods
    private void TitleUpdateLoop()
    {
        this.DrawMenuBox();

        while (_isRunning)
        {
            DrawLobbyTitleTextbyThread();
            Thread.Sleep(DrawTitleInterval);
        }
    }

    private void DrawLobbyTitleTextbyThread()
    {
        lock (_lock)
        {
            int startPosY = Managers.UI.StartPosY + 1;

            Managers.UI.ClearRows(1, 10);

            for (int row = 0; row < _titleTextSplit.Length; ++row)
            {
                ConsoleColor currentColor = _colorArray[_colorIndex % _colorArray.Length];
                Managers.UI.PrintMsgAlignCenter(_titleTextSplit[row], row + startPosY, currentColor);

                if (row % 2 == 0 && row != 0)
                    ++_colorIndex;
            }
        }
    }
    #endregion
}
```

>`SetCursorPosition` 부분이 겹쳐 다른 곳에 출력되는 문제 발생.
{: .prompt-warning }

`Thread`를 사용하여 백그라운드 쓰레드를 실행해 멀티쓰레딩을 진행하면 `SetCursorPosition`을 사용할 때<br>
이곳 저곳에서 액세스 하여 동기화가 이루어 지지 않는 문제였다.
<br>
>다른 쓰레드가 접근중일 때 액세스 하지 못하게 `lock`을 걸어주어 해결했다.
{: .prompt-tip }

---

## QA (Quality Assurance)<br>

### <mark style="background: #FFB8EBA6;"></mark>&nbsp;🐛발생한 버그들<br>

- `LoadScene()` 이후 다음 씬으로 이동했을 때 버퍼가 남아 있는 문제<br><br>
- `LoadScene()` 이후 다른 씬의 생명주기가 끝나지 않는 문제<br><br>
- BattleScene - `currentBug[index]` 부분에서 범위를 벗어나는 문제<br><br>
- StoreScene - 판매에서 Key가 먹히지 않는 문제<br><br>

이 외에도 각자의 씬에서 자잘한 버그들이 나왔지만 바로 해결이 가능한 작은 문제들이였기에<br>
따로 버그 목록에 작성을 하지는 않았다. **치명적이였던** 굵직한 문제들만 처리했다.

---

## <mark style="background: #ADCCFFA6;"></mark>&nbsp;🛠️해결 과정<br>

### <mark style="background: #FF5582A6;"></mark>&nbsp;＃LoadScene Buffer Problem [해결]
- C#에서는 GC(Garbage Collector)가 자동으로 메모리 및 입출력 버퍼를 처리해주는데,<br>
  이 점 때문에 강제적으로 버퍼를 지울 수 있게 제공되지 않았다.
- 방법이 없나 하던 찰나 아직 버퍼가 남아있다면 `KeyAvailable`이 <span style="color:#00b0f0">true</span>일 것이라 판단.<br>
  씬이 넘어가는 `LoadScene()` 메서드에서 해당 내용을 처리해주었다.
```cs
    private void StreamClear()
    {
        while (Console.KeyAvailable)
        {
            Console.ReadKey(false);
            Console.Clear();
        }
    }
```

### <mark style="background: #FF5582A6;"></mark>&nbsp;＃LoadScene - Scene Life Cycle Problem [해결]
- 기본적인 프레임워크를 만들고 각자 해당하는 씬에서 작업을 진행했었다.
- 하지만 `while(true)`로 동작하는 메서드부분들이 사용하는 것으로 인식하고<br>
  해당 Scene이 GC에 의해 해제되지 않고 해당 호출 스택으로 돌아가는 문제였다.
- 초기에는 각 씬에 플래그를 사용하여 처리했다.
	- `private bool _isLoadScene = false`
	- `LoadScene()` 메서드를 호출하는 부분에서 true로 변경
- **<span style="font-weight:bold; color:#b92727">일시적으로는 해결이 되었으나 다른 Scene에서 `while(true)`를 사용하는 곳에서도 똑같은 문제 발생</span>**
- 프레임워크 자체가 문제라 판단하여 `Scene Class` 내용을 수정하였다.

```cs
public abstract class Scene
{
    protected bool _isRun;
    public abstract SCENE_NAME SceneName { get; }

    // Initalize Method
    public abstract void Start();
    public abstract void Update();
    public abstract void Stop();

    // Start,Update Logic
    protected void StartCommon()
    {
        _isRun = true;
    }
    protected void UpdateCommon()
    {
        if (!_isRun) return;
    }
    protected void StopCommon()
    {
        _isRun = false;
    }

    // Getter
    public bool GetIsRun() { return _isRun; }
}
```

- 플래그를 abstract Scene class 자체에 넣어 base로 사용하게 했다.
- 이 후 각 씬에서 사용되는 while문의 조건들을 `_isRun`으로 바꾸니 모든 문제가 해결이 됐다.

![](https://i.imgur.com/NxrfBvi.png)

- 버그를 Fix하면서 깨달은 것은 프레임워크 부분은 `많은 테스트가 필요하고 완벽한 설계가 필요`이다.
- 프레임워크를 고치면 참조하고 있는 다른 모든 Scene들도 고쳐야하는 번거로움이 있기 때문이다.

### <mark style="background: #FF5582A6;"></mark>&nbsp;＃BattleScene OutOfRange Problem [해결]
- BattleRightWindow.cs 부분에서 계속 OutOfRange 예외를 내뱉었다.

```cs
public static void BugStatus()
{
	CurrentBug = DebuggingBugs[CurrentBugIndex];
	// ...
}
```

- DebuggingBugs에 문제인줄 알았는데 디버깅을 해보니 `CurrentBugIndex`문제였다.
- 정확한 인덱스값이 안들어가고 쓰레기 값이 들어 있는 것이다.
- **<span style="font-weight:bold; color:#b92727">초기화 문제</span>**로 인식하고 내용을 찾아보니 Battle.cs에서 초기화를 안하고 있었다.

![](https://i.imgur.com/GJKI495.png)

- 초기화를 진행 해주었더니 해당하는 내용이 정상적으로 동작하게 됐다.
- 아마 이부분은 초기화 문제만 있는게 아닌 Scene 방식으로 동작/진행되서 그런 것 같다.
	- ~~- 결국 내가 프레임워크를 제대로 설계하지 못한 잘못... 팀원들에게 사과를..~~

---

## 마무리 하며

> Lobby 씬을 구현하는데 있어서 어려웠던 부분은 기능이 적었던터라 크게 없었지만,<br>
> 비동기(Async)를 이용해 다중쓰레딩을 구현할 때 TASK 문제점을 찾기가 너무 어려웠다.<br>
>그래도 해당 문제점을 통해서 쓰레드 개념을 좀 더 이해할 수 있어 다행이라고 생각한다.

<br>
QA를 진행하면서 끝도 없이 나오는 자잘한 버그들 때문에 협업이 쉬운게 아니라는 것을 느꼇다.<br>
어떤 버그를 고치니 다른 버그가 나오고 하지만 이런 QA를 한다는건 게임을 프로덕트로 내보내기 전<br>
아주 중요한 필수작업이고 이런 QA를 가질 수 있다는 것 만으로도 도움됐다. <br>
<br>