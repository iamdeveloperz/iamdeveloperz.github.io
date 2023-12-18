---
title: C++, Programmers - 콜라 문제
author: HuiSung
date: 2023-12-18
categories:
  - Algorithm
  - Programmers
tags:
  - CPP
  - Level1
  - "132267"
image:
  path: /assets/img/Algorithm_Programmers_Level1.png
---

## [<span style="color:#7030a0">G</span>it Hub Link - 콜라 문제](https://github.com/iamdeveloperz/Programmers/tree/main/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EC%8A%A4/1/132267.%E2%80%85%EC%BD%9C%EB%9D%BC%E2%80%85%EB%AC%B8%EC%A0%9C)

---

## 💫<span style="color:#c29956">문제 설명</span>

```
오래전 유행했던 콜라 문제가 있습니다. 콜라 문제의 지문은 다음과 같습니다.

> 정답은 아무에게도 말하지 마세요.
> 
> 콜라 빈 병 2개를 가져다주면 콜라 1병을 주는 마트가 있다.
> 빈 병 20개를 가져다주면 몇 병을 받을 수 있는가?
> 
> 단, 보유 중인 빈 병이 2개 미만이면, 콜라를 받을 수 없다.

문제를 풀던 상빈이는 콜라 문제의 완벽한 해답을 찾았습니다. 상빈이가 푼 방법은 아래와 같습니다.
우선 콜라 빈 병 20병을 가져가서 10병을 받습니다.
받은 10병을 모두 마신 뒤, 가져가서 5병을 받습니다.
5병 중 4병을 모두 마신 뒤 가져가서 2병을 받고, 또 2병을 모두 마신 뒤 가져가서 1병을 받습니다.
받은 1병과 5병을 받았을 때 남은 1병을 모두 마신 뒤 가져가면 1병을 또 받을 수 있습니다.
이 경우 상빈이는 총 10 + 5 + 2 + 1 + 1 = 19병의 콜라를 받을 수 있습니다.

문제를 열심히 풀던 상빈이는 일반화된 콜라 문제를 생각했습니다.
이 문제는 빈 병 `a`개를 가져다주면 콜라 `b`병을 주는 마트가 있을 때,
빈 병 `n`개를 가져다주면 몇 병을 받을 수 있는지 계산하는 문제입니다.
기존 콜라 문제와 마찬가지로, 보유 중인 빈 병이 `a`개 미만이면,
추가적으로 빈 병을 받을 순 없습니다. 상빈이는 열심히 고심했지만,
일반화된 콜라 문제의 답을 찾을 수 없었습니다.
상빈이를 도와 일반화된 콜라 문제를 해결하는 프로그램을 만들어 주세요.

콜라를 받기 위해 마트에 주어야 하는 병 수 `a`,
빈 병 a개를 가져다 주면 마트가 주는 콜라 병 수 `b`,
상빈이가 가지고 있는 빈 병의 개수 `n`이 매개변수로 주어집니다.
상빈이가 받을 수 있는 콜라의 병 수를 return 하도록 solution 함수를 작성해주세요.
```

---

### 💡<span style="color:#00b0f0">Think about</span>

- `연산자 (Operator)`를 활용한 문제이다.
- 반복문을 돌려 놓고 연산자만 잘 활용하면 된다.
- 파라미터로 대부분에 값이 주어지기 때문에 연산만 진행하면 된다.
	- `n`에 대한 빈병의 개수 `modBottle`는 `n % a`이다.
	- 위 식을 통해서 얻어지는 콜라의 개수는 `(n / a) * b`이다.
	- 콜라는 계속 더해지기만 하면 된다.
	- `콜라의 병 수(n)`은 다음과 같은 식으로 반복문에서 사용하면 된다.
		- `((n / a) * b) + modBottle` => `getCola + modBottle`

---

### <span style="color:#00b0f0">Code</span>

```cpp
#include <string>
#include <vector>

using namespace std;

int solution(int a, int b, int n) {
    int answer = 0;

    while (true)
    {
        if(n < a) break;
        int modBottle = n % a;
        int getCola = (n / a) * b;
        answer += getCola;
        n = getCola + modBottle;
    }

    return answer;
}
```

### ⚙️<span style="color:#00b0f0">Analyze & Feedback</span>

```cpp
int solution(int a, int b, int n) {
    return (n > b ? n - b : 0) / (a - b) * b;
}
```

- 한 줄 짜리로도 해당 코드를 풀 수 있다.
- `병의 개수(콜라 + 빈 병)`이 b보다 클 경우 `n - b`로 계산을 진행한다.
- `a = 2 / b = 1 / n = 20`일 경우 다음과 같은 예시가 만들어진다.
	1. `n(20) > b(1)` 참이므로 `(20 - 1) 반환`
	2. `(20 - 1) / (a[2] - b[1]) * b(1)`
	3. `(19) / (1) * (1) = 19`

- 반복문 없이 잘 연산된 조건문 한번으로 해당 결과 값을 도출해낼 수 있다.