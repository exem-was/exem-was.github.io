---
title:  "[Algorithm] 20. 문자열 - 4"

categories:
  - Algorithm
tags:
  - [algorithm]
  
toc: true
toc_sticky: true

date: 2022-04-18
last_modified_at: 2022-04-18
---

### 예제: 팰린드롬 만들기 (문제 ID: PALINDROMIZE , 난이도: 하)

- 팰린드롬이란 앞에서 읽었을 때와 뒤에서 읽었을 때가 똑같은 문자열
- 예시: noon, stats
- 가능한 짧은 팰린드롬을 원함

### 구현 방법

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/ff41cb14-ea86-4e75-9920-8bccaa139562/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220418%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220418T034744Z&X-Amz-Expires=86400&X-Amz-Signature=8ba65afad7c9875ab6b5b3b436d3370c94e3a8b080598f5d173920d3646efb1c&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

방법: 문자열 S를 뒤집은 문자열 S’를 만든 뒤 S의 접미사 이면서 S’ 의 접두사인 문자열 탐색

1. 시작 위치가 1과 3일 때 S의 접미사와 S’의 접두사가 일치
2. 이때 남은 S’의 나머지 부분을 뒤에 붙임 (anona, anonona)
3. 첫 번째로 찾은 위치를 응답으로 반환
    1. 겹치는 부분의 길이가 가능한 길어야 결과 팰린드롬의 길이가 짧아지기 때문

결과: anona

### 구현

```cpp
int maxOverlap(string a, string b){
    int n = a.size(), m = b.size();
    vector<int> pi = getPartialMatch(b);

    int begin = 0, matched = 0;
    while(begin < n){
        if(matched < m && a[begin + matched] == b[matched]){
            ++matched;
            //a의 마지막 문자까지 탐색을 마친 경우
            //현재까지 a와 b의 겹치는 부분의 길이를 반환한다.
            if(begin + matched == n)
                return matched;
        }
        else{
            if(matched == 0)
                ++begin;
            else{
                begin += matched - pi[matched-1];
                matched = pi[matched-1];
            }
        }
    }
    return 0;
}
```

| a | b | begin | matched | n | m | matched < m | a[begin+matched] == b[matched] | matched | begin + matched == n | begin |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| anon | nona | 0 | 0 | 4 | 4 | True  | a[0]==b[0] 
a ≠ n, False |  |  | 1 |
|  |  | 1 | 0 |  |  | True | a[1] == b[0]
n=n, True | 1 | 1+1 ≠ 4
False |  |
|  |  | 1 | 1 |  |  | True | a[2] == b[1]
o==o, True | 2 | 1+2 ≠ 4
False |  |
|  |  | 1 | 2 |  |  | True | a[3] == b[2]
n == n, True | 3 | 1+3 = 4
True 
⇒ Return 3 |  |

### KMP 알고리즘의 다른 구현

- 코드 20.2 의 KMP 알고리즘은 전통적인 KMP 알고리즘 구현과는 약간 다름
- 전통적인 구현은 이해하기 어렵지만 간결하고 다른 알고리즘에 응용되기 쉬움

### 코드

```cpp
vector<int> kmpSearch2(const string &H, const string &N)
{
        int n = H.size(), m = N.size();
        vector<int> result;
        vector<int> pi = getPartialMatch(N);

        //현재 대응된 글자의 수
        int matched = 0;
        //짚더미의 각 글자를 순회
        for (int i = 0; i < n; i++)
        {
                 //matched번 글자와 짚더미의 해당 글자가 불일치할 경우,
                 //현재 대응된 글자의 수를 pi[matched-1]로 줄인다
                 while (matched > 0 && H[i] != N[matched])
                         matched = pi[matched - 1];

                 //글자가 대응될 경우
                 if (H[i] == N[matched])
                 {
                         matched++;
                         if (matched == m)
                         {
                                 result.push_back(i - m + 1);
                                 matched = pi[matched - 1];
                         }
                 }
        }
        return result;
}
```

- 지금까지 대응된 문자의 수 matched 만을 유지하면서 짚더미의 모든 글자를 순회
- 각 글자마다 matched 를 적절하게 갱신
- 마지막에 본 짚더미의 글자 번호 i 는 20.2 코드의 begin + matched 와 같기에
    
    두 코드는 완전히 같은 알고리즘을 구현
