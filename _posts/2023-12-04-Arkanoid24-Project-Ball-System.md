---
title: Unity Project - Arkanoid24, Ball System
author: HuiSung
date: 2023-12-04
categories:
  - Bootcamp
  - TIL
tags:
  - Arkanoid24
  - BallSystem
  - ServiceLocator
image:
  path: /assets/img/ARKA_PROJ_TIL.png
---

## 🏐Ball System
- 기존 공 시스템은 `Ball.cs`에서 충돌, 이동, 설정 등 모든 것이 이루어졌다.
- 이는 나중에 공 관련 사항을 수정할 때 아주 불편할 수 있다고 생각해 모듈화를 진행했다.

---

### <span style="color:#00b0f0">Ball Modularzation</span>

| ScriptName        | Description                                                                |
| ----------------- | -------------------------------------------------------------------------- |
| BallPreference.cs | Ball.cs `Base` - 볼 설정 관련 (이동속도, 소유자 등)                           |
| Ball.cs           | Preference를 상속 받음 실질적이 볼 상태(Ball State) 이동 로직 및 준비 로직 |
| BallCollision.cs  | 볼의 충돌 부분 (벽이나 플레이어 또는 외부 경계선)                          |


- #### <span style="color:#c29956">BallPreference.cs</span>

	```csharp
	
	using UnityEngine;
	
	public class BallPreference : MonoBehaviour
	{
	    public enum BALL_STATE
	    {
	        READY,
	        LAUNCH
	    }
	
	    #region Member Variables
	
	    [Header("Ball Information")]
	    [Range(4f, 6f)] public float MinSpeed = 4f;
	    [Range(14f, 16f)] public float MaxSpeed = 16f;
	    // Ball Start Default Speed
	    [Range(7f, 12f)] public float defaultSpeed = 7f;
	
	    [SerializeField] protected float _currentSpeed;
	
	    protected Rigidbody2D _paddleRbody;
	    protected Rigidbody2D _ballRbody;
	
	    // Game Pause
	    protected Vector2 _prevVelocity;
	    protected bool _isStarted = true;
	
	    public float ballIncreaseSpeedScope = 0.5f;
	
	    // Player Depend, Reference
	    protected GameObject _playerObject;
	
	    public BALL_STATE BallState { get; set; } = BALL_STATE.READY;
	    
	    // Getter
	    public GameObject BallOwner { get { return _playerObject; } }
	
	    public BallSkillState BallSkill;
	
	    #endregion
	
	
	    #region Unity Flow
	    protected virtual void Awake()
	    {
	        _ballRbody = GetComponent<Rigidbody2D>();
	    }
	
	    protected virtual void Start()
	    {
	        _currentSpeed = defaultSpeed;
	        BallSkill = BallOwner.GetComponent<BallSkillState>();
	    }
	
	    protected virtual void FixedUpdate()
	    {
	        if(Managers.Game.State != GameState.Play)
	        {
	            if (_isStarted)
	            {
	                _isStarted = false;
	                _prevVelocity = _ballRbody.velocity;
	            }
	
	            _ballRbody.velocity = Vector2.zero;
	            return;
	        }
	
	        if (!_isStarted)
	        {
	            _ballRbody.velocity = _prevVelocity;
	            _isStarted = true;
	        }
	    }
	    #endregion
	
	
	    #region Utility
	    public void AssignPlayer(GameObject player)
	    {
	        _playerObject = player;
	
	        GetPlayerComponent();
	    }
	
	    private void GetPlayerComponent()
	    {
	        _paddleRbody = _playerObject.GetComponent<Rigidbody2D>();
	    }
	
	    public void SetAdditionalCurrentSpeed(float additionalSpeed)
	    {
	        _currentSpeed = defaultSpeed + additionalSpeed;
	
	        if (_currentSpeed > MaxSpeed)
	            _currentSpeed = MaxSpeed;
	        else if(_currentSpeed < MinSpeed)
	            _currentSpeed = MinSpeed;
	    }
	
	    public void BallHitBrick()
	    {
	        BallSkill.SetSpeed(BallOwner);
	    }
	    #endregion
	}
	```

---

- #### <span style="color:#c29956">Ball.cs</span>

	```csharp
	using System;
	using UnityEngine;
	
	public class Ball : BallPreference
	{
	    #region Member Variables
	
	    public bool isCatchLaunch = false;
	
	    public int _defaultPower = 1;
	    public int _maxPower = 1;
	
	    public float _posX;
	
	    // Event
	    private event Action OnBallLaunch;
	
	    #endregion
	
	
	    #region Unity Flow
	    protected override void Awake()
	    {
	        // Get Component
	        base.Awake();
	    }
	
	    protected override void Start()
	    {
	        base.Start();
	        
	        OnBallLaunch += BallToStart;
	
	        SetAdditionalCurrentSpeed(BallOwner.GetComponent<BallSkillState>().BallExtraSpeed);
	        SetPower(BallSkill.BallExtraPower);
	    }
	
	    protected override void FixedUpdate()
	    {
	        base.FixedUpdate();
	
	        BallStateUpdateMethod();
	    }
	    #endregion
	
	
	    #region Ball State
	
	    private void BallStateUpdateMethod()
	    {
	        Action ballAction = BallState switch
	        {
	            BALL_STATE.READY => () => BallToReady(),
	            BALL_STATE.LAUNCH => () => BallToLaunch(),
	            _ => () => { }
	        };
	
	        ballAction();
	    }
	
	    private void BallToStart()
	    {
	        if (BallState != BALL_STATE.READY) return;
	        if(BallSkill.CurrentSkill == Items.Catch) isCatchLaunch = true;
	        BallState = BALL_STATE.LAUNCH;
	
	        CalculateBallPosToPaddle();
	    }
	
	    private void CalculateBallPosToPaddle()
	    {
	        var paddleWidth = _playerObject.GetComponent<BoxCollider2D>().bounds.size.x;
	        var posX = _ballRbody.position.x - _paddleRbody.position.x;
	
	        posX = posX / paddleWidth;
	        
	        var direction = new Vector2(posX, 1).normalized;
	
	        if (posX != 0)
	            _ballRbody.velocity = direction * _currentSpeed;
	        else
	            _ballRbody.velocity = Vector2.up * defaultSpeed;
	        isCatchLaunch = false;
	    }
	
	    private void BallToReady()
	    {
	        FollowThePaddle(_posX);
	    }
	
	    private void FollowThePaddle(float posX = 0f)
	    {
	        Vector2 paddlePos = _paddleRbody.position;
	        Vector2 newBallPos = new Vector2(paddlePos.x, paddlePos.y + 0.5f);
	
	        _ballRbody.position = newBallPos + new Vector2(posX, 0f);
	        _ballRbody.velocity = Vector2.zero;
	    }
	
	    private void BallToLaunch()
	    {
	        var ballVelocitySpeed = _ballRbody.velocity.magnitude;
	
	        if(ballVelocitySpeed > _currentSpeed || ballVelocitySpeed < _currentSpeed)
	        {
	            _ballRbody.velocity = _ballRbody.velocity.normalized * _currentSpeed;
	        }
	    }
	    #endregion
	
	
	    #region Item SKill
	
	    public void CheckCatchActivation()
	    {
	        if (BallSkill.CurrentSkill == Items.Catch)
	        {
	            BallState = BALL_STATE.READY;
	            _posX = transform.position.x - _paddleRbody.transform.position.x;
	        }
	    }
	
	    
	    public void SetPower(int extraPower)
	    {
	        _maxPower = _defaultPower + extraPower;
	        if(BallSkill.CurrentSkill == Items.Power)
	        {
	            transform.localScale = transform.localScale * 2f;
	        }
	    }
	
	    #endregion
	
	
	    public void CallBallLaunch()
	    {
	        OnBallLaunch?.Invoke();
	    }
	
	    private void OnDestroy()
	    {
	        OnBallLaunch -= BallToStart;
	    }
	}
	```

	- `Ball.cs`는 `BallPreference`를 상속 받아서 볼의 설정을 들고와 로직만 진행하게 변경했다.
	- 기본적인 볼 상태(State)와 설정(Speed, Owner)등은 전부 Preference에서 처리한다.
	- 이렇게 함으로써 `Ball.cs`는 스킬과 실질적인 동작(velocity) 부분만 이행하면 된다.

---

- #### BallCollision.cs

```csharp

using UnityEngine;
using static BallPreference;

public class BallCollision : MonoBehaviour
{
    private Ball _ball;
    private Rigidbody2D _ballRbody;

    private void Awake()
    {
        _ballRbody = GetComponent<Rigidbody2D>();
        _ball = GetComponent<Ball>();
    }


    #region Collision

    private void OnCollisionEnter2D(Collision2D col)
    {
        var activePlayer = Managers.Player.GetActivePlayers();

        foreach (var player in activePlayer)
        {
            if (col.gameObject == player && _ball.BallState != BALL_STATE.READY)
            {
                SFX.Instance.PlayOneShot(SFX.Instance.paddleHit);
                float paddleWidth = col.collider.bounds.size.x;

                _ball.CheckCatchActivation();
                if (player.GetComponent<BallSkillState>().CurrentSkill != Items.Catch)
                {
                    var posX = HitFactor(col.transform.position, paddleWidth);
                    var direction = new Vector2(posX, 1).normalized;

                    _ballRbody.velocity = _ballRbody.velocity.magnitude * direction;
                }
            }
            else if (col.gameObject.CompareTag("Brick"))
            {
                _ball.BallHitBrick();
            }
        }
    }

    #endregion


    #region Utility
    private float HitFactor(Vector2 paddlePos, float paddleWidth)
    {
        return (_ballRbody.position.x - paddlePos.x) / paddleWidth * 2f;
    }
#endregion
}
```

- 공의 충돌 부분에서는 실제 충돌이 일어났을 때 실행해야할 부분만 모듈화 해놨다.
- `플레이어 (각도를 위함)`와 `벽돌`부분으로 나누어 처리했다.

---

## 🛎️ServiceLocator

- 서비스 로케이터는 의존성 주입(Dependency Injection)를 중앙에서 처리해주는 시스템이다.
- 서비스를 등록 해놓고 어디서든 접근하여 해당 서비스를 이용할 수 있는 매니저 같은 시스템이다.

```csharp

using System.Collections.Generic;
using UnityEngine;

public static class ServiceLocator
{
    private static readonly Dictionary<System.Type, Object> _services =
        new Dictionary<System.Type, Object>();

    public static void RegisterService<T>(T service) where T : Object
    {
        _services[service.GetType()] = service;
    }

    public static T GetService<T>() where T : Object
    {
        if (_services.TryGetValue(typeof(T), out Object service))
        {
            return service as T;
        }
        return null;
    }
}
```

### <span style="color:#00b0f0">싱글톤 패턴과 서비스 로케이터</span>

- `Singleton`
	- 싱글톤 패턴은 클래스에 인스턴스가 하나만 있음을 보장하는 패턴이다.
	- `static`속성이나 method를 통해서 클래스 전역에 엑세스 지점을 제공한다.
	- 전역에서 엑세스가 가능한점은 간단한 사용을 제공하지만 `커플링`으로 이루어질 수 있다.
	- 전역 엑세스를 할 수 있는 이점이 단점으로도 작용하는데, 단위 테스트를 어렵게 만든다.<br><br>

- `Service Locator`
	- 서비스 로케이터는 서비스와 구성요소를 등록하고 필요할 때 검색해서 사용할 수 있다.
	- 싱글톤 패턴과 매우 유사하지만 결합도가 싱글톤과는 다르게 적용된다.
	- 클래스가 서로에 대한 참조를 직접 인스턴스화하거나 보유하지 않기 때문에,<br>
	  클래스간의 결합을 줄인다. 하지만 클래스 종속성을 숨길 수 있다.

---

## 정리하며...

#### <span style="color:#c29956">공 모듈화</span>
- 모듈화를 진행하면서 크게 어려웠던 부분은 존재하지 않았다.
- 그도 그럴 것이 기존 내용을 세분화 시키는 작업이었고 세분화 시킨 내용을 연결하는 것이 아닌<br>
  상속의 형태가 맞다고 판단하여 설정 값을 그대로 상속받는 형태로 사용 했기 때문이다.
- 그래도 설정, 볼 실제 액션, 충돌 부분을 따로 나눠 관리하니 다른 팀원이 내 코드에 접근할 때도 용이했다.


#### <span style="color:#c29956">서비스 로케이터</span>
- 서비스 로케이터 패턴은 기존 싱글톤 형태의 매니저들을 어느정도 대체할 수 있게 해주었다.
- 매니저들도 전역에서 엑세스할 수 있다는 점을 제외하면 `SRP(단일 책임 원칙)`을 준수해야한다.
- 서비스 로케이터를 사용해 어디서든 의존성을 사용할 수 있게 했다.
- 이는 `GameManager`와 같은 매니저에서 `Player` 같은 전역 정보를 다룰 필요가 없어졌다.