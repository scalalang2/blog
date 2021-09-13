---
title: "Codeforce Round 725 후기"
date: 2021-06-17T09:26:47+09:00
draft: false
---

이번 라운드에서는 세 문제밖에 풀지못했다. 코드포스는 백준과 문제 유형이 많이 다르기 때문에 코드포스에 레이팅을 높이기 위해서는 코드포스에 공개된 문제도 많이 풀어보고 업솔빙도 많이 해봐야 할 것 같다 :), 내 목표는 파란색을 찍고 그만두는 것

## A. Stone Game
### 문제
도현이는 컴퓨터 게임을 하고 있다. 이 게임은 배열 A에 존재하는 n개의 돌맹이를 가지고 진행하며 i번째 돌맹이는 a_i 만큼의 힘을 가지고 있다. 각 돌맹이 마다 가진 힘은 모두 다르다. 

각 턴마다 도현이는 첫 번째 원소 혹은 마지막 원소에 존재하는 돌맹이를 부순다. 플레이어가 부순 돌맹이는 더 이상 존재하지 않는다. 이 때 도현이는 가장 힘이 작은 돌맹이와 가장 큰 힘을 가진 돌맹이를 부숴야 게임이 종료된다. 이 때 플레이어를 도와 이 조건을 만족하기 위한 최소 횟수를 찾아내시오.

예를 들어 n이 5이고 a=[1,5,4,3,2] 라면 도현이는 다음과 같이 조건을 만족할 수 있다.
* 먼저 첫 번째 원소의 돌맹이를 부수면 a는 [5,4,3,2]가 된다.
* 그리고 다시 첫 번째 원소의 돌맹이를 부수면 a는 [4,3,2]이 된다.

위 두 번의 동작으로 돌맹이 중 가장 적은 힘과 가장 큰 힘을 가진 돌맹이를 부숴 게임을 종료할 수 있다.

### 해결 방법
문제를 보자마자 가장 먼저 떠오른 건 덱(deque)을 이용하는 거였지만 문제를 풀다가 단순 계산만으로도 풀이가 가능할 것 같아서 최소값을 가진 i와 최대값을 가진 j의 위치로 계산하여 문제를 풀이하였다. 다음 세 개의 값 중 최소값을 정답에 대입하면 된다.

1. i < j 라면 왼쪽에서 j만큼 원소를 제거하면 i도 제거되기 때문에 `j값`이 정답이다.
2. i < j 라면 오른쪽에서 i만큼 원소를 제거하면 j도 제거되기 때문에 `size(a) - i값`이 정답이다.
3. i < j 인 상황에서 왼족에서 i만큼 제거하고 오른쪽에서 size(a) - j만큼 제거해도 정답이다.

반대로 `i > j`인 경우에도 위 세가지 값 중 최소값을 대입하면 정답이 된다.

### 코드
```python
import sys
from collections import deque
input = sys.stdin.readline
 
t = int(input())
for _ in range(t):
    n = int(input())
    power = [0 for _ in range(101)]
    stones = list(map(int, input().split()))
 
    for i in range(len(stones)):
        power[stones[i]] = i + 1
    
    minIndex = power[min(stones)]
    maxIndex = power[max(stones)]
    ans = 101
    
    if minIndex < maxIndex:
        ans = min(ans, maxIndex, len(stones) - minIndex + 1, minIndex + len(stones) - maxIndex + 1)
    else:
        ans = min(ans, minIndex, len(stones) - maxIndex + 1, maxIndex + len(stones) - minIndex + 1)
 
    print(ans)
```

## B. Friends and Candies
### 문제
도현이는 n명의 친구가 있다. i번 째 친구는 a_i의 사탕을 가지고 있다. 도현이는 친구들이 모두 다른 사탕을 가진것에 불만을 가져서 모든 사탕의 수를 도일하게 맞추고 싶다. 이를 위해서 도현이는 다음 동작을 통해 모두 동일한 사탕을 가지도록 만들려고한다.

도현이는 먼저 k개의 임의의 친구를 선택한다. 그리고 k개의 친구들의 사탕을 다른 친구들에게 분배해서 동일한 사탕의 수를 만들고자 한다. 이 동작은 단 한번만 수행할 수 있으며 k는 사전에 고정된 값이 아니라 도현이가 임의로 선택할 수 있다. 이 때 k의 최소값을 찾으시오. 만약 동일하게 사탕을 분배할 수 없는 k개 존재하지 않는다면 -1을 출력한다. 

### 해결 방법
코드포스 문제를 많이 풀어본 건 아니지만, 대체로 계산문제로 풀이할 수 있는 문제들이 많이 나오는 것 같다. 위에서 문제 자체는 k명의 친구를 선택해서 재분배하는 동작을 설명했지만 자세히 보면 아래 조건을 만족함을 알 수 있다.

1. 친구가 홀수명인데 사탕이 짝수개인 경우 정답은 -1이다.
2. 친구가 짝수명인데 사탕이 홀수개인 경우 정답은 -1이다.
3. 평균값보다 많은 사탕을 가진 친구를 다른 친구에게 재분배해주면 된다.

즉, 단순히 사탕의 평균개수보다 많이 가지고 있는 친구 k개를 찾아서 정답을 출력하면 된다. 이 때 사탕을 많이 가진 친구가 평균보다 많이 가진 개수 `over_sum - avg`과 사탕을 적게 가진 친구가 평균보다 적게 가진 개수 `avg - under_sum`이 동일한지 확인하고 동일하다면 k를 출력하면 된다.

### 코드
```python
import sys
input = sys.stdin.readline
 
t = int(input())
 
for _ in range(t):
    n = int(input())
    arr = list(map(int, input().split()))
 
    avg = sum(arr) // len(arr)
    over_cnt = 0
    over_sum = 0
    under_sum = 0
 
    for i in range(len(arr)):
        if arr[i] > avg:
            over_cnt += 1
            over_sum += arr[i] - avg
        else:
            under_sum += avg - arr[i]
 
    if over_sum == 0:
        print(0)
    elif over_sum == under_sum:
        print(over_cnt)
    else:
        print(-1)
```

## C. Number of Pairs
### 문제
당신은 n개의 정수를 가진 배열 a를 가지고 있다. 이 때 임의의 `(i,j) (1 <= i < j <= n)` 정수 짝을 찾되 두 배열의 합 (a_i + a_j)이 l보다 크거나 같고 r보다 작거나 같은 모든 조합을 찾아내시오.

예시) n = 3, a=[5,1,2], l=4, r=7 인 경우 (1, 2), (1, 3) 두 개의 짝이 아래 조건을 만족한다.

* 입력
    * 테스트 케이스 t (1 <= t <= 10^4)
    * 첫째 줄에 3개의 정수 n,l,r이 주어진다. (1 <= n <= 2 * 10^5, 1 <= l <= r <= 10^9)
    * 다음 줄에 n개의 정수 a1,a2,...,an (1 <= a_i <= 10^9)가 주어진다.

### 해결 방법
(i,j) <= r을 만족하는 i,j의 집합의 수를 A라고 하자. 그리고 (i,j) <= l-1을 만족하는 하는 집합의 수를 B라고 할 때 A-B가 정답이 된다. 이 문제를 입력값이 크기 때문에 위 범위를 찾기 위해서 이분 탐색을 이용해야한다.

C++에서는 `lower_bound`와 `upper_bound`함수를 이용해서 풀이할 수 있다. 파이썬에서는 `bisect_left`와 `bisect_right`함수를 제공하는데 이 두 함수는 값의 위치를 찾는게 아니라 새로운 값이 들어갈 수 있는 위치를 찾는 것이기 때문에 결과에 + 1을 해주고 계산해야 한다.

### 코드
```python
import sys
from bisect import bisect_left, bisect_right
input = sys.stdin.readline

ans = []

t = int(input())
for _ in range(t):
    n, l, r = list(map(int, input().split()))
    v = list(map(int, input().split()))
    v.sort()
    
    ret = 0
    for i in range(n):
        lower = bisect_left(v, l - v[i]) + 1
        upper = bisect_right(v, r - v[i]) + 1
        if l <= 2 * v[i] <= r:
            ret -= 1
        ret += upper - lower
    ans.append(ret // 2)

for el in ans:
    print(el)
```