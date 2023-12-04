---
title: Unity Project - Arkanoid24 Report
author: HuiSung
date: 2023-11-30
categories:
  - Bootcamp
  - TIL
tags:
  - Project
  - Plan
  - Log
image:
  path: /assets/img/ARKA_PROJ_TIL.png
---

유니티 입문 `Unity Beginner` 섹션에 팀 프로젝트가 시작 됐다.<br>
이번 게임의 주제는 `똥피하기`, `닷지`, `벽돌깨기`였는데 과반수로 우리 팀은 `벽돌깨기`를 하게 되었다.<br>
전반적으로 지식이 부족했던 게임이어서 배움에 도움이 될 것 같다고 생각을 했다.

---

## 프로젝트 구상안

#### 🎫<span style="color:#c29956">모티브가 되는 게임에 대해</span>
- 알카노이드는 아타리의 `브레이크 아웃`에서 유래된 게임이지만 장르의 이름이 되어버린 케이스다.
- 기본적인 컨셉은 알카노이드를 모티브`(모작)`하는 것을 목표로 하여 구성을 쉽게 잡은 편이다.
- **전체적인 핵심 기능들은 다음과 같다.**
	- `벽돌 (Brick)` - Level Stage, Scriptable Object
	- `공 (Ball)` - Bounce, Main System
	- `패들 (Paddle)` - Player, Input, Control
	- `충돌 (공, 패들, 벽)` - Collision, Vector
	- `게임 기본 (Game Logic)` - Life, Score, Start, End, GameManager
- 위 내용을 현대에 맞게 재해석을 해야하는데 일단 기본 목표는 핵심 기능 구현이다.

---

#### <span style="color:#c29956">Project Introduction</span>
##### <mark style="background: #BBFABBA6;"></mark>&nbsp;🏷️Arkanoid 24
- 수능에서 항상 내년 연도를 사용하는 것을 보고 영감을 얻음.
- 현대 작품에 맞게 재해석한 느낌을 주기 위해 24년이라는 의미를 지님.

---

##### <mark style="background: #BBFABBA6;"></mark>&nbsp;📆Develop Schedule
- 시작 [ `23. 11. 30 ( 목 )` ]
- 종료예정일 [ `23. 12. 07 ( 목 ) 13 : 00 `]

---

##### <mark style="background: #BBFABBA6;"></mark>&nbsp;⚙️Environment

| Header           | Description                     |
| ---------------- | ------------------------------- |
| **IDE**          | Visual Studio 2019, 2022, Rider |
| **Tool**         | Unity 2022.3.2f1                |
| **VCS**          | Git (GitHub Desktop)            |
| **Envrionment**  | PC `only`                       |
| **Resolution**   | 1920 x 1080 `FHD`               |
| **Collaborator** | 6                               |

`Code Write` - Visual Studio (VS)<br>
`Code Refactoring` - Jetbrain Rider

---

##### <mark style="background: #BBFABBA6;"></mark>&nbsp;👤Collaborator - Team Intro
- 팀장 `송희성` - 리팩터링 / 사용자 입력 / 플레이어(Paddle) / 공(Ball) / 멀티 플레이어 (로컬)
- 팀원1 `박상원` - 전체적인 게임 흐름, 로직 (코어) / UI디자인 및 리소스 제공 / 게임매니저 / 코드 리뷰
- 팀원2 `구도현` - 벽(Brick) 레벨 구성 / 스프라이트 리소스 / 멀티 플레이어 (로컬) / 상호작용(Interact)
- 팀원3 `유건희` - UI디자인 및 데이터 바인딩 / 프리팹 및 리소스 / 게임 컨셉 / 랭킹 및 플레이어 제어
- 팀원4 `고민수` - 아이템 디자인 / 아이템 스킬 구현 / 스킬 매니저 / 코드 분기 통합 / 트러블 슈팅
- 팀원5 `나재민` - 아이템 디자인 / 아이템 스킬 구현 / UI디자인 (설정) / 상호작용(Interact)

---

##### <mark style="background: #BBFABBA6;"></mark>&nbsp;📌Features
- `공 (Ball)` - Rigidbody2D, Bounce, Collision, Item Interact, Brick Interact
- `플레이어 (Paddle)` - Rigidbody2D, UserInput, Item Interact
- `벽 (Brick)` - Collider, Collision, Score, Ball Interact
- `아이템 (Items)`
	- General
		- Scale, Speed, Power - Slow, Life, Power(Scale)
	- Special
		- Catch - 공이 패들과 충돌 시 초기 상태처럼 패들에 장착.
		- Disruption - 공이 3방향 분기로 나뉘어져 발사
		- Laser - 벽을 부술 수 있는 특수 투사체 발사
- `스테이지 (Level)` - 난이도를 지닌 Stage 형식의 Scene으로 구성
- `멀티 플레이 (Local)` - Split 화면으로 분할하여 로컬 조작 1P, 2P 제공

---

##### <mark style="background: #BBFABBA6;"></mark>&nbsp;<span style="color:#7030a0">G</span>it <span style="color:#7030a0">C</span>onvention

💬 커밋 메세지 작성 (Summary)<br>
- [💡Feature] - 신규 기능 개발 (새로운 걸 만들었을 때)<br>
- [🐛BugFix] - 발생한 버그 해결<br>
- [🛠️Fix] - 기존에 있던 기능 수정<br>
- [➕Add] - 기존 기능에 대한 내용 추가 (기존에서 뭔가 추가 됐을 때) <br>
- ~~[🔥HotFix] - 아직은 미사용~~

<br>

🏷️ 양식
- **\<Summary>**
	- 커밋 메세지 부분 참고
	- 타이틀 또는 기능에 해당하는 부분 작성 
- **\<Description>** 
	- 어떤 문제가 발생 했는지 
	- 해당 부분에 어디서 문제가 발생 했는지 
	- 문제가 발생했는데 어떻게 수정 했는지 
	- 간단하게 요약해서 정리 (자세한 것은 코드의 주석으로)

<br>

⚙️ Git Branch 
- **브랜치 생성 규칙** 
	- `자신의 이름 이니셜`-`메인 담당 기능`
	- 예시) HS-PlayerPaddle
- 커밋 이후 `Merge` 진행할 때 반드시 말씀 부탁드립니다.
- `Develop`에 최신 업데이트가 되면 자신 브랜치 최신화 시키기

---

## 프로젝트 초기 세팅 : Project Initalize


- #### <span style="color:#c29956">GIt Convention - Branch Create</span>
	- 초기 리포지토리 생성 및 이니셜라이즈 - `박상원` [GitHub](https://github.com/psw1305/TeamProject-Arkanoid)
	- Git Convention에 의거한 브랜치 생성 규칙 준수 이전
		- `main (default)` branch 접근 제한 - protected rules 적용
		- `develop` branch - 실제 개발이 이루어질 디폴트 브랜치
		- 이 후 자신의 이니셜을 딴 기능별 구현 브랜치<br><br>

- #### <span style="color:#c29956">Scene Setting</span>
	- 유니티 작업 중 Git Merge, Conflict(충돌)을 최소화 하기 위한 안전장치 설정
	- 작업하는 개인은 각자의 테스트 씬에서만 간섭없이 진행할 것.
		- `test-name.scene`
	- 유니티 버전 통일 및 테스트 씬, 폴더 구조, 유니티 프로젝트 초기 세팅 배포<br><br>

- #### <span style="color:#c29956">Bouncy ball</span>
	- 공튀기기 게임 처럼 알아서 물리를 지니는 재질 `Bounce Material (Physics)` 적용
	- `Arkanoid.cs` 스크립트 생성 및 Rigidbody2D와 Collider를 지니는 공(Ball) 프리팹 생성
	- 물리 시뮬레이트(연산, 바운싱 등)는 `Bounce Physics Material`로 처리