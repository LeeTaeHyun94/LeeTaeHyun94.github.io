---
layout: post
title:  "Method of Mutual Exclusion - TAS"
description: "Method of Mutual Exclusion - TestAndSet"
date:   2019-05-05 17:55:00
categories: OS
comments: true
---
### * 배경
- 공유 변수를 수정하는 동안 인터럽트 발생을 억제하여 임계 영역 문제를 간단하게 해결할 수 있지만, 이 방법은 항상 적용할 수 없고 실행 효율이 현저히 떨어진다.
- 소프트웨어적인 해결책은 더 복잡하고 프로세스가 2개 이상일 때는 더 오래 대기할 수 있다.

따라서, 메모리 영역의 값에 대해 검사와 수정을 원자적으로 수행할 수 있는 하드웨어 명령어 TestAndSet을 이용하여 간단한 방법으로 임계영역 문제를 해결할 수 있다.

## 1. TestAndSet(TAS) 명령어

TestAndSet 명령어는 하드웨어에서 명령을 사용하므로, 알고리즘이 간단하고, 하나의 메모리 사이클에서 수행하여 프로세스 간 경쟁 상태를 해결할 수 있다.
- 원자적 연산 명령어인 TestAndSet과 지역변수 lock을 설정하는 명령어 두 개의 기계 명령어로 이루어져 있다.
  - 원자적 연산 (Atomic Operation) : 중단 없이 실행하고 중간에 다른 사람이 수정할 수 없는 최소 단위 연산. 메모리의 1비트에서 작동하고, 대다수 기계에서 워드의 메모리 참조, 할당은 원자적이지만 나머지 대부분의 명령들은 원자적이지 않다.
  - TestAndSet 명령어
    - 일부 시스템에서 원자 명령어의 하나로, 읽기와 쓰기 모두를 제공
    - 해당 주소의 값을 읽고 새 값으로 교체하면서 해당 메모리 위치의 이전 값을 리턴한다.
    - 레지스터에 메모리 워드의 내용을 읽어와 그 위치에 1 값을 기록한 후 메모리의 이전 내용을 반환하는 일련의 원자적 연산
    - ```
      boolean TestAndSet (boolean *target) {
          boolean temp = *target;
          *target = true;
          return temp;
      }
      ```
      부울 변수 lock을 사용하여 프로세스가 임계 영역에 있으면 1로, 없으면 0으로 설정할 수 있다. 그러므로 기계가 TestAndSet 명령어를 지원할 때는 부을 변수 lock을 false로 초기화하여 상호배제를 구현할 수 있다.
    - lock을 이용한 상호배제
      ```
      do {
          while (TestAndSet(&lock)); // lock을 검사하여 true이면 대기, false이면 임계 영역 진입
          // 임계 영역
          lock = false; // 다른 프로세스의 진입 허용을 위해 lock을 false로
          // 나머지 영역
      } while (true);
      ```
      여러 프로세스가 대기 중일 때는 어떤 프로세스가 무한 대기 상태에 빠질 수 있으므로 한정 대기 조건을 만족하지는 않는다.
    - ```
      boolean waiting[n], lock = false, key;
      int j;
      do { // 프로세스 Pi의 진입 영역
        waiting[i] = true;
        key = true;
        while (waiting[i] && key) key = TestAndSet(&lock);
        waiting[i] = false;
        // 임계 영역
        // 탈출 영역
        j = (i + 1) & n;
        while ((j != i) && !waiting[j]) j = (j + 1) % n // 대기 중인 프로세스 탐색
        if (j == i) lock = false; // 대기 중인 프로세스가 없으면 다른 프로세스의 진입을 허용
        else waiting[j] = false; // 대기 중인 프로세스가 있으면 Pj가 다음 순서로  임계 영역에 진입할 수 있도록 설정
        // 나머지 영역
      } while (true);
      ```
- 장점
  - 사용자 수준에서 가능하다.
    - 메인 메모리를 공유하는 다중 프로세서나 단일 프로세서에서 프로세스 수에 관계없이 적용할 수 있다.
    - lock 변수 수에 상관없이 구현할 수 있다.
    - 구현이 단순하고 확인이 용이하다.
    - 다중 임계 영역을 지원한다.
- 단점
  - 바쁜 대기 발생
    - 프로세서 시간 소모가 크다.
    - 대기 프로세스는 비생산적, 자원이 소모되는 대기 루프에 남는다.
  - 기아 상태 발생 : 프로세스가 임계 영역을 떠날 때 프로세스 하나 이상을 대기하는 경우 가능하다.
  - 교착 상태 발생 : 플래그는 우선순위가 낮은 프로세스를 재설정할 수 있지만, 우선순위가 높은 프로세스가 선점한다. 따라서 우선순위가 낮은 프로세스는 lock을 가지고, 우선순위가 높은 프로세스가 이것을 얻으려고 시도할 때 높은 우선순위 프로세스는 무한정 바쁜 대기가 될 것이다.
