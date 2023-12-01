---
title: Unity - InputSystem(1), Mechanism
author: HuiSung
date: 2023-11-29
categories:
  - Unity
  - Learn
  - Package
tags:
  - InputSystem
  - Event
image:
  path: /assets/img/Unity_InputSystem.png
---

- 유니티 인풋 시스템은 `Version 19.1`부터 릴리즈하여 제공된 새로운 입력 시스템이다.
- 기존 Legacy 방식에는 사용자 친화적이지 않고 간단한 작업도 원할하게 처리하지 못한다고 판단하고<br>
  유니티에서 새로 구축하여 제공한 시스템이다.
- `액션(Action)` 중심으로 게임 코드와 상호작용하는 형태로 진행되어 모듈화에 큰 이점이 있다.<br><br>

- 예제 및 자세한 내용은 아래 <span style="color:#7030a0">깃허브 링크</span>를 참조 하면 된다.
	- [Input System Example, GitHub](https://github.com/iamdeveloperz/LEARN-UNITY/tree/InputSystem)

---

## Introduction : 설치 및 사용법

### ⬇️Install & Import

- 유니티 패키지 매니저에서 `Package: Unity Registry`로 변경 후  `input`으로 검색하면 찾을 수 있다.
- `Input System` Install 후 프로젝트의 Import 해주면 된다.

	![](https://i.imgur.com/Qc4EFES.png)

---

### 📰Manual

- `Import`를 끝내고 나면 해당 프로젝트의 `Input System` 패키지 설치가 완료된 것이다.
	1. `Project` 뷰에서 우클릭 → Create → <span style="color:#ffc000">Input Actions</span>
	2. 위 절차대로 행해 만든 `Input Actions`은 SO(ScriptableObject)의 형태를 지닌다.
	3. 더블 클릭하여 에셋 편집 메뉴에 들어가면 다음과 같은 이미지가 보여진다.

		![](https://i.imgur.com/v3jxVJv.png)

- #### <span style="color:#c29956">입력기기에 대응, 스키마 만들기</span>

	- `Add Control Scheme` 버튼을 눌러 `특정 키 입력`에 대응하는 스키마를 만들어준다.
	- 스키마는 키보드+마우스 또는 게임패드+마우스 같은 특정 입력 도구의 집합이다.
	- Action에 바인딩된 키를 다르게 할 수 있고, 스키마가 달라도 ActionMap과 Action은 같다.


		![](https://i.imgur.com/IZ93E9j.png)

	- 내가 사용할 입력기기에 관련된 것으로 스키마 이름을 지어준다.
		- <span style="color:#92d050">예시)</span> 키보드와 마우스를 사용한 입력 - KeyMa
		- `List is Empty` 메뉴에서 `+`버튼을 눌러 키보드와 마우스를 추가한다.<br><br>

- #### <span style="color:#c29956">액션 맵 및 액션만들기</span><br><br>

	![](https://i.imgur.com/BwD4gyS.png)

	- `Action Maps` - UI 키 입력 방식, 플레이어 입력 방식이 다를 경우, 이런 상황에 쓰인다.
	- `Actions` - 실제 액션이 될 이름 할당 키 값이라고 생각하면 된다.<br><br>
	- 이렇게 만든 `Input Action`은 `PlayerInput`이라는 컴포넌트를 붙여 사용할 수 있다.<br><br>

- #### 전달 방식 설정 (Behavior)<br><br>
	![](https://i.imgur.com/ZBG06NQ.png)

	- ##### **<span style="font-weight:bold; color:#7030a0">SendMessage</span>**
		- **특정 키 입력을 받았을 때, 특정 함수를 콜백하는 방식**
		- 콜백 함수명은 이미지에서 보다시피 `On + Actions Name`으로 구성된다.
		- `Actions`에 현재 Movement, Aiming, MouseActivity가 존재하는데 Behavior 밑에 보면 콜백할<br>
		  함수명이`On+ActionName`으로 제시 되어 있는 것을 확인할 수 있다.

		```csharp
		using UnityEngine;
		using UnityEngine.InputSystem;
		
		public class BehaviorSendMessage : MonoBehaviour
		{
		    private void OnMovement(InputValue inputValue)
		    {
		        Debug.Log("WASD, 방향키로 조작하면 동작");
		    }
		
		    private void OnAiming(InputValue inputValue)
		    {
		        Debug.Log("마우스가 이동하면 동작");
		    }
		
		    private void OnMouseActivity(InputValue inputValue)
		    {
		        Debug.Log("마우스 좌클릭 우클릭 Pressed, Released");
		    }
		}	
		```

		![](https://i.imgur.com/9Ca3JHm.png)

		- `SendMessages` - Player Input 컴포넌트가 존재하는 게임 오브젝트 안에서만 콜백 함수를 찾음.
		- `BroadcastMessages` - 컴포넌트 하위에 존재하는 자식들도 검사해서 콜백 함수를 찾음.
		- 메시지 방식은 전달 방식의 차이는 없고 어디까지 찾느냐의 스코프(범위) 차이가 있다.<br><br>

	- ##### **<span style="font-weight:bold; color:#7030a0">Invoke Unity Events</span>**
		- **Unity에서 제공하는 Event 방식을 사용한다.**
		- 해당 Actions에 대한 키 입력이 들어올 경우 설정한 이벤트 함수를 호출한다.
		- 다음 스크립트를 작성하고 오브젝트에 붙인 후 Event를 설정 해준다.

		```csharp
		
		using UnityEngine;
		using UnityEngine.InputSystem;
		
		public class BehaviorInvokeUnityEvents : MonoBehaviour
		{
		    public void OnMovement(InputAction.CallbackContext context)
		    {
		        Vector2 inputValue = context.ReadValue<Vector2>();
		        if(inputValue != null)
		        {
		            var moveDirection = new Vector3(inputValue.x, 0f, inputValue.y).normalized;
		            Debug.Log($"Unity Events Movement : {moveDirection}");
		        }
		    }
		
		    public void OnAiming(InputAction.CallbackContext context)
		    {
		        Vector2 mousePos = context.ReadValue<Vector2>();
		        Debug.Log($"Unity Events Aiming : {mousePos}");
		    }
		
		    public void OnMouseActivity(InputAction.CallbackContext context)
		    {
		        if (context.control.name == "leftButton")
		        {
		            if (context.performed)
		                Debug.Log("Left Mouse Button Pressed");
		            else if (context.canceled)
		                Debug.Log("Left Mouse Button Released");
		        }
		        else if (context.control.name == "rightButton")
		        {
		            if (context.performed)
		                Debug.Log("Right Mouse Button Pressed");
		            else if (context.canceled)
		                Debug.Log("Right Mouse Button Released");
		        }
		    }
		}
		```

	- `이벤트 콜백 함수 등록`
		![](https://i.imgur.com/gi9xAKh.gif)

	- `플레이 모드`
		![](https://i.imgur.com/hu1quYJ.gif)

	- Send Message 방식과 달리 `On + Action Name` 규칙을 이행할 필요가 없다.
	- 하지만 `이벤트 함수 네이밍 규칙`에 의거해 `On`을 적용해주었다.
	- 메서드의 접근 지정자는 Button과 같이 `public`으로 지정 해주어야만 등록이 가능하다.

---

## 정리 & 요약<br><br>

### 설치
- `Package Manager` -> `Unity Registry`에서 `Input System`을 인스톨 및 임포트한다.
- 프로젝트 뷰에서 `우클릭` -> `Create` -> `Input Actions`를 만든다.

### 액션맵
- `Schema` - 플랫폼에 대응 될 컨트롤러들 ( Key&Mouse , GamePad , Joystick ... )
- `Action Map` - Player, UI와 같이 입력 동작 방식이 다른 오브젝트에 대응
- `Actions` - Map에 매핑될 입력 바인딩들 (LeftButton, W, A .... 등등)

### 전달 방식 - Behavior
- **<span style="font-weight:bold; color:#7030a0">SendMessage</span>**
	- `SendMessage` - 특정 키가 입력됐을 때 명시된 특정 메서드 호출
		- `Player Input` 컴포넌트를 지닌 오브젝트 안에서만 특정 메서드 검색
	- `BroadcastMessage` - SendMessage 방식과 같지만 하위 오브젝트도 검색하여 메서드 호출
- **<span style="font-weight:bold; color:#7030a0">Invoke Event</span>**
	- Button과 같이 이벤트를 구독 시켜줘 해당하는 메서드를 호출하게 만드는 방식
	- SendMessage처럼 명시된 메서드 네이밍을 사용할 필요가 없다.
	- `InputAction.CallbackContext`를 파라미터로 사용

> 인풋 시스템이 어떻게 돌아가는지 동작 방식(Mechanism)에 대해서 살펴 보았다.<br>
> 두 번째 포스팅에선 인풋 시스템을 사용해 플레이어 이동 스크립트를 작성해보도록 할 것이다.
{: .prompt-info }