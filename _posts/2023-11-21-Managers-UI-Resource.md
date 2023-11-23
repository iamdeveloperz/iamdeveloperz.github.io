---
title: Managers Implements - UI, Resource
author: HuiSung
date: 2023-11-21
categories: 
  - Bootcamp
  - TIL
tags:
  - TIL
  - Managers
image:
  path: /assets/img/CS_TIL_11-21.png
---

매니저(관리자)는 게임 스크립트 구성에 있어서 필수 요소라고 할 수 있다.<br>
게임 매니저 또는 각종 매니저들을 이용해 중복되는 게임 요소들을 중앙 집중 관리 할 수 있다.<br>
<br>
## 리소스 매니저

리소스 매니저의 주 역할은 파일 자원(리소스)들을 관리하는 것이다.<br>
특히 리소스 매니저를 구현을 꼭 했어야 하는 이유가 존재하는데 다음과 같다.
- [x] 팀원들의 리소스 경로(Path) 통합
- [x] 문자열 사용 제한, 로지컬 에러 발생률 저하
- [x] Json, Sound, Txt 등 정제되지 않은 리소스를 쉽게 사용하기 위함

팀원들이 각자에 Scene에서 종속적이지 않게 작업을 했지만 제대로 된 프레임워크가 없어,<br>
리소스에 대한 파일 경로와 리소스를 불러오기 위한 중복된 코드들이 존재했다.<br>
중앙 집중 관리를 통해서 이를 한군데에 통합 해야만 한다.

### <mark style="background: #BBFABBA6;"></mark>&nbsp;💫Manager_Resource.cs

```cs
/// <summary>
/// # Resource 자원들을 관리하는 매니저 클래스
/// ## Utility -> Resources에 Key나 Path들을 관리한다.
/// ## .txt , .json과 같은 파일들을 로드하는 역할
/// </summary>
public class Manager_Resource
{
    #region Member Variables
    // JSON, TXT 파일 보관
    private Dictionary<ResourceKeys, string>? _textResources;
    // Sound 파일 경로 보관
    private Dictionary<ResourceKeys, string>? _soundResources;

    private bool _isComplete;

    #endregion


    #region Main Methods
    /// <summary>
    /// # 생성자 및 이니셜라이저 역할
    /// </summary>
    public Manager_Resource()
    {
        _textResources = new Dictionary<ResourceKeys, string>();
        _soundResources = new Dictionary<ResourceKeys, string>();

        _isComplete = false;
    }


    /// <summary>
    /// # 리소스를 모두 불러오는 메서드
    /// </summary>
    public void LoadAllResources()
    {
        string folderPath = GetResourceFolderPath();

        // 해당 폴더가 존재하지 않는다면?
        if (!Directory.Exists(folderPath))
            throw new Exception($"폴더 {folderPath}가 존재하지 않습니다.");
        else // 존재 한다면
        {
            var files = Directory.GetFiles(folderPath);

            foreach(string filePath in files)
            {
                string fileName = Path.GetFileNameWithoutExtension(filePath);
                string fileExtension = Path.GetExtension(filePath); // 확장자명

                if (IsSupportedFileExtension(fileExtension))
                    TextFileLoadFromResourceKey(filePath, fileName);
                else if (IsSupportedFileExtensionSound(fileExtension))
                    SoundFileLoadFromResourceKey(filePath, fileName);
            }
        }

        if(_textResources?.Count != 0 || _soundResources?.Count != 0)
            _isComplete = true;
    }

	// ...

    public string GetTextResource(ResourceKeys key)
    {
        if (_textResources?.ContainsKey(key) == true)
            return _textResources[key];
        else
        {
            WriteLine($"Text resources not found : {key.ToString()}");
            //throw new Exception($"해당하는 리소스{key.ToString()}가 존재하지 않습니다");
            return string.Empty;
        }
    }

    public string GetSoundResource(ResourceKeys key)
    {
        if (_soundResources?.ContainsKey(key) == true)
            return _soundResources[key];
        else
        {
            Console.WriteLine($"Sound resources not found : {key.ToString()}");
            throw new Exception($"해당하는 리소스{key.ToString()}가 존재하지 않습니다");
        }
    }

    // 제네릭 타입으로 반환받는 메서드
    public T GetResources<T>(ResourceKeys key)
    {
        if (typeof(T) == typeof(string) && _textResources != null)
        {
            if (_textResources.ContainsKey(key) == true)
                return (T)(object)_textResources[key];
        }
        else if (typeof(T) == typeof(AudioFileReader) && _soundResources != null)
        {
            if (_soundResources.ContainsKey(key) == true)
                return (T)(object)_soundResources[key];
        }
        
        throw new Exception($"해당하는 리소스{key.ToString()}가 존재하지 않습니다");
    }

    #endregion



    #region Helper Methods
    /// <summary>
    /// # 리소스 폴더의 위치를 반환하는 메서드
    /// </summary>
    public string GetResourceFolderPath()
    {
        // 현재 앱이 실행되는 위치에 디렉토리 즉 .Net폴더
        string currentDirectory = AppDomain.CurrentDomain.BaseDirectory;

        // 상위 폴더로 이동
        string teamProjectDirectory = Path.Combine(currentDirectory, "..", "..", "..");
        string resourcePath = Path.Combine(teamProjectDirectory, "Resources");

        return resourcePath;
    }
	// ...
}
```

Utility - Literals.cs에 `ResourceKey`라는 enums(열거형)을 만들어 리소스를 불러 올 때<br>
매핑이 안되서 발생하는 오류가 최대한 없도록 만들었다.

```cs
    public enum ResourceKeys
    {
        // TEXT
        TextLobbyAscii,
        // JSON
        BugList,
        DebugText,
        ItemList,
        SavePlayer,
        // BGMS
        newWorldBGM,
        startBGM,
        BattleBGM
    }
```

---

### <mark style="background: #FFB8EBA6;"></mark>&nbsp;만들면서 발생했던 문제
- <span style="color:#00b0f0">파일 확장자 문제</span>
	- 파일 확장자별로 처리를 했어야하는데 Extension이라는 메서드를 몰라 발생했던 문제다.
	- 초기에는 GetFiles로 받아온 경로에 맨뒷자리부터 일일히 검사를 했었다.
	- Path 클래스에서 제공하는 `GetExtension()` 메서드를 통해 해결했다.<br><br>

- <span style="color:#00b0f0">사운드파일 처리 문제</span>
	- 사운드파일을 넣었지만 이를 관리하는 방식이 계속 바껴서 발생한 문제다.
	- NAudio Package를 사용할 때 AudioFileReader 형태로 사용 한다.
	- MAC에서 `winmm.dll`문제로 인해 폐기하고 `NetCoreAudio`로 변경.
	- Play를 하기 위해서는 클래스 파일이 아닌 `경로 형태`로 다시 지정해야 했다.
	- 사용에 있어 계속 불편함이 있었는데, 제네릭으로 메서드를 만들어 문제를 해결했다.

```cs
// 제네릭 타입으로 반환받는 메서드
public T GetResources<T>(ResourceKeys key)
{
	if (typeof(T) == typeof(string) && _textResources != null)
	{
		if (_textResources.ContainsKey(key) == true)
			return (T)(object)_textResources[key];
	}
	else if (typeof(T) == typeof(AudioFileReader) && _soundResources != null)
	{
		if (_soundResources.ContainsKey(key) == true)
			return (T)(object)_soundResources[key];
	}
	
	throw new Exception($"해당하는 리소스{key.ToString()}가 존재하지 않습니다");
}
```

---

## UI 매니저
콘솔에서는 UI를 통합적으로 관리하기가 어려운 환경이다. 실제 눈에 그려지는 것들이 전부 X, Y좌표계에서 그려지기 때문에 `SetCursorPosition`을 이용해 원하는 위치에 출력을 하고 지우는 것은 쉽지 않기 때문이다.<br>
<br>그래서 콘솔에서 사용되는 **UIManager**에 역할을 정의하기로 했다.
- 중복된 출력 코드들을 통합해서 제공 - 위치를 지정하고 컬러를 넣고 출력하는 등
- 원하는 위치를 지우는(공백) 메서드 제공 - `posRow, FromCol, ToCol`과 같은 형태
- Box & Border를 그려주는(Draw) 메서드 제공
- 텍스트를 정렬(가운데)해 출력해주는 메서드 제공<br>

### <mark style="background: #BBFABBA6;"></mark>&nbsp;💫Manager_UserInterface.cs

```cs
#region Draw Methods
public int DrawAsciiMessage(ResourceKeys key, int startPosY = 2)
{
	string asciiArtResource = Managers.Resource.GetTextResource(key);
	string[] asciiArtSplitMessage = asciiArtResource.Split(new[] { "\n" }, StringSplitOptions.None);

	int idx;
	for(idx = 0; idx < asciiArtSplitMessage.Length; ++idx)
	{
		ConsoleColor color = ConsoleColor.Gray;
		if (idx < 3) color = ConsoleColor.Yellow;
		else if (idx >= 3 && idx < 6) color = ConsoleColor.Cyan;
		else color = ConsoleColor.Blue;

		PrintMsgAlignCenter(asciiArtSplitMessage[idx], idx + startPosY, color);
	}

	return idx;
}

public void DrawBox(int startPosX, int startPosY, int width, int height, ConsoleColor color = ConsoleColor.Green)
{
	string topView = "┌" + new string('─', width - 2) + "┐";
	string bottomView = "└" + new string('─', width - 2) + "┘";

	PrintMsgCoordbyColor(topView, startPosX, startPosY, color);
	for(int col = startPosY + 1; col < startPosY + height; ++col)
	{
		string symbol = "│";
		PrintMsgCoordbyColor(symbol, startPosX, col, color);
		PrintMsgCoordbyColor(symbol, startPosX + width - 1, col, color);
	}
	PrintMsgCoordbyColor(bottomView, startPosX, startPosY + height, color);
}

public COORD[] DrawColumnBox(int startPosX, int startPosY, int width, int height, int columnsNumber = 2, ConsoleColor color = ConsoleColor.Green)
{
	int columnWidth = (width - 1) / columnsNumber;
	COORD[] columnPositions = new COORD[columnsNumber];

	// 상단 테두리
	string topView = "┌" + string.Join("┬", Enumerable.Repeat(new string('─', columnWidth), columnsNumber)) + "┐";
	PrintMsgCoordbyColor(topView, startPosX, startPosY, color);

	// 측면 테두리 및 컬럼 분리선
	for (int col = startPosY + 1; col < startPosY + height; ++col)
	{
		string sideView = "│" + string.Join("│", Enumerable.Repeat(new string(' ', columnWidth), columnsNumber)) + "│";
		PrintMsgCoordbyColor(sideView, startPosX, col, color);
	}

	// 하단 테두리
	string bottomView = "└" + string.Join("┴", Enumerable.Repeat(new string('─', columnWidth), columnsNumber)) + "┘";
	PrintMsgCoordbyColor(bottomView, startPosX, startPosY + height, color);

	// 컬럼 시작 좌표 계산
	for (int i = 0; i < columnsNumber; ++i)
		columnPositions[i] = new COORD(
			startPosX + 2 + i * (columnWidth + 1),
			startPosY + 1);

	return columnPositions;
}
#endregion



#region Print Text Methods
/// <summary>
/// 중앙 정렬하여 출력하는 메서드
/// </summary>
public void PrintMsgAlignCenter(string message, int posY, ConsoleColor fontColor = ConsoleColor.Gray)
{
	PrintMsgAlignCenter(message, posY, fontColor, BackgroundColor);
}
public void PrintMsgAlignCenter(string message, int posY, 
	ConsoleColor fontColor, ConsoleColor bgColor)
{
	lock (_lock)
	{
		int padding = (Renderer.FixedXColumn - GetMessageUTFLength(message)) / 2;

		SetPos(padding, posY);
		PrintMsgToColor(message, fontColor, bgColor);
	}
}

/// <summary>
/// X : 중앙, Y도 중앙
/// 즉 화면 정중앙에 출력하는 메서드
/// </summary>
public void PrintMsgAlignCenterByCenter(string message, ConsoleColor fontColor = ConsoleColor.Gray)
{
	PrintMsgAlignCenterByCenter(message, fontColor, BackgroundColor);
}
public void PrintMsgAlignCenterByCenter(string message,
	ConsoleColor fontColor, ConsoleColor bgColor)
{
	lock (_lock)
	{
		int wPadding = (Renderer.FixedXColumn - GetMessageUTFLength(message)) / 2;
		int hPadding = (Renderer.FixedYRows / 2);

		SetPos(wPadding, hPadding);
		PrintMsgToColor(message, fontColor, bgColor);
	}
}

/// <summary>
/// 원하는 위치에 출력하는 메서드
/// </summary>
public void PrintMsgCoordbyColor(string message, int posX, int posY, ConsoleColor fontColor = ConsoleColor.Gray)
{
	PrintMsgCoordbyColor(message, posX, posY, fontColor, BackgroundColor);
}
public void PrintMsgCoordbyColor(string message, int posX, int posY,
	ConsoleColor fontColor, ConsoleColor bgColor)
{
	lock (_lock)
	{
		SetPos(posX, posY);
		PrintMsgToColor(message, fontColor, bgColor);
	}
}
#endregion



#region Clear Methods
public void ClearRow(int row)
{
	lock (_lock)
	{
		SetPos(StartPosX, row);
		PrintMsg(new string(' ', areaX));
	}
}

public void ClearRows(int from, int to)
{
	lock (_lock)
	{
		for (int row = from; row <= to; ++row)
		{
			SetPos(StartPosX, row);
			PrintMsg(new string(' ', areaX));
		}
	}
}

public void ClearRowColSelect(int row, int colStart, int colEnd)
{
	lock (_lock)
	{
		SetPos(colStart, row);
		PrintMsg(new string(' ', colEnd - colStart));
	}
}

public void ClearRowsColSelect(int from, int to, int colStart, int colEnd)
{
	lock(_lock)
	{
		for(int row = from; row <= to; ++row)
		{
			SetPos(colStart, row);
			PrintMsg(new string(' ', colEnd - colStart));
		}
	}
}
```

`Draw()` 무언가를 그려주는 메서드들을 구현할 때 많이 애를 먹었었다.<br>
버그가 있거나 그런 것은 아니었지만 수학적인 공식이 많이 들어가야 했기 때문이다.<br><br>
박스의 초기좌표 `posX, posY`를 지정받고 그 좌표를 기준으로 `Right, Bottom`으로 그려나가는 방식이다.<br>
자동화를 시키기 위해서는 꼭 필요한 작업이었기에 시간이 좀 걸리더라도 구현을 해야했다.<br>
특히 열이 그려지는 컬럼 박스를 그릴 때 힘들었는데 `Join`과 `Enumerable.Repeat`를 발견해 사용했다.<br>

---

### <mark style="background: #FFB8EBA6;"></mark>&nbsp; ColumnBox - 열이 나눠진 박스

- **<span style="color:#ffc000">string.Join</span>**
	- `string.Join(string separator, IEnumerable<string> values)`
		- separator : 문자열 사이에 삽입할 구분자(Delimiter)
		- values : 연결할 문자열의 컬렉션
	- 여러 문자열을 하나의 문자열로 결합할 때 사용한다.
	- 구분자(Delimiter)를 사용해서 문자열 컬렉션 요소들을 연결하는 형태이다.<br><br>
- **<span style="color:#ffc000">Enumerable.Repeat</span>**
	- `Enumerable.Repeat<T>(T element, int count)`
		- element : 반복할 값 또는 객체
		- count : 값이 반복될 횟수
	- var repeatedNumbers = Enumerable.Repeat(1, 5);
	- `// repeatedNumber: {1, 1, 1, 1, 1}`<br>

```cs
public COORD[] DrawColumnBox(int startPosX, int startPosY, int width, int height, int columnsNumber = 2, ConsoleColor color = ConsoleColor.Green)
{
	int columnWidth = (width - 1) / columnsNumber;
	COORD[] columnPositions = new COORD[columnsNumber];

	// 상단 테두리
	string topView = "┌" + string.Join("┬", Enumerable.Repeat(new string('─', columnWidth), columnsNumber)) + "┐";
	PrintMsgCoordbyColor(topView, startPosX, startPosY, color);

	// 측면 테두리 및 컬럼 분리선
	for (int col = startPosY + 1; col < startPosY + height; ++col)
	{
		string sideView = "│" + string.Join("│", Enumerable.Repeat(new string(' ', columnWidth), columnsNumber)) + "│";
		PrintMsgCoordbyColor(sideView, startPosX, col, color);
	}

	// 하단 테두리
	string bottomView = "└" + string.Join("┴", Enumerable.Repeat(new string('─', columnWidth), columnsNumber)) + "┘";
	PrintMsgCoordbyColor(bottomView, startPosX, startPosY + height, color);

	// 컬럼 시작 좌표 계산
	for (int i = 0; i < columnsNumber; ++i)
		columnPositions[i] = new COORD(
			startPosX + 2 + i * (columnWidth + 1),
			startPosY + 1);

	return columnPositions;
}
```

구분자로 `┬`특수문자를 사용 하고, 그 외에 해당 <span style="color:#ffc000">컬럼의 길이</span> 만큼 `─`로 반복해서 그려준다.<br>
그러고 `columnsNumber` 사용자로부터 입력받은 열의 개수만큼 이 과정을 반복한다.<br>

![](https://i.imgur.com/aEBg0lL.png)

**<span style="color:#92d050">\<실행 결과></span>**

![](https://i.imgur.com/0yBcIYx.png)

---

## 마무리 하며
UIManager를 구현할 때 계산식을 생각하는 것이 제일 힘들었다.<br>
어느 지점에서부터 어느 부분까지 그릴 때 정렬을 계산하는 것을 손으로 적어가며 기록했다.<br>
<br>
계산을 할 때는 확실히 컴퓨터로 키보드를 하는 것보다 손으로 머리속에 있는 것을 그려가는 것이 도움된다.<br><br>
**<span style="color:#92d050">리소스 매니저나 UI매니저를 구현할 때 공통적인 것이 무엇인지 파악하고 넣는 것</span>**<br>
이 부분이 정말 어려웠는데 객체 지향 프로그래밍에서의 `클래스 분리`에 해당하는 내용이다.<br>
<br>
이 부분을 제외하면 계속 매니저 프레임워크를 만들어 버릇한게 도움이 되는지,<br>
크게 어려운 부분은 없었다. 내일은 Lobby Scene을 구현하고 QA를 진행할 예정이다.
- **Lobby Scene 구현**
- **버그 잡기 및 QA [트러블 슈팅]**