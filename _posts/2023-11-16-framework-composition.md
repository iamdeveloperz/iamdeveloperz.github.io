---
title: C# Hexa Village - Framework Composition
author: HuiSung
date: 2023-11-16
categories:
  - Bootcamp
  - TIL
tags:
  - TIL
  - Framework
sticker: lucide//frame
image:
---

C# Console에서 게임 엔진 Unity처럼 동작하는 방법을 구성할려고 한다. 아주 간단한 구현 방법을 생각했는데<br>
유니티에서 제공되는 MonoBehaviour의 메인 기능을 Scene으로 추상화 시키는 것이다.

## 추상 클래스의 구성

* 가장 먼저 할 일은 Scene들이 가지고 있는 중복된 동작들을 요약/정리 하는 것이다.

> Scene은 `Start()`와 `Update()`로 기본 구성 되고, 초기화와 업데이트를 진행한다.<br>
> 위 내용을 토대로 abstract Scene class를 추상화만 시켜주면 완성이다.
<br>
---
### Scene Class
```
TEST
```

```C#
/*
** Title, Game, Lobby등의 씬이 상속받게 될 추상 클래스
*/
public abstract class Scene
{
	public abstract void Start();
	public abstract void Update();
}
```

추상 클래스는 상속받는 자식 클래스가 abstract로 지정된
`멤버 필드` 또는 `메서드`를 명시적으로 구현해야만 한다.

---
### <mark style="background: #FFB8EBA6;"></mark> Sub Class

```C#
public class Title : Scene
{
	public override void Start()
	{
		// Member Variables Init
	}

	public override void Update()
	{
		// break가 없는 while(true)라고 생각.
	}
}
```

추상 클래스를 통해 자식 클래스들을 제어할 수 있는 형태가 구성 됐다.

---
### Example

```C#
public static void Main()
{
	/*
	* Scene 클래스를 상속받는 Title을 동적 할당 생성 *
	* Start() 함수를 통해 단 한번 초기화를 시켜준다. *
	*/
	Title titleScene = new Title();
	titleScene.Start();
	
	Scene currentScene = titleScene; // 자식 titleScene을 받는다.
	
	while(true)
	{
		// currentScene은 추상 클래스지만 안에 내용은 자식이 들어가있다.
		// 즉 Update를 돌리면 안에 자식 내용이 돌아간다.
		currentScene.Update();
	}
}
```

<p align="center"><img src='https://github.com/iamdeveloperz/iamdeveloperz.github.io/assets/59020441/646379a5-3704-441a-ab38-a4fa9834d37d' border="5"></p>

---

## 프레임 워크 구현

* 위 내용을 기반으로 하여 이제 `SceneManager`만 구현하면 프레임워크의 완성이다.
* 씬 매니저의 구성은 다음 코드들로 이루어진다.

### <mark style="background: #FFB8EBA6;"></mark> Singleton Super Class

```C#

namespace HexaCoreVillage.Framework;

/// <summary>
/// # 싱글톤화를 위한 상속용 클래스
/// ## 사용 예제
/// ### public class Manager : Singleton<Manager>
/// ### 위와 같이 생성된 클래스는 싱글톤이 되며 Manager.Instance 사용 가능
/// </summary>
public class Singleton<T> where T : class, new()
{
    // 싱글톤화된 객체 인스턴스
    private static T? _instance;
    // 혹시 모를 비동기 작업을 위해 Lock 제약조건
    // 아예 무시하고 넘어가셔도 무방합니다.
    private static readonly object Lock = new object();

    #region Properties
    public static T Instance
    {
        get
        {
            lock(Lock)
            {
                if(_instance == null)
                    _instance = new T();

                return _instance;
            }
        }
    }
    #endregion

}
```

### <mark style="background: #FFB8EBA6;"></mark> SceneManager Class

```C#
/// <summary>
/// # Scene을 관리하는 매니저 클래스
/// ## Scene by Scene : 씬을 이동 시키는 매개 클래스
/// </summary>
public class Manager_Scene
{
    #region Member Variables

    private Scene? _currentScene = null;

    #endregion



    #region Properties (Getter)
    public Scene? GetCurrentScene() { return _currentScene; }
    #endregion

    

    #region Main Methods
    public void LoadScene(SCENE_NAME sceneKey)
    {
        // 추후 리소스 관리쪽으로 넘어갔을 때를 위한 처리 (아직은 기능 없음)
        if (_currentScene != null)
            _currentScene = null;

        // switch Lambda, _currentScene에 SCENE_NAME에 해당하는 SCENE으로 동적 할당
        _currentScene = sceneKey switch
        {
            SCENE_NAME.TITLE => new TitleScene(),
            SCENE_NAME.LOGIN => new LoginScene(),
            SCENE_NAME.LOBBY => new LobbyScene(),
            SCENE_NAME.STATUS => new StatusScene(),
            SCENE_NAME.STORE => new StoreScene(),
            SCENE_NAME.BATTLE => new BattleScene(),
            SCENE_NAME.REWARD => new RewardScene(),
            _ => throw new ArgumentException("Invalid [SceneKey]")
        };

        // Change the 'scene' and initalize(Start)
        _currentScene.Start();
    }
    #endregion
}

```

> 나중에 수정 시 참고해야 될 사항

	리소스를 절약할 수 있다. 씬을 버리지 않고 재활용 한다면!
		1. List<Scene> _sceneList
		2. Scene 추상 클래스에 abstract void Reset() 메서드 추가
		3. if(_currentScene != null) 부분에 _currentScene.Reset()을 해준다.
		4. switch Lambda식에서 new()를 통한 생성이 아닌 List[Index]로 꺼내온다.

* 이로써 간단한 Scene기반 프레임워크가 구현되었다.
* Main Procedure의 반복문은 currentScene의 업데이트만 진행된다.

---
## <mark style="background: #ADCCFFA6;">해보고자 하는 것</mark>

> [!닌텐도 1세대와 같이 4방향 움직이는 게임]
> - 필요 프레임워크 구상<br> 
> 	+ Scene은 foreach를 통해 항시 업데이트 및 렌더링이 되어야한다.
> 	+ **class** : GameNode → Scene → UserScene
> 	+ **class** : GameNode → GameObject (Player)<br>
> 	+ 캐릭터 이동 및 화면 그리기
> 		+ 더블버퍼링 기법 + 더티 플래그 기법을 합쳐서 구현
> 		+ 캐릭터의 입력을 받을 때 = FrontBuffer (그려지는 화면)
> 		+ 캐릭터의 입력을 받은 후 = BackBuffer (뒤에서 작업할 화면)