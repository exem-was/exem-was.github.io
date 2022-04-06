---
title:  "[Algorithm] 19. 큐와 스택, 데크"
excerpt: "짝이 맞지 않는 괄호"

categories:
  - Algorithm
tags:
  - [algorithm]

toc: true
toc_sticky: true

date: 2022-04-06
last_modified_at: 2022-04-06
---
### 문제: 짝이 맞지 않는 괄호

- 모든 괄호는 해당하는 짝이 있어야 합니다. 이때(는 )와 ,{는}와,[는]와 짝을 이뤄야 만합니다.
- 모든 괄호 쌍은 먼저 열린 뒤 닫힙니다.
- 한 괄호 쌍이 다른 괄호쌍과 서로 ‘교차해’있으면 안됩니다. 이 정의에 의하면 [(])는 짝이 맞지 않는 경우 입니다.

### 입력

테스트 케이스 수, 각 문자열의 테스트 케이스

```cpp
3 
()
({[}])
({}[(){}])
```

### 출력

각 테스트 케이스마다 주어진 괄호 문자열이 짝이 맞는다면 YES 아니면 NO

```cpp
YES
NO
YES
```

### 구현

```cpp
bool wellMatched(const string& formula) {
	const string opening("({["), closing(")}]");
	stack <char> openStack;
	for (int i = 0; i < formula.size(); i++) {
		if (opening.find(formula[i]) != -1)
			openStack.push(formula[i]);
		else {
			if (openStack.empty())
				return false;
			if (opening.find(openStack.top()) != closing.find(formula[i]))
				return false;
			openStack.pop();
		}
	}
	return openStack.empty();

}
int main() {
	int C;
	string a;
	cin >> C;
	for (int i = 0; i < C; i++) {
		cin >> a;
		if (wellMatched(a))
			cout << "YES" << endl;
		else
			cout << "NO" << endl;
	}
}
```

## 자바 코드 구현

```java
public class BRACKETS2 {
	public static void main(String[] args) {
		BRACKETS2 brackets2 = new BRACKETS2();
		System.out.println(brackets2.wellMatched("()"));
		System.out.println(brackets2.wellMatched("({[}])"));
		System.out.println(brackets2.wellMatched("({}[(){}])"));
	}

	boolean wellMatched(String formula) {
		String opening = "({[";
		String closing = ")}]";
		Deque<Character> openStack = new ArrayDeque<>();
		for (int i = 0; i < formula.length(); i++) {
			if (opening.indexOf(formula.charAt(i)) != -1) {
				openStack.push(formula.charAt(i));
			} else {
				if (openStack.isEmpty()) {
					return false;
				}
				if (opening.indexOf(openStack.getFirst()) != closing.indexOf(formula.charAt(i))) {
					return false;
				}
				openStack.pop();
			}
		}
		return openStack.isEmpty();
	}
}
```

자바에서 Stack을 사용하는 것보다 Deque로 사용하는 것이 좋다.

> A more complete and consistent set of LIFO stack operations is
provided by the {@linkDeque} interface and its implementations, which should be used in preference to this class.  For example:
Deque<Integer>stack = new ArrayDeque<Integer>();
  
