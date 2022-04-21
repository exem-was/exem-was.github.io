---
title:  "[Algorithm] 20 문자열 - 6"

categories:
  - Algorithm
tags:
  - [algorithm]
  
toc: true
toc_sticky: true

date: 2022-04-20
last_modified_at: 2022-04-20
---

### 20.5 접미사 배열

- 어떤 문자열 S 의 모든 접미사를 사전순으로 정렬해 둔 것
- 접미사들을 문자열 배열에 저장하면
    
    문자열 길이의 제곱에 비례하는 메모리가 필요하기 때문에
    
    대개 접미사 배열은 접미사의 시작 위치를 담는 
    
    정수 배열로 구현됨
    
- “alohomora” 의 접미사 배열 A[] 와 각 위치에서 시작하는 접미사들을 보여줍니다.
    
[pic](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/e98edf35-d603-4004-863c-a59525fd391f/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220421%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220421T161949Z&X-Amz-Expires=86400&X-Amz-Signature=dd341859e0460cd0c2d39b0f235d96341b8fd886f96647a09fd67394dd074321&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
    

### 접미사 배열을 이용한 문자열 검색

- 접미사 배열을 이용해 할 수 있는 일로 문자열 검색이 있다.
- 접미사 배열을 이용한 문자열 검색은 짚더미 H가 바늘 N을 포함한다면 항상 N은 H의 어떤 접미사의 접두사라는 점을 이용한다.

### 모든 문자열에 성립되는 속성

1. H : alo**homo**ra 에서 N : homo를 찾고 있다.
2. N : homo는 A[2] homora의 접두사이다.
- 위 속성을 이용하면 H의 접미사 배열을 이진 탐색해서 각 문자열이 출현하는 위치를 찾을 수 있다.
- 접미사 배열의 길이는 항상 |H|이므로 이진 탐색의 내부는 O(log|H|) 번 수행되는 데, 각 문자열 비교에 O(|N|) 시간이 걸리기 때문에 이 이진 탐색의 수행 시간은 O(|N|log|H|)

### 접미사 배열 구현

- 접미사 배열을 만드는 가장 간단한 방법은 정렬 알고리즘을 사용하는 것
- 문자열의 길이가 n일때 [0, n-1] 범위의 정수를 모두 담은 정수 배열을 정렬하는데, 두 정수를 비교할 때 해당 위치에서 시작하는 접미사들을 비교한다.
- 해당 코드는 두 원소의 비교에 상수 시간이 걸릴 때 O(n log n)이 걸리고 최악의 경우는 두 문자열 비교에 O(n)이 걸리기때문에 O(n^2 log n)이 된다.
- “aaaa....aaaa”같은 문자열은 모든 비교가 끝까지 이뤄짐.

### 구현 코드

```java
/**
 * Suffix Array 정렬 알고리즘으로 생성하기
 * 값 비교 O(n)
 * 값 정렬 O(nlgn) 
 * 시간복잡도 O(n^2*lgn)
 */

public class SuffixArray_Sort {
   public static void main(String[] args) {
      String text = "alohomora";
      List<Integer> suffix = getSuffixArray(text);

      for(int i : suffix){
         System.out.println(i + " - " + text.substring(i));
      }
   }

   private static List<Integer> getSuffixArray(String text) {
      int n = text.length();
      // 접미사 배열이 될 반환 값
      List<Integer> perm = new ArrayList<>();
      for (int i = 0; i < n; i++) {
         perm.add(i);
      }
      Collections.sort(perm, Comparator.comparing(text::substring));
      return perm;
   }
}
```

콘솔 출력

```java
8 - a
0 - alohomora
3 - homora
1 - lohomora
5 - mora
2 - ohomora
4 - omora
6 - ora
7 - ra
```

### 접미사 배열 만들기 - 멘버 마이어스 알고리즘

- 정렬 알고리즘은  너무 느려서 사용하기 어려움
- 가장 빠른 알고리즘은 선형시간이지만 과정이 복잡하다.
- **정렬하는 문자열들이 한 문자열의 접미사**라는 점을 이용해 수행 시간을 낮추는 방법을 사용한다.
- 이 알고리즘은 접미사들의 목록을 여러번 정렬하는 데 매번 그 기준을 바꾼다.
    1. 처음에는 접미사의 첫 한 글자만을 기준으로 정렬
    2. 다음에는 접미사의 첫 두 글자를 기준으로 정렬
    3. 그 다음에는 접미사의 첫 네 글자를 기준으로 정렬
    4. 이렇게 log N 번의 정렬을 하고 나면 원하는 접미사 배열을 얻는다.
- 정렬을 많이 하지만 이전 정렬에서 얻은 정보를 사용해 두 문자열 대소 비교를 O(1)에 할 수 있다.

](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/93be659d-216e-420e-b62d-633400cc441a/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220421%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220421T161953Z&X-Amz-Expires=86400&X-Amz-Signature=788e8546d3effe80ef67d678f2edbbf3ed54007338cb2e83c1b4d141fd00c9e3&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
