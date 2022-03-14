---
title:  "[Algorithm] 18. 선형 자료 구조"
excerpt: "일렬로 늘어선 같은 종류의 자료 여러 개를 저장하기 위한 기초 자료"

categories:
  - Algorithm
tags:
  - [algorithm]

toc: true
toc_sticky: true
 
date: 2022-03-14
last_modified_at: 2022-03-14
---

# 도입

배열의 정의

- 일렬로 늘어선 같은 종류의 자료 여러 개를 저장하기 위한 가장 기초적인 자료 구조
- 동적 배열과 연결리스트

# 동적 배열

배열의 큰 문제중 하나는?

- 처음 배열 선언시 배열의 크기를 지정해야 한다.

이와 같은 문제를 해결하기 위해 자료 개수가 변함에 따라 크기가 변경되는 동적 배열을 사용한다.

동적 배열은 일반 배열처럼 언어 차원에서 지원되는 기능이 아니다.

동적 배열 역시 배열을 이용해 만들어낸 별도 자료구조다.

동적배열이 배열이므로 갖는 특성

- 원소들이 메모리의 연속된 위치에 저장된다.
- 주어진 위치의 원소를 반환하거나 변경하는 동작을 O(1) 에 할 수 있다.

동적 배열이 배열에 비해 갖는 추가 특성

- 배열의 크기를 변경가능하다. 동작 수행 시간은 배열의 크기 N에 비례한다.
- 새로운 원소를 배열의 맨 끝에 추가함으로써 크기 1을 늘리는 연산을 지원한다. 이 동작은 상수 시간이 걸린다.

동적 배열의 크기가 바뀔 때 동작 방식

- 단순하게 새 배열을 동적으로 할당받는다.
- 기존 원소를 복사한다.
- 새 배열을 참조하도록 바꿔치기 한다.

```cpp
int size; // 배열의 크기
ElementType* array; // 실제 배열을 가리키는 포인터
```

append() 연산을 어떻게 상수 시간에 구현할까?

즉, 배열의 크기와 상관없이 항상 일정한 시간에 원소를 추가할 수 있어야 한다.

메모리를 처음 할당 받을 때, 배열의 크기가 커질 것을 대비하여 여유 분의 메모리를 미리 할당한다.

만약, 배열이 이미 할당한 메모리에 꽉 찼을 때?

더 큰 메모리를 할당받아서 배열의 원소를 모두 옮긴다.

```
(a)
| 6 | 6 | 6 | 8 | 5 | 8 |   |  |
											 size  capacity
(b)
| 6 | 6 | 6 | 8 | 5 | 8 | 7 | 6 |
																size, capacity
						
```

- size: 배열에 들어가 있는 실제 원소의 수
- capacity: 배열에 할당받은 메모리 크기

```sql
public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    }
```

동적 배열이 저장해야 할 것들

- 현재 배열 크기
- 배열이 현재 저장된 메모리 내의 위치
- 용량

```cpp
// 현재 배열의 크기가 용량 보다 작으면
array[size++] = newValue;
```

위 예제 b와 같이 미리 할당해 둔 메모리가 꽉 찼을 때

```cpp
// 배열 용량이 꽉 찼을 때 재할당
if (size == capacity) {
  // 용량을 M만큼 늘린 새 배열을 할당
  int newCapacity = capacity + M;
  int* newArray = new int[newCapacity];
  // 기존 자료를 복사
  for (int i = 0; i < size; i++) {
		newArray[i] = array[i];
  }
  // 기존 배열을 삭제하고, 새 배열로 바꿔치기
  if(array) delete [] array;
  array = newArray;
  capacity = newCapacity;
}
array[size++] = newValue;
```

이 재할당과정에 드는 시간은?

- O(N+M), N 기존 배열 크기, M 추가된 배열 크기
- 재 할당에 선형 시간이 걸리는데, 과연 상수 시간이 맞을까?
