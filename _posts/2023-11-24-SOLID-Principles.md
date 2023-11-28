---
title: OOP Basic - S.O.L.I.D Principles
author: HuiSung
date: 2023-11-24
categories:
  - OOP
  - Design
tags:
  - SOLID
  - Principles
image:
  path: /assets/img/SOLID_Principles.png
pin: true
---

객체 지향의 특징 및 개념을 설명 하기 어렵다면 [`OOP Feature & Keyword`](https://iamdeveloperz.github.io/posts/OOP-Features-Keyword)을 읽고 오길 바란다.<br><br>

우리는 객체 지향을 더 깊이 있게 이해하고 이를 잘 활용 하기 위해서<BR>  `S.O.L.I.D Principles`(솔리드 원칙)이라는 것을 학습할 것이다.

---

## S.O.L.I.D Principles

### <mark style="background: #FFB8EBA6;"></mark>&nbsp;✅객체 지향 설계의 원칙

<span style="color:#ffc000">**S.O.L.I.D**(일명: 솔리드) 원칙이란 객체 지향 설계`Object-Oriented-Design`에서 지켜야할 원칙이다.</span>
<br>
이 솔리드 원칙을 어느정도 이해만 해도 코드를 작성하기 전 설계가 꼭 필요하단 것을 알게될 것이다.<br><br>
무엇보다 솔리드 원칙을 이해하고 지킬려고 노력하면 `코드 스킬`이나 `설계 능력`이 알아서 향상된다.<br>


| Title                                                                                          | Description                                                                                                  |
|:---------------------------------------------------------------------------------------------- |:------------------------------------------------------------------------------------------------------------ |
| **<span style="font-weight:bold; color:#00b0f0">S</span>**RP (Single Responsibility Principle) | 하나의 클래스는 하나의 책임만을 가져야 한다.                                                                 |
| **<span style="font-weight:bold; color:#00b0f0">O</span>**CP (Open/Closed Principle)           | 소프트웨어 확장은 열려 있지만 변경에 대해서는 닫혀 있어야한다.                                                 |
| **<span style="font-weight:bold; color:#00b0f0">L</span>**SP (Liskov Substitution Principle)   | 객체는 프로그램 정확성을 깨지 않고<br> `하위 타입` 인스턴스로 바꿀 수 있어야 한다.                           |
| **<span style="font-weight:bold; color:#00b0f0">I</span>**SP (Interface Segregation Principle) | 특정 클라이언트를 위한 인터페이스 여러 개가<br>범용 인터페이스 하나 보다 낫다.                               |
| **<span style="font-weight:bold; color:#00b0f0">D</span>**IP (Dependancy Inversion Principle)  | 프로그래머는 `추상화`에 의존. 구체화에 의존해서는 안된다.<br> 관련된 내용으로는 **의존성 주입** 설계가 있다. |

> **<span style="font-weight:bold; color:#ffc000">왜 이 내용을 꼭 알아야 할까?</span>** <br>
> 
>  ---
>  
> 솔리드 원칙은 객체 지향에 기본으로써 GoF에서 고안한 `디자인 패턴(DesignPattern)`같은 것 또한<br>
> SOLID 설계 원칙을 준수하며 만들어진 것이기 때문에 사용할려면 완벽히 이해하고 넘어가야한다.
{: .prompt-info }

---

### <mark style="background: #FFB8EBA6;"></mark>&nbsp;좋은 프로그램을 만들기 위해

`좋은 프로그램`이란 클라이언트가 요청하는 서비스를 보다 쉽게 제공해줄 수 있어야 한다.<br>
시스템에 새로운 요구사항 또는 변경사항이 발생했을 때 다른 객체들에 영향이 적어야 한다.<br>
<br>
SOLID 객체 지향 원칙을 준수하여 코드를 작성한다면 `유지보수/확장성`을 챙길 수 있다.<br>
무엇보다 불필요한 코드에 대한 `복잡성을 제거`해 리팩토링 소요시간을 줄이는데,<br>
이는 곧 개발 소요시간에 직접적인 영향을 끼친다. (개발의 생산성)
<br><br>
즉, 좋은 프로그램은 개발자가 예기치 못한 변경사항에 대해 유연하게 대처할 수 있고<br>
이 후 클라이언트 요구에 따른 확장에도 어려움이 없는 소프트웨어를 말한다.
<br><br>
> 🏷️ 솔리드 원칙은 어떠한 라이브러리도 프레임워크도 아니다.
><br>또한 디자인 패턴과 같은 것도 아니다. 특정 기술에 국한되는 개념이 아니라는 것이다.
><br> 즉, 어떤 프로그래밍 언어/프레임워크에서도 적용할 수 있는 설계 원칙이다.

---

### <mark style="background: #BBFABBA6;"></mark>&nbsp;S.O.L.I.D 원칙 5가지 간단 정리
솔리드 원칙은 앞서 설명했던 내용들 그대로 객체-지향-설계에 있어서 매우 중요한 개념이다.<br>
그래서 각 원칙별로 따로 포스팅을 할 예정인데, 그 전에 5가지가 어떤 원칙인지 간략하게 알아보자!

#### <span style="color:#c29956">✨SRP - 단일 책임 원칙</span>
- <span style="color:#00b0f0">클래스를 변경해야 하는 이유는 단 하나여야 한다.</span>
- 모든 코드 모듈(클래스, 함수 등)이 소프트웨어 기능에서 유일한 목적을 가져야 한다.
- 컴포넌트 기반 유니티에서는 모든 것을 관리하는 하나의 컴포넌트가 아닌,<br>
  특정 기능을 지니는 여러 컴포넌트를 갖는 것이 선호되며 그룹화 하여 전체 객체를 형성한다.
- 즉, Player.cs / Enemy.cs같이 통합된 것이 아닌 `PlayerInput.cs` `WeaponHandler.cs`로 나눠야한다.
<br>

#### <span style="color:#c29956">OCP - 개방/폐쇄 원칙</span>
- <span style="color:#00b0f0">소프트웨어 엔티티(클래스, 모듈, 기능 등)은 확장을 위해서 열려야하지만,<br>
  반대로 수정(변경)을 위해서는 폐쇄적으로 닫혀 있어야 한다.</span>
- 즉 확장을 위해 새로운 필드나 요소를 모듈에 추가하지만 이를 사용하는 다른 모듈에서<br>
  구현을 변경할 필요가 없을 때 이 원칙이 적용된다.
- `Fire()`가 있는 `IWeapon` 인터페이스를 구현하는 객체 `Gun`에 대한 참조를<br> 지니고 있는 `WeaponHandler.cpp`가 있다고 가정.
- 해당 `Fire 메서드`에서 무슨 일이 발생하든 이를 구현하는 클래스가 변경되든<br>
  `WeaponHandler.cpp`는 그대로이며 변경되어서는 안된다. 그대로 `Fire()`를 호출 할 수 있어야한다.

```cpp
class IWeapon {
	virtual void Fire() = 0;
	virtual ~IWeapon() { }
};

class Gun : public IWeapon
{
public:
	void Fire() override
	{
		std::cout << "총을 쐈습니다." << std::endl;
		// Fire 메서드 변경
	}
};

class WeaponHandler
{
private:
	IWeapon* playerWeapon;

public:
	void Attack()
	{
		// 변경 내용을 알 필요없이 Fire만 쓰면 된다.
		playerWeapon->Fire();
	}
};
```

#### <span style="color:#c29956">LSP - 리스코프 치환 원칙</span>
- <span style="color:#00b0f0">하위 유형의 인스턴스는 상위 클래스 유형의 인스턴스를 대체할 수 있어야한다.</span>
- 다른 클래스에서 상속받은 클래스는 결과를 변경하지 않고 기본 클래스처럼 사용 가능해야한다.
- `Reaction()`가 있는 `NPCAI` 클래스가 있다고 가정, 거기에 `EnemyAI`와 `CitizenAI`를 만든다.<br>
- 둘 다 `NPCAI`에서 상속되지만 `Reation 메서드`의 구현은 다르다.<br>
  결국 플레이어가 이들과 충돌하면 `NPCAI->Reaction`을 호출 하지만<br>
  반응하는 개체가 적인지 시민인지에 따라서 결과가 달라지는 것이다.


```cpp
class NPCAI
{
public:
	virtual void Reaction() = 0;
};

class EnemyAI : public NPCAI
{
public:
	void Reaction() override
	{
		std::cout << "당신은 적입니다. 사살합니다." << std::endl;
	}	
};

class CitizenAI : public NPCAI
{
public:
	void Reaction() override
	{
		std::cout << "반갑습니다. 플레이어님" << std::endl;
	}
};

void ProcessReaction(NPCAI* npc)
{
	npc->Reaction();
}

int main()
{
	Player* player = new Player();
	std::vector<NPCAI*> npcs;
	for(int i = 0; i < 4; ++i)
	{
		NPCAI* npc;
		if(i % 2 ==0)
			npc = new CitizenAI();
		else
			npc = new EnemyAI();
		npcs.push_back(npc);
	}

	for(int i = 0; i < npcs.size(); ++i)
		ProcessReaction(&npcs[i]);
}
```

#### <span style="color:#c29956">ISP - 인터페이스 분리 원칙</span>
- <span style="color:#00b0f0">클라이언트(사용자)가 사용하지 않는 인터페이스에 의존하도록 강요해선 안된다.</span>
- 클래스가 인터페이스를 구현해야하는 경우 선언해야 하는 모든 함수를 활용,<br>
  모든 클래스에 필요하지 않은 함수가 있는 경우 인터페이스를 두 개 이상으로 나눠야함.
- **예시)**
	- `Attack(), Turn()`을 지니는 인터페이스 `IEnemy`
	- `Vampire, Zombie`클래스는 이를 가질 수 있지만 `Orc`는 Turn이 필요 없다.
	- `Orc` 클래스는 `IEnemy`인터페이스를 구현하면 빈 함수가 생긴다. (원칙 위반)
	- `IEnemy`인터페이스는 `Attack()`만을 남기고 `ITurner`라는 인터페이스는 `Attack()`을 지니게한다.

```cpp
class IEnemy
{
public:
	virtual void Attack() = 0;
	virtual ~IEnemy() { }
};

class ITurner : public IEnemy
{
public:
	virtual void Turn() = 0;
	virtual ~ITurner() { }
};

class Vampire : public ITurner
{
public:
	void Attack() override;
	void Turn() override;
};

class Zombie : public ITurner
{
public:
	void Attack() override;
	void Turn() override;
};

class Orc : public IEnemy
{
public:
	void Attack() override;
};
```

#### <span style="color:#c29956">✨DIP - 의존관계 역전 원칙</span>
- <span style="color:#00b0f0">종속성 반전은 "높은 수준의 모듈은 낮은 수준의 모듈에 의존해서는 안된다."</span>
- 둘 다 `추상화`에 의존 해야 하는 것이 의존관계 역전 원칙이다.
- 추상화는 세부 사항에 의존하지 않고(즉, 구체화) 세부 사항은 추상화에 따라 달라진다.
- 관련된 내용으로 `의존성 주입`이라는 메커니즘이 있는데, DIP내용은 보다 복잡하고<br>
  헷갈리는 내용이 많으니 `DIP 포스팅`에서 추가적으로 더 다룰 예정이다.