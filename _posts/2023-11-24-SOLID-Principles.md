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

**<span style="font-weight:bold; color:#00b0f0">객체 지향 (Object-Oriented)</span>**<br>
지금 나와 같은 학생의 신분으로는 두루뭉실하며 이해하기 어려운 개념이라고 생각한다.<br>
`Object-Oriented-Programming`의 핵심은 현실에 존재하는 또는 가상의 어떤 오브젝트를 `추상화` 하는 것이다.<br>

- **추상화의 일반적인 정의 및 개념**
	- 복잡한 자료, 모듈, 시스템 등 핵심적인 개념 또는 기능을 간추려 내는 것
	- 구체적인 사물(오브젝트)간 공통점을 취하고 차이점을 버리는 일반화(Generalization)이다.<br>

우리는 이 객체 지향 프로그래밍을 보다 잘 이해하기 위해 `SOLID Principles`라는 것을 학습할 것이다.

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
- 특정 기능을 바꿀 때, 변경에 대한 스코프가 최소화 되도록 설계하는 것
- 다시 말해 사칙 연산 기능을 하는 클래스의 수정은 사칙 연산 관련된 내용만 수정한다.
<br>

#### <span style="color:#c29956">OCP - 개방/폐쇄 원칙</span>
- 확장성에 대해서는 열려 있고, 변경성에 대해서는 닫혀 있는 것
- 기능을 확장할 시에 코드 변경을 최소화 할 수 있도록 한다.
<br>

#### <span style="color:#c29956">LSP - 리스코프 치환 원칙</span>
- 자식 클래스가 부모 클래스의 메서드를 오버라이딩 하거나 구현할 때 규약을 지켜야함.
- 이 내용은 추 후 코드 샘플을 보면서 같이 이해하는 것이 더 이해가 쉽다.
<br>

#### <span style="color:#c29956">ISP - 인터페이스 분리 원칙</span>
- 큰 역할에 인터페이스보다 작은 열할에 인터페이스를 여러개로 나눠 세분화 하는 것
- SRP(단일 책임 원칙)을 지키기 위해서 인터페이스 또한 하나의 책임만을 져야만 한다.

#### <span style="color:#c29956">✨DIP - 의존관계 역전 원칙</span>
- 다형성(Polymorphsim)에 해당하는 내용으로 다른 원칙도 필수이지만 제일 중요한 내용이다.
- `OCP`를 따르기 위해선 확장성에 대해 고려 해야하는데 이 부분이 `DIP`도 따라온다.
- OCP와 DIP를 구현을 위해 디자인 패턴은 아니지만 메커니즘으로 `의존성 주입`이 존재한다.