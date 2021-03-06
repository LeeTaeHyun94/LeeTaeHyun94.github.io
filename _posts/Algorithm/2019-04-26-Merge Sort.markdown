---
layout: post
title:  "Merge Sort"
description: "Merge Sort"
date:   2019-04-26 03:25:00
categories: Algorithm
comments: true
---
병합(합병) 정렬은 분할 정복을 이용하는 점에서 퀵 소트와 비슷하다고 할 수 있다.

- 병합 정렬의 특징
  - 피봇 선택에 따라 최악의 시간복잡도가 O(N^2)이 되는 퀵 소트와 달리, 어떤 경우에도 O(NlogN)의 시간복잡도를 갖는다.
  - 대신, O(NlogN)의 공간복잡도를 필요로 한다.

1. 재귀 함수를 통해 리스트의 크기가 1이 될 때까지 분할하는 과정을 거친다.
2. 분할 이후에 재귀 함수가 기저 조건을 만나 이전 단계로 돌아가게 되면, 분할되었던 리스트가 합쳐지게 되는데, 이 때 분할된 두 개의 리스트의 처음부터 각각 비교해주면서 차례대로 조건에 맞게 정렬하면서 병합하면 된다. (오름차순이면 작은 것이 앞으로 오게끔)

위 과정을 거치게 되면, 모든 단계에서 분할된 두 개의 리스트는 이전 과정에서 정렬된 상태가 항상 보장되므로 정확성 또한 보장할 수 있다. 때문에 두 개의 리스트에서 처음부터 조건에 맞게 두 개의 원소끼리 차례대로 비교해주면 된다.

```c++
```
