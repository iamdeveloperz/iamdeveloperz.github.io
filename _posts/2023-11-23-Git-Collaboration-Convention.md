---
title: Git - Collaboration & Convention
author: HuiSung
date: 2023-11-23
categories: 
  - Bootcamp
  - TIL
tags:
  - TIL
  - Git
image:
  path: /assets/img/GIT_TIL_11-23.png
---

팀 프로젝트를 진행하면서 팀원 뿐만이 아닌 나도 많은 어려움을 겪었던 부분은 다름 아닌 `Git`이다.<br>
깃을 실제 협업처럼 사용하기 위해 `콜라보레이션`을 이용했는데 쉽다면 쉬운데 어렵다면 어려웠다.

## ♻️ Git Collaboration

### <mark style="background: #FFB8EBA6;"></mark>&nbsp; Git에서의 기본 동작(Commit / Push / Pull)

**<span style="font-weight:bold; color:#00b0f0">[Git 핵심 3가지 기능]</span>**
- `Commit` - 원격 저장소에 올리기 전 로컬에 데이터들을 저장(등록)
- `Push` - 커밋한 자료(로컬에 저장한 자료)들을 원격 저장소에 업로드
- `Pull` - Push 해서 저장소에 올라왔던 자료를 로컬로 당겨오는 것. (다운로드)<br>

위에 언급한 세 가지 내용이 `GitHub Desktop`을 이용할 때 핵심이 되는 기능들이다.<br>
만약 자신이 그냥 편하게 개인 프로젝트 또는 혼자 작업을 한다고 하면 저 세 개만 알고 있어도<br>
무난하게 자신의 저장소를 최신화 및 버전 관리를 할 수 있다.<br><br>
하지만 우리는 `협업(Collaboration)`을 하는 것이 목표이기 때문에 또 다른 기능을 알아야 한다.

---

### <mark style="background: #FFB8EBA6;"></mark>&nbsp; 🗝️병합 (Merge)
병합`(일명: 머지)`은 콜라보레이션(협업)에 있어 가장 핵심이되는 Git Technition이라고 할 수 있다.<br>
이 머지를 하는 과정에 있어서 충돌이 났을 때 대처방법이나 충돌이 되도록 나지 않게끔 구성을 해야한다. 

![](https://i.imgur.com/OVVxAr5.png)

팀 프로젝트를 진행하면서 팀원들이 가장 어려움을 겪었던 부분중에 하나라고 할 수 있다.<br>
특히 초기 리포지토리(저장소) 셋팅을 할 때 `.gitignore`에 문제가 생겼는지 계속 캐시파일이 들어가서<br>
갱신되는 문제가 발생했는데 이 점 때문에 안날 컨플릭트(충돌)도 계속 발생하게 됐다.
<br>
#### <span style="color:#ffc000">머지 하는 방법은 다음과 같다.</span>
- #### 기본 로직 (Commit - CheckOut - Fetch&Pull - CheckOut - Push)

![](https://i.imgur.com/oc5KL7t.png)

1. 자신이 구성한 코드 또는 데이터를 로컬에 저장한다 `(Commit - 자신 브랜치)`
2. 브랜치를 변경하여 자신이 머지할 브랜치로 변경.
	1. 최신 업데이트가 존재 하는지 확인하기 위해서 `Fetch Origin`
	2. 최신 자료가 있을 경우 `Pull`을 받아 최신화, 없더라도 자신 브랜치로 변경
3. Current Branch가 자신의 브랜치로 변경되었다면, 아까 커밋했던 내용을 **<span style="font-weight:bold; color:#00c000">푸시</span>**한다.

- #### 병합 로직

![](https://i.imgur.com/HPTARx0.png)

- 브랜치 푸시가 끝나게 되면, PR(Preview Pull Request) 버튼이 생성되어 활성화 된 모습을 볼 수 있다.
- `PR`을 눌러 내가 머지할 내용을 미리 보고 머지를 할지 안할지 정한다.

![](https://i.imgur.com/ovKx2a7.png)

1. `base: branch-name`을 꼭 개발브랜치<span style="color:#ffc000">(병합 해야하는 브랜치)</span>로 바꾸어 준다.
2. `Create pull request`버튼을 눌러 병합에 대한 요청을 만든다.
3. 깃-허브 사이트로 이동되며 커밋 내용 및 머지 내용을 작성한 후 `merge confirm`한다.

---

### <mark style="background: #FFB8EBA6;"></mark>&nbsp; Merge를 통한 협업 할 때 주의할 점

- **<span style="font-weight:bold; color:#b92727">항상 내 브랜치가 개발 브랜치와 동기화(즉, 최신화)</span>**가 되었는지부터 확인한다.
	- 이 부분이 제일 중요한 내용인데 최신화가 안된 브랜치를 통해 병합을 하면 새 데이터가 날라간다.

![](https://i.imgur.com/Jq4vbP3.png)

- 위 이미지를 살펴보면 `Branch A`는 어떤 기능을 만들고 Develop과 병합한다.
- `Branch B`는 `Develop` 브랜치가 변경되었지만 자신의 브랜치를 최신화 하지 않는다.<br>
  즉 `Branch B`에서 Merge Into로 최신화 하지 않고 이후 자신의 작업물만을 `Merge`한다.
- 이렇게 되면 `Branch A`의 기능은 전부 날라가게 된다.

이 부분은 실제 팀프로젝트를 진행하면서도 발생했던 내용인데, 이렇게 되면 이전 커밋으로 되돌리고<br>
`PR`을 요청했던 브랜치를 다시 최신화 시키고 해야하는 번거로운 상황이 발생하기 때문에 꼭 주의해야한다.

--- 

## Git Convention
### <mark style="background: #FFB8EBA6;"></mark>&nbsp; 컨벤션(규칙)이 필요한 이유

팀 프로젝트를 진행하며 `정훈님`이 엄청 짜임새 있는 깃 컨벤션을 준비해주셨다.<br>
컨벤션이란 코드작성 등 무엇인가를 `협업`하기 전 우리들만의 규칙이다.

![](https://i.imgur.com/qoqvSf5.png){: .normal }<br>

이런 규칙이 존재하면 커밋 모음을 봤을 때 언제 기능이 생겨났고 버그가 있으며 등등을<br>
**<span style="font-weight:bold; color:#92d050">이모지</span>**만으로 파악할 수 있기 때문에 한 눈에 들어오는 정도가 다르다.

이렇게 규칙적으로 커밋 메시지를 생성하지 않으면 나중에 `난잡한 커밋 메시지`들을 볼 수 있을 것이다.<br>

---

## 커뮤니케이션의 중요성
### <mark style="background: #FFB8EBA6;"></mark>&nbsp; 협업을 할 때는 의사소통을 잘하자!
이해하기는 쉽지만 실천하기 제일 힘든 내용이다. 커뮤니케이션이 중요하다는 것은 머리로 다 알고 있을거다.<br>
하지만 `내가 물어보는 것이 폐가 되지 않을까` 또는 `이런 것도 모르냐며 부끄러울 것 같아서`가 대부분이다.<br>
<br>
이번 팀프로젝트를 하면서 겪었던 가장 큰 문제가 커뮤니케이션, 의사소통에 중요성이었다.<br>
<span style="color:#92d050">무언가 잘못 되거나 충돌이 나면 혼자 해결하려고 끙끙 앓지 말고</span> 도움을 받아야만한다.<br>
<br>
초기에는 다들 깃 사용법도 익숙치 않고, 충돌을 처리하는 대처 능력 또한 떨어졌지만,<br>
지속된 커뮤니케이션으로 해결 방법도 찾고 대처 능력도 크게 향상 되었었다.
<br>
이번에 새로 진행되는 유니티-입문 팀 프로젝트에서도 이 부분을 더 이끌어서 해보도록 하자