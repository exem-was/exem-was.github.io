---
title:  "[Algorithm] 20. 문자열 (2/3)"
excerpt: "20.2 문자열 검색"

categories:
  - Algorithm
tags:
  - [algorithm]

toc: true
toc_sticky: true
 
date: 2022-04-12
last_modified_at: 2022-04-12
---

**pi[i] = N[..i] 의 접두사도 되고 접미사도 되는 문자열의 최대 길이**

pi를 구하는 방법은 추후에 다룸

### 알고리즘 구현

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
    

### **pi[ ] 를 구하는 법**

1. 같은 값이 나오면 1 씩 값을 쌓는다
2. 값이 다르면 이전 인덱스의 이전 인덱스 값을 확인하고 돌아간다.
3. 돌아가서 일치하면 index 에 +1을 해서 넣어주고 틀리면 이전 인덱스로 돌아간다.

```
a a b a a a b a c
  a a b a a a b a c
0 : 하나도 안 맞으면 0
0 1 : 맞으면 1
0 1 v : 틀리니까 대조 중인 index 의 이전 index 로 가서 대조
			: i[1] 인덱스의 값이 0이니까 [0]번 인덱스(a)로 가서 b와 비교 틀리면 이전 인덱스와 비교 
			: 틀리니까 0

a a b a a a b a c
		  a a b a a a b a c
0 1 0 
0 1 0 1 : 맞으니까 1
0 1 0 1 2 : 맞으니까 1+1
0 1 0 1 2 v : i[2] 인덱스에서 틀렸으니 이전 2번째 인덱스의 인덱스로 가서 1비교 a 니까
						: a와 일치하므로 1(index) + 1 = 2
0 1 0 1 2 2 : 맞았으니 순차적으로 다시 진행

a a b a a a b a c
			  a a b a a a b a c
0 1 0 1 2 2 3 4 v : i[4]에서 틀렸으니 이전 인덱스 b의 값인 0으로 가서 비교
0 1 0 1 2 2 3 4 0 : a 와 다르니 0

a a b a a a b a c
								  a a b a a a b a c

pi[] = {0, 1, 0, 1, 2, 2, 3, 4, 0}
```

### example

| H | a | a | b | a | a | a | c | d | a |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| N | a | a | b | a | a | a | b | a | c |
| Pi | 0 | 1 | 0 | 1 | 2 | 2 | 3 | 4 | 0 |

### 구현

```cpp
//N에서 자기 자신을 찾으면서 나타나는 부분 일치를 통해 pi[]를 계산한다.
//pi[i] = N[..i] 의 접미사도 되고 접두사도 되는 문자열의 최대 길이
vector<int> getPartialMatch(const string &N){
    int m = N.size();
    vector<int> pi(m, 0);
	  //kmp 로 자기 자신을 찾는다.
		//N을 N에서 찾는다. begin=0 이면 자기 자신을 찾아버리니까 건너뛴다.
    int begin = 1, matched = 0;
		//비교할 문자가 N의 끝에 도달할 때까지 찾으면서 부분 일치를 모두 기록한다.
    while(begin + matched < m){
        if (N[begin + matched] == N[matched]){
            ++matched;
            pi[begin + matched - 1] = matched;
						//일치하면 + 1해서 쌓아준다.
        }
        else {
            if (matched == 0) ++begin;
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

**N = aabaabac**

| begin | matched | N[begin+matched] | N[matched] | matched | pi[begin + matched - 1] | pi | begin | matched |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 0 | N[1]=a | N[0]=a | 1 | Pi[1] = 1 | [0,1....] |  |  |
| 1 | 1 | N[2]=b | N[1]=a |  |  |  | 1 + 1 - pi[0]  = 2 | pi[0]=0 |
|  |  |  |  |  |  |  |  |  |
