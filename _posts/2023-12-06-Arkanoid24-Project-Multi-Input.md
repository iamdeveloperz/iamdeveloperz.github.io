---
title: Unity Project - Arkanoid24, Multi-Input
author: HuiSung
date: 2023-12-06
categories:
  - Bootcamp
  - TIL
tags:
  - Arkanoid24
  - MultiPlay
  - BallManager
  - PlayerMapping
image:
  path: /assets/img/ARKA_PROJ_TIL_INPUT.png
---

- 싱글 플레이 기반에서 로컬 멀티플레이 기반으로 확장하면서 많은 어려움을 겪었다.
- 지난 시간에 스포너를 통해서 싱글, 멀티에 따라 자동 생성해주는 매니저를 만들었지만,<br>
  플레이어에 대한 입력 처리와 공에 소유권 처리를 하지 않았기 때문에 동작하지 않는다.

## 플레이어 입력 처리

---

### <span style="color:#00b0f0">Action Maps
</span>

![](https://i.imgur.com/BqqVxYQ.png)

- `Action Maps`는 해당 컨트롤러에 대한 입력이 필요한 오브젝트들을 나눈다고 생각하면 된다.
- `키보드/마우스` 스키마를 사용하고 있었기 때문에 Scheme은 그대로 놔두고,<br>
  SoloPaddle, MultiPaddle1/2를 추가하여 스위칭 할 수 있는 기반을 만들어 두었다.
- `PaddleController.cs`에서 스위칭 해주는 처리만 해주면 플레이어 입력도 솔로/멀티 대응이 된다.

---

### <span style="color:#00b0f0">PaddleController.cs</span>

```csharp
public class PaddleController : MonoBehaviour
{
    #region Events

    public event Action<Vector2> OnMoveEvent;
    public event Action OnFireEvent;

    #endregion



    #region STATE
    public enum PADDLE_STATE
    {
        READY,
        MOVEMENT
    }
    #endregion



    #region Properties
    public PADDLE_STATE PaddleState { get; set; } = PADDLE_STATE.READY;
    #endregion



    #region Member Variables

    // Literals
    public const string SoloMaps = "SoloPaddle";
    public const string MultiMaps1 = "MultiPaddle1";
    public const string MultiMaps2 = "MultiPaddle2";

    // Input Field
    protected PlayerInput _playerInput;

    #endregion



    #region Unity Flow
    protected virtual void Awake()
    {
        // Get Component
        _playerInput = GetComponent<PlayerInput>();

        // Service Register
        ServiceLocator.RegisterService(this);
    }

    protected virtual void Start()
    {
        PaddleMapsSetting();
    }
    #endregion



    #region Swtich Action Maps
    private void PaddleMapsSetting()
    {
        Action paddleMaps = (Managers.Game.Mode == GameMode.Versus) ?
            EnableMultiPaddle : EnableSoloPaddle;

        paddleMaps?.Invoke();
    }

    public void EnableSoloPaddle()
    {
        _playerInput.SwitchCurrentActionMap(SoloMaps);
    }

    public void EnableMultiPaddle()
    {
        if (gameObject.CompareTag(PlayerManager.TagPlayer1))
            _playerInput.SwitchCurrentActionMap(MultiMaps1);
        else if (gameObject.CompareTag(PlayerManager.TagPlayer2))
            _playerInput.SwitchCurrentActionMap(MultiMaps2);
    }
    #endregion



    #region Call Event
    public void CallMoveEvent(Vector2 axis)
    {
        OnMoveEvent?.Invoke(axis);
    }

    public void CallFireEvent()
    {
        OnFireEvent?.Invoke();
    }
    #endregion
}
```

- `region 'Switch Action Maps'` 부분을 보면은 솔로와 멀티 플레이에 대한 대응을 따로 해주었다.
- 만약 게임 모드가 `Versus`일 경우엔 멀티 플레이 모드로서 태그를 검사하고 해당 Maps를 대응시켜준다.
- 멀티가 아닐 경우 즉, 솔로 모드일 경우 조건문 필요 없이 스위칭해주면 끝이다.
- 스위칭을 통해 패들이 정상적으로 동작하는 것을 알 수 있다.
	- 마우스 - Solo Mode
	- 키보드 - Multi Mode

## 볼에 대한 플레이어 소유권

- 이제 볼이 어떤 플레이어에 것인지에 대한 소유권을 확인해야만 한다.
- 아이템과 스킬을 적용 받기 위해서는 볼의 소유권자가 명확해야 하기 때문이다.
- 볼 매니저를 만들어 플레이어와 볼을 맵핑 시키는 기능을 수행하게 한다.

---

### <span style="color:#00b0f0">BallManager.cs</span>

```csharp
public class BallManager
{
    private Dictionary<GameObject, List<GameObject>> _playerBallMap =
        new Dictionary<GameObject, List<GameObject>>();


    public void AssignBallToPlayer(GameObject player, GameObject ball)
    {
        // 해당 플레이어에 대한 볼 리스트가 없다면?
        if (!_playerBallMap.ContainsKey(player))
        {
            _playerBallMap[player] = new List<GameObject>();
        }

        // 볼 리스트에 추가
        _playerBallMap[player].Add(ball);
    }

    public List<GameObject> GetBallsForPlayer(GameObject player)
    {
        if(_playerBallMap.TryGetValue(player, out List<GameObject> balls))
        {
            return balls;
        }

        return new List<GameObject>(); // 없을 경우 빈 오브젝트 리스트를 반환
    }

    // 모든 활성화된 플레이어의 모든 볼들을 리스트로 반환
    public List<GameObject> GetAllBalls()
    {
        List<GameObject> allBalls = new List<GameObject>();

        foreach (var playerBalls in _playerBallMap.Values)
        {
            allBalls.AddRange(playerBalls);
        }

        return allBalls;
    }

    public void RemovePlayer(GameObject player)
    {
        if(_playerBallMap.ContainsKey(player))
        {
            _playerBallMap.Remove(player);
        }
    }

    public void RemoveBallFromPlayer(GameObject player, GameObject ball)
    {
        if(_playerBallMap.ContainsKey(player) || player != null)
        {
            _playerBallMap[player].Remove(ball);
        }
    }

    public GameObject CreateBallForPlayer(GameObject player, Vector3? startPos = null)
    {
        var ballStartPos = startPos ?? new Vector3(player.transform.position.x, player.transform.position.y + 0.5f);
        var ball = Managers.Resource.Instantiate("BallPrefab", ballStartPos);

        var ballPreference = ball.GetComponent<BallPreference>();
        ballPreference.AssignPlayer(player);

        AssignBallToPlayer(player, ball);

        SetBallMaterial(player, ball);

        return ball;
    }

    public void CreateBalls()
    {
        foreach(var player in Managers.Player.GetActivePlayers())
        {
            CreateBallForPlayer(player);
        }
    }

    private void SetBallMaterial(GameObject player, GameObject ball)
    {
        var ballSpriteRenderer = ball.GetComponentInChildren<SpriteRenderer>();
        if (player.tag == PlayerManager.TagPlayer1)
        {
            ballSpriteRenderer.material = Managers.Resource.GetMaterial("BlueGlow_Ball");
        }
        else if (player.tag == PlayerManager.TagPlayer2)
        {
            ballSpriteRenderer.material = Managers.Resource.GetMaterial("RedGlow_Ball");
        }
        else
        {
            ballSpriteRenderer.material = Managers.Resource.GetMaterial("BlueGlow_Ball");
        }
    }

    public void ReleaseAll()
    {
        _playerBallMap.Clear();
    }
}
```

- `CreateBallForPlayer` 메서드를 통해서 볼을 생성한다.
- `AssginBallToPlayer` 메서드를 통해서 플레이어와 볼을 매핑시킨다.
- `BallPreference.cs`에서 `BallOwner`라는 프로퍼티를 사용해<br>
  해당 볼이 어떤 플레이어에 것인지 명확하게 소유권을 분배해서 확인할 수 있다.<br><br>

- 멀티 플레이와 솔로 플레이가 대응될 수 있게 활성화된 플레이어만큼 공을 생성한다.
	```csharp
	public void CreateBalls()
    {
        foreach(var player in Managers.Player.GetActivePlayers())
        {
            CreateBallForPlayer(player);
        }
    }
	```

---

### 🐛Bug Fixed

- 아이템을 사용할 때 소유권자를 명시 했음에도 스킬이 중복 적용되던 버그
	- 해당 버그는 `Item`스킬 관련 코드를 작성할 때 아마 매니저를 사용해서 그런 것 같다.
	- 따로 `lock`을 걸어 적용하던 것도 아니였고, 스킬 스테이트가 단 하나만 존재 했기 때문에 발생

> <해결><br>
> 기존 매니저(싱글톤) 형태로 사용하던 스킬 시스템을 각 공에게 배분<br>
> `BallPreference`에 `public BallSkilLState BallSkill`로 선언하여 사용했다.<br>
> 이제 공은 개별적으로 스킬을 들고 있기 때문에 기존에 중복 적용되던 치명적인 버그가 해결 되었다.
{: .prompt-info}