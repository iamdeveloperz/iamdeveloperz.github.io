---
title: C++, Programmers - 가장 가까운 같은 글자
author: HuiSung
date: 2023-12-07
categories:
  - Algorithm
  - Programmers
tags:
  - CPP
  - Level1
  - 142086
image:
  path: /assets/img/Algorithm_Programmers_Level1.png
---

## [<span style="color:#7030a0">G</span>it Hub Link - 가장 가까운 같은 글자](https://github.com/iamdeveloperz/Programmers/tree/main/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EC%8A%A4/1/142086.%E2%80%85%EA%B0%80%EC%9E%A5%E2%80%85%EA%B0%80%EA%B9%8C%EC%9A%B4%E2%80%85%EA%B0%99%EC%9D%80%E2%80%85%EA%B8%80%EC%9E%90)

---

## 💫<span style="color:#c29956">문제 설명</span>

```
문자열 `s`가 주어졌을 때, `s`의 각 위치마다 자신보다 앞에 나왔으면서,
자신과 가장 가까운 곳에 있는 같은 글자가 어디 있는지 알고 싶습니다.  

예를 들어, `s`="banana"라고 할 때,  각 글자들을 왼쪽부터 오른쪽으로
읽어 나가면서 다음과 같이 진행할 수 있습니다.

- b는 처음 나왔기 때문에 자신의 앞에 같은 글자가 없습니다. 이는 -1로 표현합니다.
- a는 처음 나왔기 때문에 자신의 앞에 같은 글자가 없습니다. 이는 -1로 표현합니다.
- n은 처음 나왔기 때문에 자신의 앞에 같은 글자가 없습니다. 이는 -1로 표현합니다.
- a는 자신보다 두 칸 앞에 a가 있습니다. 이는 2로 표현합니다.
- n도 자신보다 두 칸 앞에 n이 있습니다. 이는 2로 표현합니다.
- a는 자신보다 두 칸, 네 칸 앞에 a가 있습니다. 이 중 가까운 것은 두 칸 앞이고, 이는 2로 표현합니다.

따라서 최종 결과물은 [-1, -1, -1, 2, 2, 2]가 됩니다.

문자열 `s`이 주어질 때, 위와 같이 정의된 연산을 수행하는 함수 solution을 완성해주세요.
```

---

### 💡<span style="color:#00b0f0">Think about</span>

- 배열에 대한 `인덱스`만을 사용하는 문제인 것 같다.
- ~~처음 글자가 나왔을 때는 무조건 `-1`로 표기 된다. 이는 알파벳 아스키 배열이 필요한가?~~
	- `bool` 값을 이용해 나왔는지 안나왔는지 `true/false`를 체크한다면 더 편할수도 있다.
	- 하지만 알파벳 아스키를 만드는 것 또한 귀찮은 행동이니 해당 부분은 패스한다.
- 반복문을 문자열의 길이만큼 돌린다. `s.length()`
- 해당 반복문 안에서 인덱스 변수를 선언 및 초기화를 해준다. `int index = -1;`
- 첫 글자와 다음 글자를 비교하기 위해서 `j < i`를 충족하는 중첩 반복을 시행한다.
- `s[i] == s[j]` 만약 같다면 `index = i - j`를 하여 인덱스 값을 얻어낸다.
- 이 후 벡터에 `push_back()`을 해주면 인덱스들을 지닌 결과가 나온다.

---

### <span style="color:#00b0f0">Code</span>

```cpp
#include <string>
#include <vector>

using namespace std;

vector<int> solution(string s) {
    vector<int> answer;

    for (int i = 0; i < s.length(); ++i)
    {
        int index = -1;
        for (int j = 0; j < i; ++j)
        {
            if (s[i] == s[j])
                index = i - j;
        }
        answer.push_back(index);
    }

    return answer;
}
```

---

### ⚙️<span style="color:#00b0f0">Analyze & Feedback</span>

- 현재 코드는 `O(n^2)`에 해당하는 시간 복잡도를 지니고 있다.
- 반복문을 한 번만 돌려도 해당하는 결과 값을 도출해낼 수 있었는데, 다음과 같다.

#### <span style="color:#c29956">1. Map을 사용한 Mapping</span>

```cpp
#include <string>
#include <vector>
#include <map>
using namespace std;

vector<int> solution(string s)
{
    map<char, int> mp;
    vector<int> answer;
    for (int i = 0; i < s.size(); ++i)
    {
        if (mp.find(s[i]) != mp.end()) answer.push_back(i - mp[s[i]]);
        else answer.push_back(-1);
        mp[s[i]] = i;
    }
    return answer;
}
```

- `Key-Value`를 사용한 문제 해결 방식이다. 일단 시간 복잡도가 `O(n)`으로 두 배가 줄어버렸다.
	- `Key` - 기존 문자열의 `문자`를 키 값으로 저장
	- `Value` - 문제에서 제공한 인덱스를 밸류로 저장
- `map`컨테이너는 중복을 허용하지 않는 점을 이용해 인덱스만 변경해주면 된다.
- <span style="color:#92d050">즉, `banana`라는 예시가 있을 때 다음과 같이 동작한다.</span>
	1. 조건 불충족 -> `mp[b] = 0` = `answer[0] = -1`
	2. 조건 불충족 -> `mp[a] = 1` = `answer[1] = -1`
	3. 조건 불충족 -> `mp[n] = 2` = `answer[2] = -1`
	4. 조건 충족 -> `i - mp[a]` = `3 - 1` / `mp[a] = 3` = `answer[3] = 2`
	5. 조건 충족 -> `i - mp[n]` = `4 - 2` / `mp[n] = 4` = `answer[4] = 2`
	6. 조건 충족 -> `i - mp[a]` = `5 - 3` / `mp[a] = 5` = `answer[5] = 2`

---

### <span style="color:#c29956">2. 다른 벡터를 이용한 방법</span>

```cpp
#include <string>
#include <vector>
using namespace std;

vector<int> solution(string s)
{
    vector<int> answer;

    vector<int> alpha(26, -1);
    for(int i=0; i<s.length(); i++)
    {
        if(alpha[s[i]-'a']==-1) answer.push_back(-1);
        else                    answer.push_back(i-alpha[s[i]-'a']);
        alpha[s[i]-'a']=i;
    }

    return answer;
}
```

- 이 것 또한 시간 복잡도가 `O(n)`으로 내가 풀이했던 방법보다 효율적이다.
- `alpha`라는 벡터를 만들어 `-1`로 초기화 후 아스키 코드로 중복 체크를 한다.
- 똑같이 `banana`라는 예시를 들었을 때 다음과 같이 동작한다.
	1. 조건 불충족 -> `alpha['b'(98) - 'a'(97) = 1] == -1` = `answer[0] = -1`
	2. 조건 불충족 -> `alpha['a' - 'a'] == -1` = `answer[1] = -1`
	3. 조건 불충족 -> `alpha['n' - 'a'] == -1` = `answer[2] = -1`
	4. 조건 충족 -> `alpha['a'] == -1이 아님` = `answer[3] = 3 - 1 (alpha['a' - 'a'])`
	5. 조건 충족 -> `alpha['n'] == -1이 아님` = `answer[4] = 4 - 2 (alpha['n' - 'a'])`
	6. 조건 충족 -> `alpha['a'] == -1이 아님` = `answer[5] = 5 - 3 (alpha['a' - 'a'])`

- 위 내용에 코드는 제한 사항이 소문자일 경우에만 유효하다.
- Map 컨테이너를 사용하는 방식이 보다 깔끔하게 사용할 수 있다.