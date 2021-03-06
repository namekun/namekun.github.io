---
title: "프로그래머스 코딩테스트 연습 문제 - Hash - level 3 - 베스트 앨범"
tags:
  - python
  - algorithm
  - hash
  - 프로그래머스
comments: true
---

### 문제 설명

스트리밍 사이트에서 장르 별로 가장 많이 재생된 노래를 두 개씩 모아 베스트 앨범을 출시하려 합니다. 노래는 고유 번호로 구분하며, 노래를 수록하는 기준은 다음과 같습니다.

1. 속한 노래가 많이 재생된 장르를 먼저 수록합니다.
2. 장르 내에서 많이 재생된 노래를 먼저 수록합니다.
3. 장르 내에서 재생 횟수가 같은 노래 중에서는 고유 번호가 낮은 노래를 먼저 수록합니다.

노래의 장르를 나타내는 문자열 배열 genres와 노래별 재생 횟수를 나타내는 정수 배열 plays가 주어질 때, 베스트 앨범에 들어갈 노래의 고유 번호를 순서대로 return 하도록 solution 함수를 완성하세요.

### 제한사항

- genres[i]는 고유번호가 i인 노래의 장르입니다.
- plays[i]는 고유번호가 i인 노래가 재생된 횟수입니다.
- genres와 plays의 길이는 같으며, 이는 1 이상 10,000 이하입니다.
- 장르 종류는 100개 미만입니다.
- 장르에 속한 곡이 하나라면, 하나의 곡만 선택합니다.
- 모든 장르는 재생된 횟수가 다릅니다.

### 입출력 예

| genres                                | plays                      | return       |
| ------------------------------------- | -------------------------- | ------------ |
| [classic, pop, classic, classic, pop] | [500, 600, 150, 800, 2500] | [4, 1, 3, 0] |

### 입출력 예 설명

classic 장르는 1,450회 재생되었으며, classic 노래는 다음과 같습니다.

- 고유 번호 3: 800회 재생
- 고유 번호 0: 500회 재생
- 고유 번호 2: 150회 재생

pop 장르는 3,100회 재생되었으며, pop 노래는 다음과 같습니다.

- 고유 번호 4: 2,500회 재생
- 고유 번호 1: 600회 재생

따라서 pop 장르의 [4, 1]번 노래를 먼저, classic 장르의 [3, 0]번 노래를 그다음에 수록합니다.

---

우선 생각 난 것은 딕셔너리의 구조를 어떻게 짜야하는가이다. 

처음에는 `<인덱스, 장르>` 한  개, `<인덱스 , 횟수>` 이렇게 두개 만들고, 추가로 합계 해시를 만들까했었다. 그런데 이렇게 하자니 어떻게 해시간의 연동을 이끌어 낼까도 문제였다.

어떻게 할까 고민하다가 다른 분의 풀이를 참고하기로 했다.

해시안을 이런식으로 구현하였다. 

```python
hash_map = {장르 : [총 횟수, (인덱스, 재생횟수)]}
```

이렇게 구현한 뒤에 새로 `list`를 만들어 주고, 그 안에 딕셔너리에 데이터를 넣은 뒤에 내림차순으로 한번 더 정렬을 해준다.

그 다음 내림차순으로 된 딕셔너리에서 총 플레이 횟수인 맨 앞을 `pop`으로 빼주고, 그 안에서도 또 내림차순으로 정렬한 뒤에 플레이 횟수가 높은 노래 2개의 인덱스를 `answer`에 더해준다.

그렇게 연산을 행한 뒤에 기존 딕셔너리에서 사용한 맨 앞의 데이터를 `pop`해준다.

위의 연산은 딕셔너리가 존재할때까지 계속 반복하도록 한다.

이렇게 구현한 코드는 다음과 같다.

```python
from operator import itemgetter


def solution(genres, plays):
    answer = []
    musics = {}

    for i in range(0, len(genres)):
        if genres[i] not in musics:
            musics[genres[i]] = []
            musics[genres[i]].append(0)
        musics[genres[i]].append((i, plays[i]))
        # 플레이 횟수를 더해줘서 가장 앞에 오게 한다.
        musics[genres[i]][0] += plays[i]

    # sort desc

    temp = list(musics.values())
    # 기준은 해당 값의 처음값으로 하고, 내림차순으로 정렬한다.
    # itemgetter는 sort 하고자 하는 list의 item의 1번째 인덱스 기준으로 sort 하는 방식
    temp.sort(key=itemgetter(0), reverse=True)

    # 가장 많이 플레이된 장르를 Pop한다.
    # 그리고 가장 많이 플레이된 두개의 노래를 고른다.
    while temp:
        max = -1

        temp[0].pop(0)

        # 플레이 횟수를 기준으로 많이된 순으로 내림차순 정렬
        temp[0].sort(key=itemgetter(1), reverse=True)

        # 가장 플레이 많이 된 두개의 노래를 answer에 append해준다.
        for i in range(0, len(temp[0])):
            if i >= 2:
                break
            answer.append(temp[0][i][0])

        # 현재 장르를 제거한다.
        temp.pop(0)

    return answer
```

처음 풀어보는 level 3짜리 문제였는데.. 아직 내 알고리즘적인 사고력이 부족한 것이 느껴졌다. 더더욱 정진해야겠음을 느끼게했다.
