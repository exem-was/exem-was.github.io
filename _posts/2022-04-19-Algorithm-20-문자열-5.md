---
title:  "[Algorithm] 20. 문자열 - 5"

categories:
  - Algorithm
tags:
  - [algorithm]
  
toc: true
toc_sticky: true

date: 2022-04-19
last_modified_at: 2022-04-19
---

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

H=aabaaba

n=7

N=abaab

m=5

| i | matched | H[i] ≠ N[matched] | matched > 0 | matched | pi[matched - 1] | matched | H[i] == N[matched] | matched | matched == m | ret.put_back(i-m+1) | matched |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 0 | 0 | a = a, False | False |  |  |  | a = a, True | 1 | 1 ≠ 5 |  |  |
| 1 | 1 | a = b, True | True | 1 | pi[0] | 0 | H[1] == N[0]
a = a, True  | 1 | 1 ≠ 5 |  |  |
| 2 | 1 | b = b, False |  |  |  |  | H[2] == N[1] | 2 | 2 ≠ 5 |  |  |
| 3 | 2 | a = a, False |  |  |  |  | H[3] == N[2]
a = a | 3 | 3 ≠ 5 |  |  |
| 4 | 3 | a = a, False |  |  |  |  | H[4] == N[3]
a == a | 4 | 4 ≠ 5 |  |  |
| 5 | 4 | b = b, False |  |  |  |  | H[5] == N[4]
b == b | 5 | 5 = 5, True | 5 - 5 + 1 = 1
ret.put_back(1) | pi[5-1]
(abaab)→ 2 |
| 5 | 2 |  |  |  |  |  |  |  |  |  |  |
|  |  |  |  |  |  |  |  |  |  |  |  |

###
