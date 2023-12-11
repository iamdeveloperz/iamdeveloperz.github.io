---
title: Unity Project - Arkanoid24, Multi Input
author: HuiSung
date: 2023-12-08
categories:
  - Bootcamp
  - TIL
tags:
  - Arkanoid24
  - MultiPlay
  - Local
  - MultiInput
image:
  path: /assets/img/ARKA_PROJ_TIL_MULTI.png
---

## 싱글 플레이 → 로컬 멀티 플레이로 확장

---

### 🎮<span style="color:#00b0f0">Player Input</span>

- 플레이어 인풋 시스템은 유니티에서 제공하는 `SO(Scriptable Object)` 패키지이다.
- 사용하면 할수록 기존 Legacy 방식보다 훨씬 효율적인 기능을 제공한다.
- `Player Input` 동작 방식은 다음 링크를 참고하면 된다. [InputSystem Example](https://iamdeveloperz.github.io/posts/Unity-Input-System-1-Mechanism/)<br><br>

- 유니티에서는 멀티 플랫폼 확장에 대한 편의성을 제공하기 위해서 `Player Input Manager`를 제공한다.
	![](https://i.imgur.com/FoHKuOb.png)
	- 화면 분할(Split Screen) 기능
	- Prefab을 이용한 플레이어 스포너(멀티 대응) 기능
- 기존에 InputSystem을 사용했기 때문에 프로젝트에서도 해당 기능을 사용하려고 한다.
- 그러면 `Input Actions`에 Scheme만 변경해주면 되기 때문이다.

---

### 🚨<span style="color:#00b0f0">컨트롤러 문제로 인한 `PlayerInputManager` 사용 불가</span>

- 위 기능은 어디까지나 다른 컨트롤러를 사용했을 때 자동으로 생성해주는 시스템이였다.
	- 즉, PC에서 사용되는 입력 장치 `Keyboard/Mouse` 두 개를 사용해야 두 플레이어를 생성해준다.
	- 이는 키보드 하나로 1P, 2P를 제어할려는 우리의 성격과는 맞지 않았다.<br><br>

	- 이걸 그대로 사용하는 법은 PlayerInput에 대한 `Maps Handle`을 `C#으로 커스터마이징` 하면 되는데,<br>
	  아직 이해도가 낮고 실력이 부족해서 그렇게 하기는 힘들어 끙끙 앓기만 했다.<br>
	  
	  ![](https://i.imgur.com/Kc9YIVd.png)

---

### 💡<span style="color:#00b0f0">Solved</span>

- `PlayerInputManager`와 같은 역할을 하는 클래스를 만들어 사용하면 되는 부분이다.<br><br>

	- #### <span style="color:#c29956">필요 기능</span>

		- 플레이어 스포너 (솔로와 멀티에 대응 될 수 있도록)
		- 카메라 스포너
			- 멀티일 경우 분할 (Screen Split)

```csharp

using System;
using System.Collections.Generic;
using UnityEngine;

public class PlayerManager
{
    #region Member Variables

    private List<GameObject> _players = new();

    private readonly Vector3 MainCameraSpawnPoint = new Vector3(0, 0, -CameraZOrder);
    private readonly Vector3 Player1CameraSpawnPoint = new Vector3(-CameraSplitAxisX, 0, -CameraZOrder);
    private readonly Vector3 Player2CameraSpawnPoint = new Vector3(CameraSplitAxisX, 0, -CameraZOrder);

    private readonly Vector3 PlayerPaddleSpawnPoint = new Vector3(0, PaddleStartPosY, 0);
    private readonly Vector3 Player1PaddleSpawnPoint = new Vector3(-CameraSplitAxisX, PaddleStartPosY, 0);
    private readonly Vector3 Player2PaddleSpawnPoint = new Vector3(CameraSplitAxisX, PaddleStartPosY, 0);

    // Literals
    public const float CameraZOrder = 10;
    public const float CameraSplitAxisX = 10;

    public const float PaddleStartPosY = -4f;

    public const string PlayerPrefabName = "Paddle";
    public const string CameraPrefabName = "MainCamera";

    public const string TagPlayer = "Player";
    public const string TagPlayer1 = "Player1";
    public const string TagPlayer2 = "Player2";

    #endregion


    #region Player Spawner
    
    public void PlayerSpawn()
    {
        Action playerSpawn = (Managers.Game.Mode == GameMode.Versus) ?
            MultiPlayerSpawn : SoloPlayerSpawn;

        playerSpawn?.Invoke();
    }

    private void MultiPlayerSpawn()
    {
        var player1 = Managers.Resource.Instantiate(PlayerPrefabName);
        var player2 = Managers.Resource.Instantiate(PlayerPrefabName);

        player1.transform.position = Player1PaddleSpawnPoint;
        player2.transform.position = Player2PaddleSpawnPoint;

        player1.tag = TagPlayer1;
        player2.tag = TagPlayer2;

        MultiPlayerSpriteSetting(player1, player2);

        _players.Add(player1);
        _players.Add(player2);
    }

    private void MultiPlayerSpriteSetting(GameObject player1, GameObject player2)
    {
        var p1SpriteRenderer = player1.GetComponentInChildren<SpriteRenderer>();
        var p2SpriteRenderer = player2.GetComponentInChildren<SpriteRenderer>();

        p1SpriteRenderer.material = Managers.Resource.GetMaterial("BlueGlow_Paddle");
        p2SpriteRenderer.material = Managers.Resource.GetMaterial("RedGlow_Paddle");
    }

    private void SoloPlayerSpawn()
    {
        var player = Managers.Resource.Instantiate(PlayerPrefabName);

        player.transform.position = PlayerPaddleSpawnPoint;

        player.tag = TagPlayer;

        _players.Add(player);
    }

    #endregion



    #region Camera Spawner
    public void CameraSpawn()
    {
        Action cameraSpawn = (Managers.Game.Mode == GameMode.Versus) ?
            MultiCameraSpawn : SoloCameraSpawn;

        cameraSpawn?.Invoke();
    }

    private void MultiCameraSpawn()
    {
        var player1CamPrefab = Managers.Resource.Instantiate(CameraPrefabName);
        var player2CamPrefab = Managers.Resource.Instantiate(CameraPrefabName);

        player1CamPrefab.transform.position = Player1CameraSpawnPoint;
        player2CamPrefab.transform.position = Player2CameraSpawnPoint;

        // Audio Listener Remove
        AudioListener audioListener2 = player2CamPrefab.GetComponent<AudioListener>();
        if(audioListener2 != null)
            GameObject.Destroy(audioListener2);

        var player1Camera = player1CamPrefab.GetComponent<Camera>();
        var player2Camera = player2CamPrefab.GetComponent<Camera>();

        Rect camera1ViewRect = new Rect(0, 0, 0.5f, 1);
        Rect camera2ViewRect = new Rect(0.5f, 0, 0.5f, 1);

        player1Camera.rect = camera1ViewRect;
        player2Camera.rect = camera2ViewRect;
    }

    private void SoloCameraSpawn()
    {
        var mainCamera = Managers.Resource.Instantiate(CameraPrefabName);

        mainCamera.transform.position = MainCameraSpawnPoint;

        mainCamera.AddComponent<CameraSetter>();
    }
    #endregion


    #region Utilites
    // 현재 활성화된 플레이어(들)을 반환하는 메서드
    public List<GameObject> GetActivePlayers()
    {
        // 멀티플레이 모드일 경우
        if (Managers.Game.Mode == GameMode.Versus)
        {
            return _players;
        }
        // 솔로 플레이 모드일 경우
        else
        {
            // 리스트에 하나의 플레이어만 포함되어 있는지 확인
            if (_players.Count > 0)
            {
                // 첫 번째 플레이어만 반환
                return new List<GameObject> { _players[0] };
            }
            else
            {
                // 플레이어가 없을 경우, 빈 리스트 반환
                return new List<GameObject>();
            }
        }
    }

    public void ReleasePlayerObject()
    {
        _players.Clear();
    }
    #endregion
}

```

- 이로써 기본적인 `PlayerInputManager`를 모방한 컴포넌트를 만들었다.
- 선택하는 모드에 따라서 플레이어와 카메라를 알맞게 자동으로 생성해준다.

![](https://i.imgur.com/iUglJrF.gif)


---

## 여전히 존재하는 문제

- `PlayerManager.cs`를 만듦으로써 생성과 분할은 제대로 적용될 수 있게 됐다.
- 하지만 여전히 사용자로부터 입력을 받는 `PlayerInput` 부분에 대한 처리를 하지 않았다.
- 그리고 멀티 플레이어일 경우 각 플레이어에 대응되는 무언가 즉, 소유권을 알아야만 한다.
	- 기존에 벽돌과 공을 그대로 사용하기 위해서 필요

<br>

1. `플레이어 입력 처리`
	1. 멀티 플레이어일 때 - 키보드(W,A,D / Left,Right,Up) 입력
	2. 솔로 플레이어일 때 - 마우스(이동, LeftButton) 입력
2. `스킬이 개별적으로 처리 되기 위한 공 소유권 처리`
	1. 멀티 플레이어일 때 - 각 플레이어에 맞게 `Ball`을 소유권을 부여해야함
	2. 솔로 플레이어일 때 - 그대로 사용하면 됌.