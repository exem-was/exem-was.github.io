---
title:  "[Algorithm] 19. 큐와 스택, 데크"
excerpt: "일렬로 늘어선 자료들을 표현하는 자료 구조"

categories:
  - Algorithm
tags:
  - [algorithm]

toc: true
toc_sticky: true

date: 2022-03-29
last_modified_at: 2022-03-29
---
# 스택과 큐의 활용
# 예제: 큐를 이용한 조세푸스 문제의 해법

큐를 사용하여 조세푸스 문제를 해결해보자.

링크드 리스트로 구현을 했을 때는, 죽을 사람 가리키는 포인터를 옮겼다.

큐로 구현할 경우 사람들 자체를 움직이는 방식으로 구현해보자.

<br>

- 큐의 첫 번째 사람이 나와서 죽고
- 큐의 맨 앞에 있는 사람을 맨 뒤로 보내는 작업을 k-1 번 반복한다.

```java
public class JOSEPHUS {
	public static void main(String[] args) {
		JOSEPHUS JOSEPHUS = new JOSEPHUS();
		int[] solution = JOSEPHUS.solution(6, 3); // 3, 5
		System.out.println(Arrays.toString(solution));

		solution = JOSEPHUS.solution(40, 3);
		System.out.println(Arrays.toString(solution)); // 11, 26
	}

	public int[] solution(int n, int k) {
		// 전체 인원 n 만큼의 큐를 구현한다.
		Queue<Integer> queue = toQueue(n);
		// 첫번쨰 사람을 먼저 없앤다.
		queue.poll();
		while(queue.size() > 2) {
			// 앞에 사람을 꺼내어 뒤로 보내는 작업을 k-1 번 하고
			rearrangeQueue(queue, k-1);
			// 맨 앞 사람을 없앤다.
			queue.poll();
		}
		int first = queue.poll();
		int second = queue.poll();
		return new int[] {first, second};
	}

	private void rearrangeQueue(Queue<Integer> queue, int count) {
		for (int i = 0; i < count; i++) {
			Integer element = queue.poll();
			queue.add(element);
		}
	}

	private Queue<Integer> toQueue(int n) {
		Queue<Integer> queue = new LinkedBlockingQueue<>(n);
		for (int i = 0; i < n; i++) {
			queue.add(i+1);
		}
		return queue;
	}

}
```
