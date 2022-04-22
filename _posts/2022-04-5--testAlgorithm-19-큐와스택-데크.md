---
title:  "[Algorithm] 19. 큐와 스택, 데크"

categories:
  - Algorithm
tags:
  - [algorithm]
  
toc: true
toc_sticky: true

date: 2022-04-5
last_modified_at: 2022-04-5
---

# 스택과 큐의 활용

### 예제: 스택을 이용한 울타리 자르기 문제의 해법

![pic](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/bb9fa9e4-fcf2-42a9-a983-c454c366d911/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220422%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220422T033007Z&X-Amz-Expires=86400&X-Amz-Signature=29c455fbb765746ec03f5f1345a5ab0fbbe1491f0060950c4d6657c4ad2c18d8&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

### 구현

```cpp
// 각 판자의 높이를 저장하는 배열
vector<int> h;

// 스택을 사용한 O(n) 해법
int solveStack() {
	// 남아있는 판자들의 위치들을 저장한다.
	stack<int> remaining;
	h.push_back(0);
	int ret = 0;
	for (int i = 0; i < h.size(); i++) {
		// 남아있는 판자들 중 오른쪽 끝 판자가 현재 높이 h[i] 보다 높다면
		// 이 판자의 최대 사각형은 i 에서 끝난다.
		while (!remaining.empty() && h[remaining.top()] >= h[i]) {
			int j = remaining.top();
			remaining.pop();
			int width = -1;
			// j 번째 판자 왼쪽에 판자가 하나도 안 남아 있는 경우 left[j] = -1
			// 아닌 경우 left[j] = 남아있는 판자 중 가장 오른쪽에 있는 판자의 번호가 된다.
			if (remaining.empty())
				width = i;
			else
				width = (i - remaining.top() - 1);
			ret = max(ret, h[j] * width);
		}
		remaining.push(i);
	}
	return ret;
}
```

### 1. 과정 이해하기

(stack 에 남은 가장 오른쪽인자 : stack R)

![pic](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/97698ce8-d961-4482-b54b-a4e445073271/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220422%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220422T033015Z&X-Amz-Expires=86400&X-Amz-Signature=b50e1672068b57aa5e2bc5ade43fce7524e368ee7a1c2da73ba2e80beaa5c5d2&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

(a) remaining 에 쌓인 데이터가 없으니 stack 에 넣고 통과한다.

```java
remaining.push(i);
```

![pic](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/e37f5293-c3ea-4e0d-8375-f79da92603f9/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220422%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220422T033023Z&X-Amz-Expires=86400&X-Amz-Signature=52fdbc52f8ad235c62e923df3807ae9decab2bb13ba159934a052789b66e8dc2&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

(b) 현재 블록이 아직 stack R 보다 크므로 아직 최대 사각형 끝이 안나왔다.

쌓아주고 넘어간다

```java
remaining.push(i);
```

![pic](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/fc75ff51-ac85-471f-b734-2a051d0f233f/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220422%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220422T033031Z&X-Amz-Expires=86400&X-Amz-Signature=cc8d6c4fe00aef2cc76a4da7b3a168135495dc4ff3bf73ef58a162bc39a336a8&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

(c) 드디어 현재 블록이 stack R 보다 작다 == 최대 사각형의 마지막 포인트다.

```java
//while 문 조건 성립
int j = remaining.top(); // stack R 꺼내서 저장하고
remaining.pop(); // 지우고

int width = -1;
//stack 에 값이 비어있으면 가장 왼쪽 포인트이므로 width = i
			if (remaining.empty())
				width = i;
//아니면 i = 2 이므로 2 - 0 - 1 = 1 이므로 너비 1
			else
				width = (i - remaining.top() - 1);

// 높이(2번째 판자 stack R 의 높이) x 너비(1)
ret = max(ret, h[j] * width);
```

![pic](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/3dc63381-44bf-48d7-9d4d-05543a0ec4a3/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220422%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220422T033039Z&X-Amz-Expires=86400&X-Amz-Signature=9f99128dfaf38e4c37d4f7507132ced1bbc4c76fcfd94b36de0360ab8947fafe&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

(d) 에서 현재 블록이

remaining 이 empty 상태가 아니고, 현재 높이가 stack R 보다 크니까

쌓아주고 넘어간다

```java
remaining.push(i);
```

![pic](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/9dcefdd7-b460-4fb9-9383-a7245d8edd5e/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220422%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220422T033047Z&X-Amz-Expires=86400&X-Amz-Signature=4a8f0d0394b34f6bbb14ab1d5e45d308a600da448f3df407a70042f511ce3617&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)tled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9dcefdd7-b460-4fb9-9383-a7245d8edd5e/Untitled.png)

(e) 현재 블록이 stack R 과 같아도 조건으로 들어간다.

```java
while (!remaining.empty() && h[remaining.top()] >= h[i]) {...
```

```java
int j = remaining.top(); // stack R 꺼내고
remaining.pop(); // 지우고

int width = -1;
//stack 에 값이 비어있으면 가장 왼쪽 포인트이므로 width = i
			if (remaining.empty())
				width = i;
//아니면 i = 4 이므로 4 - 2 - 1 = 1 이므로 너비 1
			else
				width = (i - remaining.top() - 1);

// 높이(4번째 판자인 stack R 의 높이) x 너비(1)
ret = max(ret, h[j] * width);
```

![[그림] 계산되는 사각형과 인덱스 ](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/66b133f5-43ba-4800-ae5e-3ed116e76a2b/Untitled.png)

[그림] 계산되는 사각형과 인덱스 

(f) stack R 보다 작은 값이 들어왔으므로 최대 사각형의 끝값이다. (e) 와 같이 연산한다.

```java
// stack R(5번째 판자) 꺼내서 j에 저장하고 지우고

// i = 5 이므로 5 - 2 - 1 = 2 이므로 너비 2
width = (i - remaining.top() - 1);

// 높이(5번째 판자인 stack R 의 높이) x 너비(2)
ret = max(ret, h[j] * width);
```

![[그림] for 문 비교 후 ++i 로 i = 6](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d608d92c-c160-4465-a4da-2ffac7f9ead5/Untitled.png)

[그림] for 문 비교 후 ++i 로 i = 6

(g) for 문에 들어왔을 때 i = 6으로 값이 0 이므로 최대 사각형의 끝이다.

```java
// stack R(6번째 판자) 꺼내서 j에 저장하고 지우고

// i = 6 이므로 6 - 2 - 1 = 3 이므로 너비 3
width = (i - remaining.top() - 1);

// 높이(6 번째 판자인 stack R 의 높이) x 너비(3)
ret = max(ret, h[j] * width);
```

![pic](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/66b133f5-43ba-4800-ae5e-3ed116e76a2b/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220422%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220422T033056Z&X-Amz-Expires=86400&X-Amz-Signature=41ff5c1063bb515c282fd3fd0f47994c6ce7e99ca451887ed67c4f333921c977&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

(h) 지웠는데도 stack R 이 현재 i = 6 의 높이(0)보다 크므로 2번째 while 문을 탄다.

```java
// stack R (2번째 판자) 꺼내서 j에 저장하고 지우고

// i = 6 이므로 6 - 0 - 1 = 5 이므로 너비 5
width = (i - remaining.top() - 1);

// 높이(2 번째 판자인 stack R 의 높이) x 너비(5)
ret = max(ret, h[j] * width);
```

![pic](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/d608d92c-c160-4465-a4da-2ffac7f9ead5/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220422%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220422T033105Z&X-Amz-Expires=86400&X-Amz-Signature=a310d035335e007f704ce9875264403f3ad49ef975567ae933ccf53c5ce9c98d&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

(i) 지웠는데도 stack R (첫번째 판자) 이 현재 높이(0)보다 크므로 3번째 while 문을 탄다.

```java
// stack R (1번째 판자) 꺼내서 j에 저장하고 지우고

// remaining.empty 이므로 width = i = 6 
if (remaining.empty())
			width = i;

// 높이(1 번째 판자인 stack R 의 높이) x 너비(6)
ret = max(ret, h[j] * width);
```

이후에는 조건 중 !remaining.empty()로 인해 걸리지 않으므로 while 조건에서 넘어간다.

```java
while (!remaining.empty() && h[remaining.top()] >= h[i]) {...
```

다음 for 문으로 넘어가고 i = 7로 for 문도 함께 종료된다.
