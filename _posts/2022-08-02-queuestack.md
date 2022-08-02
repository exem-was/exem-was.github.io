---
title:  "[Algorithm] Queue/Stack "

categories:
  - Algorithm
tags:
  - [java, algorithm, datastructure]
  
toc: true
toc_sticky: true

date: 2022-08-02
last_modified_at: 2022-08-02
---

# 같은 숫자는 싫어
https://school.programmers.co.kr/learn/courses/30/lessons/12906?language=java

## 문제 설명
배열 arr가 주어집니다. 배열 arr의 각 원소는 숫자 0부터 9까지로 이루어져 있습니다. 이때, 배열 arr에서 연속적으로 나타나는 숫자는 하나만 남기고 전부 제거하려고 합니다. 단, 제거된 후 남은 수들을 반환할 때는 배열 arr의 원소들의 순서를 유지해야 합니다. 예를 들면,
arr = [1, 1, 3, 3, 0, 1, 1] 이면 [1, 3, 0, 1] 을 return 합니다.
arr = [4, 4, 4, 3, 3] 이면 [4, 3] 을 return 합니다.
배열 arr에서 연속적으로 나타나는 숫자는 제거하고 남은 수들을 return 하는 solution 함수를 완성해 주세요.

## 제한사항
배열 arr의 크기 : 1,000,000 이하의 자연수
배열 arr의 원소의 크기 : 0보다 크거나 같고 9보다 작거나 같은 정수

## 입출력 예
arr	answer
[1,1,3,3,0,1,1]	[1,3,0,1]
[4,4,4,3,3]	[4,3]
입출력 예 설명
입출력 예 #1,2
문제의 예시와 같습니다.


## 문제 풀이
## John 풀이
```java
public int[] solution(int [] arr) {
    List<Integer> ans = new ArrayList<>();
    int temp = -1;
    for (int e : arr) {
        if (temp == e) {
            continue;
        }
        ans.add(e);
        temp = e;
    }
    return ans.stream()
            .mapToInt(Integer::intValue)
            .toArray();
}
```
## Chip 풀이
```java
public int[] solution(int [] arr) {
  Stack<Integer> temp = new Stack<>();
  temp.push(arr[0]);
  for (int number : arr) {
    if (temp.peek()!= number) {
     temp.push(number);
    }
  }

  return temp.stream().mapToInt(x -> x).toArray();
}
```

## Hazel 풀이
```java
public int[] solution(int []arr) {
    Deque<Integer> stack = new ArrayDeque<>();
    for (int i = 0; i < arr.length; i++) {
        if (!stack.isEmpty() && stack.peek() == arr[i]) {
            continue;
        }
        stack.push(arr[i]);
    }

    int[] answer = new int[stack.size()];
    for (int i = 0; i < answer.length; i++) {
        answer[i] = stack.pollLast();
    }

    return answer;
}
```
## GM 풀이
```java
public int[] solution(int []arr) {
  Deque<Integer> stack = new ArrayDeque<>();

  for(int a : arr) {
      if(Objects.isNull(stack.peekLast()) || stack.peekLast() != a){
          stack.add(a);
      }
  }
  return stack.toArray()).mapToInt(x -> (int) x).toArray();
}
```

# 올바른 괄호
## 문제 설명
괄호가 바르게 짝지어졌다는 것은 '(' 문자로 열렸으면 반드시 짝지어서 ')' 문자로 닫혀야 한다는 뜻입니다. 예를 들어

"()()" 또는 "(())()" 는 올바른 괄호입니다.
")()(" 또는 "(()(" 는 올바르지 않은 괄호입니다.
'(' 또는 ')' 로만 이루어진 문자열 s가 주어졌을 때, 문자열 s가 올바른 괄호이면 true를 return 하고, 올바르지 않은 괄호이면 false를 return 하는 solution 함수를 완성해 주세요.

## 제한사항
문자열 s의 길이 : 100,000 이하의 자연수
문자열 s는 '(' 또는 ')' 로만 이루어져 있습니다.

## 입출력 예
s	answer
"()()"	true
"(())()"	true
")()("	false
"(()("	false

## 문제 풀이

## John 풀이
```java
boolean solution(String s) {
    Deque<Character> deque = new ArrayDeque<>(s.length());
    for (char paren : s.toCharArray()) {
        if (paren == ')') {
            if (deque.isEmpty() || deque.pop() != '(') {
                return false;
            }
        }
        if (paren == '(') {
            deque.addFirst(paren);
        }
    }

    return deque.isEmpty();
}
```
```
효율성  테스트
테스트 1 〉	통과 (16.02ms, 71.2MB)
테스트 2 〉	통과 (14.79ms, 53.3MB)
```

## Chip 풀이
```java
boolean solution(String s) {
   if (s.length() % 2 == 1) {
      return false;
   }

   int remain = 0;

   for(int i=0;i<s.length();i++){
      if (s.charAt(i) == '(') {
         remain += 1;
      } else {
         if (remain == 0) {
            return false;
         } else {
            remain -= 1;
         }
      }
   }

   return remain == 0;
}
```
```
효율성  테스트
테스트 1 〉	통과 (6.20ms, 52.4MB)
테스트 2 〉	통과 (6.34ms, 52.5MB)
```


## Hazel 풀이
```java
boolean solution(String s) {
    if (s.length() % 2 == 1 || s.charAt(0) == ')'){
        return false;
    }

    char[] chars = s.toCharArray();
    Deque<Character> stack = new ArrayDeque<>();
    for (int i = 0; i < chars.length; i++) {
        if (chars[i] == '(') {
            stack.push(chars[i]);
        } else {
            if (stack.isEmpty()){
                return false;
            }
            stack.pop();
        }
    }

    return stack.isEmpty() ? true : false;
}
```
```
효율성  테스트
테스트 1 〉	통과 (15.34ms, 53.2MB)
테스트 2 〉	통과 (15.53ms, 53MB)
```
