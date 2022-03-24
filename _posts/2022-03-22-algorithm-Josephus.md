---
title:  "[Algorithm] 18. 선형 자료 구조"
excerpt: "18.3 연결 리스트"

categories:
  - Algorithm
tags:
  - [algorithm]

toc: true
toc_sticky: true
 
date: 2022-03-23
last_modified_at: 2022-03-23
---

# 연결리스트

## 동적 배열과 연결리스트 비교

동적 배열과 연결 리스트의 가장 큰 차이점은?

- 원소를 삽입하고 삭제하는 시간과 임의의 원소에 접근하는 시간에 차이점이 있다.

- 만약, 모든 원소를 순회하면서 삽입과 삭제를 한다면 연결리스트가 옳은 선택이다.

| 작업 | 동적 배열 | 연결 리스트 |
|---|---|---|
|이전 원소/다음 원소 찾기|O(1)|O(1)|
|맨 뒤에 원소 추가/삭제하기|O(1)|O(1)|
|맨 뒤 이외의 위치에 원소 추가/삭제|O(n)|O(1)|
|임의의 위치의 원소 찾기|O(1)|O(n)|
|크기 구하기|O(1)|O(N) 또는 구현에 따라 O(1)|

<BR>

i번째 노드를 찾는데 드는 시간은?

리스트의 길이에 선형 비례한다.

<BR>  
  
## 문제: 조세푸스 문제

- 조세푸스는 N-1 명의 동료 병사들과 함께 동굴에 포위당함

- N명의 사람들이 원형으로 둘러선 뒤에 순서대로 자살하려고 한다.

- 한 명이 자살하면, 다음에는 그 사람의 시계 방향으로 K번째 살아 있는 사람이 자살한다.

- 조세푸스와 다른 병사 하나만이 살아남았을 때 이들은 마음을 바꿔 로마에 항복해서 살아남았다.

- 마지막 두 명 중 하나가 되기 위해서는 조세푸스는 첫 번째 병사로부터 몇 자리 떨어진 곳에 있어야할까?

<BR>  

## 시간 및 메모리 제한

- 1초 안에 실행

- 64MB 이하의 메모리 사용

<BR>  

## 입력

- 테스트 케이스의 수 C (C≤ 50)

- 각 테스트 케이스 두 개의 정수 N, K로 주어진다.(3≤N, K≤1000)

- N = 전체 병사의 수

- K = 간격

<BR>  

ex) N=6, K=3

```
1 -> 2 -> 3 -> 4 -> 5 -> 6
x -> 2 -> 3 -> 4 -> 5 -> 6
x -> 2 -> 3 -> x -> 5 -> 6
x -> x -> 3 -> x -> 5 -> 6   
x -> x -> 3 -> x -> 5 -> x   
```

최종적으로 살아 남은 병사는 3번과 5번이다.

<BR> 

구현은 연결리스트가 적합할 것 같다. -> 원소의 삭제가 빈번하다.

```java
import java.util.Arrays;
import java.util.LinkedList;

public class JOSEPHUS {
	public static void main(String[] args) {
		JOSEPHUS josephus = new JOSEPHUS();
		int[] solution = josephus.solution(6, 3);
		System.out.println(Arrays.toString(solution));

		solution = josephus.solution(40, 3);
		System.out.println(Arrays.toString(solution)); // 11, 26
	}

	public int[] solution(int N, int K) {
		// 전체 병사의 수 N으로 연결리스트 생성
		LinkedList<Integer> soldiers = toLinkedList(N);
		// 연결리스트의 원소의 갯수가 2개가 남을 때까지 while

		int i = 0;
		soldiers.remove(i);
		while (soldiers.size() > 2) {
			i = (i+K-1) % soldiers.size();
			soldiers.remove(i);
		}
		return new int[]{soldiers.get(0), soldiers.get(1)};
	}

	private LinkedList<Integer> toLinkedList(int N) {
		LinkedList<Integer> result = new LinkedList<>();
		for (int i = 1; i <= N; i++) {
			result.add(i);
		}
		return result;
	}
}
```



  
