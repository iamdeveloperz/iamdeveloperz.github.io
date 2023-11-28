---
title: OOP Basic - Features/Keyword
author: HuiSung
date: 2023-11-27
categories:
  - OOP
  - Design
tags:
  - Features
  - Encapsulation
  - Abstraction
  - Inheritance
  - Polymorphism
  - Class
  - Instance
image:
  path: /assets/img/OOP_Features.png
pin: true
---

객체-지향-설계에 [`S.O.L.I.D Principles`](https://iamdeveloperz.github.io/posts/SOLID-Principles/)을 이해하기 위해서는 당연히 객체 지향 특징을 알아야한다.<br>

---

## <span style="color:#c29956">객체-지향이란? (Object-Oriented)</span>

어떤 사람이 객체 지향에 대해서 설명을 해달라고 한다면 설명을 할 수 있는가?<br>
이 부분이 내가 해당 개념을 완벽하게 이해하고 있는지에 대한 척도라고 할 수 있다.<br>

> 객체 지향 프로그래밍이 뭐에요?<br>

- 객체 지향 프로그래밍은 컴퓨터 프로그래밍 패러다임 중 하나입니다.
- 필요한 데이터를 `추상화` 시켜 상태(변수)와 기능(메서드)을 지닌 객체를 만들고,<br>
  이 객체들 간의 유기적인 상호작용을 통해 로직을 구성하는 프로그래밍 기법입니다.<br>

위와 같이 답변을 할 수 있다면 객체 지향 프로그래밍에 대해선 어느정도 이해를 하고 있다고 볼 수 있다.<br>
답변에 내용에서 핵심은 `추상화(abstract)`라는 단어이므로 절대 빼먹지 않길 바란다.

---

### <mark style="background: #FFB8EBA6;"></mark>&nbsp;🖥️객체 지향 프로그래밍의 장/단점

<br>

- **<span style="font-weight:bold; color:#92d050">장점</span>**
	- `코드 재사용`
		- 남이 만든 클래스를 가져와 그대로 재사용할 수 있다.
		- `상속`을 통해 해당 클래스의 내용을 확장하여 사용할 수 있다.
	- `쉬운 유지보수`
		- 절차적 프로그래밍에서는 코드를 수정해야할 때 일일이 찾아 수정해야한다.
		- OOP는 해당 기능을 담당하는 클래스 내부에 필드 또는 메서드만 수정하면 된다.
	- `대형 프로젝트에 적합`
		- 클래스 단위로 모듈화(Modularization)시켜 개발을 할 수 있다.
		- 이는 대형 프로젝트와 같은 여러명이 협업하는 환경에 업무 분담이 용이하다.

<br>

- **<span style="font-weight:bold; color:#92d050">단점</span>**
	- 처리속도가 절차적 프로그래밍보다 느리다. (상대적으로 느림)
	- 객체에 개수에 따라 용량이 매우 커질 수 있다.
	- 설계를 하는데에 많은 시간과 노력이 필요하다.

---


## <span style="color:#c29956">객체 지향 프로그래밍 키워드/특징</span>

### <mark style="background: #FFB8EBA6;"></mark>&nbsp;Keyword

- #### **Class**
	- 클래스는 일종의 메타데이터 현실로 비유하자면 청사진 또는 붕어빵 틀이라고 할 수 있다.
	- `추상화`를 거쳐 오브젝트에 해당하는 속성(Attribute)과 행동(Behavior)을<br>
	  `변수(Member Field)와 기능(Method)으로 정의 해놓은 것` 이라고 볼 수 있다.
	- 클래스는 실물이 존재하지 않는 설계도 이기 때문에 `인스턴스/객체`라고 표현하지 않는다.

<br>

- #### **Instance (Objects)**
	- 객체는 클래스를 이용해 만들어진 설계의 실체 또는 붕어빵이라고 할 수 있다.
	- 프로그래밍 관점으로 본다면 클래스에서 정의한 것을 토대로 실제 메모리에 할당된 것이다.
	- 인스턴스(객체)는 메모리에 상주하는 실체로서 우리가 흔히 아는 사물이라고 이해하면 된다.

<br>

> `Class` - 어떤 사물을 만들기 위한 설계도(청사진)<br>
> `Instance(Objects)` - 설계도(청사진)을 통해 만들어진 실제 사물 (실체)

---

### <mark style="background: #FFB8EBA6;"></mark>&nbsp;Features

- #### ✨**<span style="font-weight:bold; color:#c29956">Abstraction - 추상화</span>**
	- <span style="color:#00b0f0">추상화란 특정 사물(Object)에 대한 "공통적인 속성과 기능을 모아서 추출"하는 것이다.</span>
		- `데이터 추상화` - 객체의 관련된 속성만 **표시**
		- `제어 추상화` - 불필요한 세부 정보에 대해서는 **숨김**
	- 즉, 공통적인 속성과 기능들을 추출해 부모(슈퍼)클래스를 선정하고,<br>
	  이벤트 발생에 대한 로직을 정의하지 않고 대표할 수 있는 표현으로 대체 하는 것<br>
	  <br>

	- ##### 제어 추상화
		- <span style="color:#00b0f0">클래스의 메소드를 사용하는 사용자에게 내부 로직을 숨기는 것</span>
		- 예시를 들면서 설명하면 우리는 자동차를 운전할 때 `시동/정지` `가속/브레이크`에만 신경쓰면된다. <br>
		  실제로 `시작/정지`와 같은 메커니즘은 사용자가 알 필요가 없다는 것이다.
		  ![](https://i.imgur.com/5scpstY.png)

			```cpp
			#include "Abstract_Controll.h"

			int main(void)
			{
				AbstractionExample::Car* myCarAvantte = new AbstractionExample::Avantte();
			
				myCarAvantte->Accel();
				myCarAvantte->Accel();
				myCarAvantte->Break();
			
				return 0;
			}
			```

		- 우리는 자동차를 타며 `Accel`과 `Break`를 사용만 하면 되며,<br>
		  내부적으로 어떻게 동작하는지는 알 필요가 없다.
		- 해당 코드 내용은 솔리드원칙`(리스코프 치환 원칙)`과도 연관된다.
		- **<span style="font-weight:bold; color:#ffff00">쉽게 표현하면 구체적이지 않고 추상적으로 메서드 동작을 가늠해 결과만 받고 끝낸다.</span>**<br><br>
	- ##### 데이터 추상화
		- <span style="color:#00b0f0">사물 또는 대상(object)을 간단한 개념으로 일반화 하는 과정을 말한다.</span>
		- 쉽게 말해서 사각형, 삼각형, 원등의 객체들이 있을 때 공통적인 특징을 하나로 묶어,<br>
		  공통 특징인 도형(Shape)로 이름을 붙이는 것이 데이터 추상화이다.
		- 위 코드 예제로 사용했던 K3 객체를 추상화를 통해 객체 정보로 분리를하면,<br>
		  `K3 → 승용차 → 자동차 → 이동 수단`으로 추상화가 이루어진다.
			```cpp
			#pragma once
	
			#include <iostream>
			
			using namespace std;
			
			// 이동 수단
			class Vehicle 
			{
				// 시동 및 엔진 전원 기능
				virtual void PowerOn() = 0;
			};
			
			// 자동차
			class Car : public Vehicle
			{
				// 자동차에 대한 엑셀 및 브레이크 기능
				virtual void Accel() = 0;
				virtual void Break() = 0;
			};
			
			// 승용차
			class PassengerCar : public Car
			{
				virtual void Roofback() = 0;
			};
			
			class K3 : PassengerCar
			{
				void PowerOn() override { }
				void Accel() override { }
				void Break() override { }
				void Roofback() override { }
				
				void K3Function()
				{
			
				}
			};
			```

		 - 최종적으로 `K3`자동차는 시동, 엑셀, 브레이크, 루프백, K3기능까지 지니게된다.
		 - 이러한 공통 분모를 굳이굳이 나눠야 된다고 생각이 들 수도 있다.
		 - K3 하나만을 만들 때는 비효율적일 수 있지만 제품의 종류가 늘어나면 얘기가 달라진다.
		 - 추상화 해놨던 기능 상속만을 통해 다음과 같이 아주 빠른 확장성을 가질 수 있다.

			![](https://i.imgur.com/uIZR6lL.png)

---

- #### **<span style="font-weight:bold; color:#c29956">Inheritance - 상속</span>**
	- <span style="color:#00b0f0">상속은 부모-자식 관계가 형성되며 객체들간의 관계를 구축하는 방법이다.</span>
	- 위 `K3` 예시를 통해서 추상화를 통해 분리한 **추상 개념 정보들을 서로 이어줬다.**
	- 예제에서 서로 이어준 행동이 상속이다.
	- 상속은 상위(부모) 클래스의 속성(변수)과 기능(메서드)를 재사용해서,<br>
	  하위(자식) 클래스가 상위 클래스의 속성과 기능을 물려 받는 것이다.
	- 여기서 접근 제어 지시자 중 `private`을 통해 자식도 접근할 수 없게 할 수 있다.
	- 상속되는 클래스를 가리켜 `super , parent`라고 부르고 상속받는 클래스를 `sub , child`라고 부른다.

		```cpp
		// parent class
		class Super {
		protected:
			string name;
			int age;
		public:
			void Say() {
				cout << "Hi! my name is " << name << " and " << age << " year";
			}
		};
		// child class
		class Sub : public Super {
		protected:
			string hair;
		public:
			void HairColor() { 
				//... 
			}
		};
		```

	- 상속을 통해서 `중복된 속성들을 제거`하고 `코드의 재사용성`을 늘릴 수 있다.
	- Lion, Dog, Cat이라는 클래스가 존재한다고 했을 때 다음과 같다.

		```cpp
		class Dog {
		    int teethCount; // 중복된 속성들
		    int legCount; // 중복된 속성들
		    int tailCount; // 중복된 속성들
		    void Bark();
		};
		class Cat {
		    int teethCount; 
		    int legCount; 
		    int tailCount; 
		    void Meow();
		};
		class Lion {
		    int teethCount; 
		    int legCount; 
		    int tailCount;
		    void Roar();
		};
		```

		```cpp
		class Animal {
		protected:
			int _teethCount;
			int _legCount;
			int _tailCount;
		};
		class Dog : public Animal {
		public:
			void Bark();
		};
		class Cat : public Animal {
		public:
			void Meow();
		};
		class Lion : public Animal {
		public:
			void Roar();
		}
		```

	- 중복된 속성들을 하나로 묶어 이 특징을 지닌 동물들한테 상속을 해줬다.
	- **뭔가? 쎄하다! 맞다.**
	- 추상화 관련 내용에서 이미 알게모르게 학습 했던 내용이다.
	- OOP의 특징 4가지는 이렇게 서로 밀접한 관계를 지니고 있다.

---

- #### **<span style="font-weight:bold; color:#c29956">Encapsulation - 캡슐화</span>**
	- 캡슐화는 `정보 은닉(Information Hiding)`안에 속해 있는 개념 중 하나이다.
	- 정보 은닉은 데이터와 기능에 대한 코드의 형태를 외부로부터 알수 없게 한다.
	- 보호된 필드에 대해서는 `getter / setter`로만 접근이 가능하며,<br>
	  C#에서는 `properties (프로퍼티)`라는 것이 제공되어 게터/세터 메서드를 대체한다.
	- 이해가 잘 되지 않는다면 캡슐 알약 예시를 생각해보면 된다.
		- 캡슐 알약은 안에 어떠한 정보가 들어 있는지 모르지만 사용자는 해당 약으로부터<br>
		  효과만 누리면 된다. 이는 `제어 추상화`와도 이어지는 내용이다.
	- <span style="color:#92d050">캡슐화는 쉽게 하자면 키워드만으로도 표현할 수 있다.</span>
		- `private , protected` <br>

	```cpp
	#include <iostream>

	using namespace std;
	
	class CapsuleMedicine
	{
	public:
		void MedEffect()
		{
			cout << "약을 먹고 상태가 완화되는 중입니다." << endl;
		}
	
	private:
		// 데이터 속성은 외부에서 접근하지 못하도록 설정
		int _ingredient1 = 10;
		int _ingredient2 = 20;
		int _ingredient3 = 30;
	};
	```

	- 아주 간단한 캡슐화지만 해당 예시로부터 캡슐화가 어떤 것인지 이해할 수 있다.
	- 사용자는 약의 접근해서는 안되고 오로지 약의 효과만 제공 받으면 된다.
	- `private`을 통해 외부로부터의 접근을 캡슐로 감싸 막았다.
	- 사용자는 먹는다는 행위를 통해 `MedEffect()` 약의 효과만을 누리면 된다.

---

- #### **<span style="font-weight:bold; color:#c29956">Polymorphism - 다형성</span>**
	- <span style="color:#00b0f0">다형성이란 같은 자료형에 여러가지 타입의 데이터를 대입해 다양한 결과를 얻어내는 성질이다.</span>
	- 프로그래밍 언어에서 대표적인 다형성을 지니는 것으로는 다음과 같다.
		- overloading (오버로딩)
		- override , overriding (오버라이드, 오버라이딩)
		- up / down casting (업/다운 캐스팅)
		- `abstract` & `interface` (추상 클래스 & 인터페이스)
	- **즉 프로그래밍 관점에서의 다형성은 상속 관계에 있는 클래스에 대한 다채로운 성질이다.**
	- 간단하게 다형성에 대해서 알아보자면 다음 코드로 예시를 들 수 있다.

		```cpp
		class Rectangle { };
		class Triangle { };
		class Circle { };
		int main(void)
		{
			vector<Rectangle> rectangles;
			vector<Triangle> triangles;
			vector<Circle> circles;
			//...
		}
		```

	- 위와 같은 형태를 지닌 데이터 타입들을 하나의 타입으로 묶음 처리할 수 있다면?
	- 그 상위 타입을 통해서 데이터들의 공통적인 그리는 메서드를 사용할 수 있다.
	- 이 것이 다형성의 일환이며 구현하기 위해서 추상화와 상속의 내용이 가미된다.
	- 다음은 다형성을 구현한 코드이다.

		```cpp
		class Shape
		{
		public:
			// 순수 가상 함수를 이용해 구현을 명시
			virtual void Draw() = 0;
			virtual double GetArea() = 0;
		};
		
		class Rectangle : public Shape
		{
		public:
			double GetArea() override
			{
				return (double)_width * _height;
			}
		
			void Draw() override
			{
				std::cout << "사각형을 그립니다." << std::endl;
			}
		
		private:
			int _width;
			int _height;
		};
		
		class Triangle : public Shape
		{
		public:
			double GetArea() override
			{
				return (double)_width * _height * 0.5;
			}
		
			void Draw() override
			{
				std::cout << "삼각형을 그립니다." << std::endl;
			}
		
		private:
			int _width;
			int _height;
		};
		// ...
		```

		```cpp
		#include "Polymorphism.h"
		#include <vector>
		
		using namespace std;
		
		int main(void)
		{
			vector<Shape*> shapes;
		
			Rectangle* rect = new Rectangle();
			Triangle* tri = new Triangle();
		
			shapes.push_back(rect);
			shapes.push_back(tri);
		
			for (int i = 0; i < shapes.size(); ++i)
				shapes[i]->Draw();
		
			return 0;
		}
		```

		![](https://i.imgur.com/VpWUF9v.png)