---
title:  "[Algorithm] 20. 문자열"
excerpt: "20.2 문자열 검색"

categories:
  - Algorithm
tags:
  - [algorithm]

toc: true
toc_sticky: true
 
date: 2022-04-13
last_modified_at: 2022-04-13
---
### INPUT

```cpp
src     : ababacabacaaba
search  : abacaaba 
```

### PI

```java
//pi[i] = str[..i] 의 접미사도 되고 접두사도 되는 문자열의 최대 길이
public static int[] getPartialMatch(String str) {
		int[] pi = new int[str.length()];
	 //N을 N에서 찾는다. begin=0 이면 자기 자신을 찾아버리니까 건너뛴다.
		int begin = 1, matched = 0;
	//비교할 문자가 N의 끝에 도달할 때까지 찾으면서 부분 일치를 모두 기록한다.
		while (begin + matched < str.length()) {
			if (str.charAt(begin + matched) == str.charAt(matched)) {
				++matched;
				pi[begin + matched - 1] = matched;
		//일치하면 접미사, 접두사 되는 문자열 최대 길이 + 1해서 쌓아준다.
			} else {
				if (matched == 0)
					++begin;
		//값이 다르고 matched가 0이면 begin을 다음 인덱스로 이동시킨다.
				else {
					begin += matched - pi[matched - 1];
					matched = pi[matched - 1];
				}
			}
		}
		return pi;
	}
```

abacaaba 예시 ⇒ [0, 0, 1, 0, 1, 1, 2, 3]

| begin | matched  | str.charAt(matched) | str.charAt(begin + matched)  | pi | 문자열 위치 |
| --- | --- | --- | --- | --- | --- |
| 1 | 0 | a | b | pi[1]=0 | abacaaba  |
| 2 | 0 | a | a | pi[2]=1 | abacaaba  |
| 2 | 1 | b | c |  | abacaaba  |
| 3 | 0 | a | b | pi[3]=0 | abacaaba  |
| 4 | 0 | a | a | pi[4]=1 | abacaaba  |
| 4 | 1 | b | a |  | abacaaba  |
| 5 | 0 | a | a | pi[5]=1 | abacaaba  |
| 5 | 1 | b | b | pi[6]=2 | abacaaba  |
| 5 | 2 | a | a | pi[7]=3 | abacaaba  |

### KMP

```cpp
vector<int> kmpSearch(string& src, string& search){
    int n = src.size(), m = search.size();
    vector<int> ret;
    // 탐색할 문자열의 접두사, 접미사 길이를 문자열의 처음부터 끝 까지 미리 계산
    vector<int> pi = getPartialMatch(search);    
    int begin = 0, matched = 0;
    
    while(begin <= n - m){
        // 탐색할 문자열과 원본 문자열에서 현재 위치의 문자가 동일한 경우
        if (matched < m && src[begin + matched] == search[matched]){
            ++matched;
            
            // 문자열이 모두 일치하는 경우 답에 추가한다.
            if (matched == m)
                ret.push_back(begin);
        }
        else{
            // 일치하는 부분이 없는 경우, 다음 위치의 문자 부터 탐색
            if(matched == 0)
                ++begin;
           
            else{
                begin += matched - pi[matched - 1];
               // begin을 옮겼다고 처음부터 다시 비교할 필요가 없다.
								// 옮긴 후에도 p[matched-1] 만큼은 항상 일치하기 때문이다.
                matched = pi[matched - 1];
            }
        }
    }

    return ret;
}
```

### 시간 복잡도 분석

- 분기 결과마다 다른 동작을 하기에 분석하기 어렵지만
    
    if 문의 각 분기 내용이 최대 몇번 수행되는 지로 수행 시간을 분석
    
- while 문 내에서 begin + matched 는 절대 감소하지 않는다.
- 한번 match 가 증가하고 나면 H[begin+matched] 를 다시 참조할 일은 전혀 없으므로
- 비교 성공은 짚더미의 각 문자당 최대 한 번씩만 일어나고
    
    결과적으로 이 분기는 최대 O(|H|)번 수행된다.
    

### 예제: 접두사/접미사

문자열 S가 주어질 때 이 문자열의 접미사도 되고 접두사도 되는 문자열들의 길이를 전부 출력

S=”ababbaba” 일 경우 “a”와 “aba”는 문자열의 접미사도 되고 접두사도 된다. 

- **a**babbab**a**
- **aba**bb**aba**

```cpp
//s의 접두사도 되고 접미사도 되는 문자열들의 길이를 반환한다.
vector<int> getPrefixSuffix(const string$ s){
	vector<int> ret, pi = getPartialMatch(s);
	int k = s.size();
	while(k>0){
	  //s[..k-1]는 답이다.
		ret.push_back(k);
    k = pi [k-1];
}
return ret;
```

| S | k | ret.push_back | pi[k-1] |
| --- | --- | --- | --- |
| ababbaba | 8 | 8 | pi[7] = ababbaba → 3 ⇒ k |
|  | 3 | 3 | pi[2] = ababbaba → 1 = k |
|  | 1 | 1 | pi[0] = ababbaba → 0 = k |
|  |  |  |  |

정답=8,3,1
