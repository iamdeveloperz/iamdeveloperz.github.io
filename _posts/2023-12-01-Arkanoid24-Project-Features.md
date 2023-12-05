---
title: Unity Project - Arkanoid24, Features
author: HuiSung
date: 2023-12-01
categories:
  - Bootcamp
  - TIL
tags:
  - Arkanoid24
  - Features
  - InputSystem
image:
  path: /assets/img/ARKA_PROJ_TIL.png
---

## Feature : Player (Paddle)
- `Unity Input System`을 활용하여 플레이어(Paddle) 구현
- Behavior => `SendMessage` 방식으로 해당 메서드가 있는지 찾아 실행시키는 방식으로 진행
- 각자에 의존성을 낮추기 위해서 기능별로 SRP원칙을 지키며 모듈화 진행

| Script                   | Description                                                |
| ------------------------ | ---------------------------------------------------------- |
| PaddleEventController.cs | 플레이어 Movement, Fire 등을 이벤트로 발행해 제어하는 코드 |
| PaddleInputController.cs | 플레이어 입력(Keyboard, Mouse)을 받는 코드                 |
| PaddleMovement.cs        | 플레이어 이동(`Device : Mouse, Movement`)을 하는 메서드                         |
| PaddleFire.cs            | 플레이어가 공을 발사(초기상태)를 제어하기 위한 코드        |

---

### 🎮<span style="color:#00b0f0">Player Movement</span>
- 플레이어 이동을 마우스 포지션으로만 처리
	- 이 부분에서 살짝 문제가 있었는데, 마우스 포지션을 따라가게 만드니까,<br>
	  플레이어가 제어 되지 않을 정도로 너무 빠른 문제가 발생.
	- 기존 코드는 transform을 직접 제어해서 물리 연산에도 오차가 발생

 	> transform을 직접적으로 건드려 포지션을 변경해주던 부분을 물리 연산으로 변경<br>
 	> Rigidbody2D => velocity에 direction값과 speed 값을 곱해주어 마우스를 따라가게 처리<br>
 	> `_rbody.velocity = direction * movementSpeed`
 	{: .prompt-info }

```csharp
private void FixedUpdate()
{
	ApplyMovement();
}

private void ApplyMovement()
{
	float distancePaddleToMove = _movementAxis.x - transform.position.x;
	
	float moveSpeed = distancePaddleToMove * _movementSpeed;

	_rbody.velocity = new Vector2(moveSpeed, 0);
}
```

---

### Event Manager
- `Remove` => PaddleEventController.cs 
- `Add` => EventManager.cs
- 기존 패들 이벤트 컨트롤러에 존재하던 발행하는 이벤트들을 중앙 관리를 위해 이벤트 매니저 생성
- 이벤트 매니저는 통합적으로 모든 이벤트를 관리하고 퍼블리시(발행) 해주는 역할을 함

```csharp
  
using System;  
using UnityEngine;  
  
public class EventManager  
{  
    #region Event Actions  
  
    /* Paddle */  
    public event Action<Vector2> OnMoveEvent;  
    public event Action OnFireEvent;  
  
    /* Ball */  
    public event Action OnBallLaunch;  
  
    #endregion  
  
  
    #region Publish Paddle    
    public void PublishMoveEvent(Vector2 axis)  
    {  
        OnMoveEvent?.Invoke(axis);  
    }  
  
    public void PublishFireEvent()  
    {  
        OnFireEvent?.Invoke();  
    }  
    #endregion  
  
  
    #region Publish Ball    
    public void PublishBallLaunch()  
    {  
        OnBallLaunch?.Invoke();  
    }  
    #endregion  
}
```

- 구독과 발행을 중앙 집중적으로 관리하기 때문에 사용성에 유리함. `(어디서든 접근)`
- 하지만 이벤트 특성상 코드가 복잡해지는 경향이 있는데 이 부분을 항상 유의해야함.
- 현재는 작은 규모의 이벤트만을 관리해서 따로 생성해도 문제 없지만,<br>
  추 후 다른 프로젝트나 현재 프로젝트 범위가 커지면 이벤트 매니저는 사용을 지양해야함
- 이벤트 중앙 관리는 의존성을 최소화 할 수 있지만, 코드의 복잡성을 증가시키기 때문에 사용에 신경쓰자!