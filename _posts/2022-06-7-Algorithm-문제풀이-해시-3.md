---
title:  "[Algorithm] 문제풀이 - 해시-3"

categories:
  - Algorithm
tags:
  - [algorithm]
  
toc: true
toc_sticky: true

date: 2022-06-7
last_modified_at: 2022-06-7
---

# 1. 완주하지 못한 선수

- 수많은 마라톤 선수들이 마라톤에 참여하였습니다.
- 단 한 명의 선수를 제외하고는 모든 선수가 마라톤을 완주하였습니다.

## 주어지는 것

- 마라톤에 참여한 선수들의 이름이 담긴 배열 participant
- 완주한 선수들의 이름이 담긴 배열 completion

## 반환해야할 것

완주하지 못한 선수의 이름

### 제한사항

- 마라톤 경기에 참여한 선수의 수는 1명 이상 100,000명 이하입니다.
- completion의 길이는 participant의 길이보다 1 작습니다.
- 참가자의 이름은 1개 이상 20개 이하의 알파벳 소문자로 이루어져 있습니다.
- 참가자 중에는 동명이인이 있을 수 있습니다.

### 문제 풀이

- Map , contains Key 사용하지 않고
    
    ```java
    import java.util.HashMap;
    import java.util.Map;
    
    class Solution {
       public  String solution(String[] participant, String[] completion) {
    				Map<String, Integer> participantMap = toMap(participant);
            Map<String, Integer> completionMap = toMap(completion);
           
    		for (String key : completionMap.keySet()) {
    			int result = participantMap.get(key) - completionMap.get(key);
    
    			if (result > 0) {
    				return key;
    			}
    
    			participantMap.remove(key);
    		}
    
    		return participantMap.keySet().stream().findFirst().get();
    	}
    
    	 private Map<String, Integer> toMap(String[] participant) {
            Map<String, Integer> map = new HashMap<>();
            for (String s : participant) {
                map.put(s, map.getOrDefault(s, 0) + 1);
            }
    
            return map;
        }
    }
    
    ```
    
    ```java
    테스트 1 〉	통과 (86.15ms, 85.3MB)
    테스트 2 〉	통과 (112.20ms, 89.2MB)
    테스트 3 〉	통과 (147.40ms, 96.5MB)
    테스트 4 〉	통과 (131.58ms, 97MB)
    테스트 5 〉	통과 (66.24ms, 96.3MB)
    ```
    

- 풀이2(실패)
    
    ```java
    class Solution {
       public String solution(String[] participant, String[] completion) {
            Map<String, Integer> participantMap = toMap(participant);
            Map<String, Integer> completionMap = toMap(completion);
    
            for (String name: participantMap.keySet()) {
                if (completionMap.getOrDefault(name, 0) != participantMap.get(name)) {
                    return name;
                }
            }
            return null;
        }
    
        private Map<String, Integer> toMap(String[] participant) {
            Map<String, Integer> map = new HashMap<>();
            for (String s : participant) {
                map.put(s, map.getOrDefault(s, 0) + 1);
            }
    
            return map;
        }
    }
    ```
    
    ```java
    테스트 1 〉	통과 (45.47ms, 83MB)
    테스트 2 〉	실패 (70.24ms, 89.2MB)
    테스트 3 〉	실패 (64.22ms, 94.8MB)
    테스트 4 〉	실패 (79.66ms, 96MB)
    테스트 5 〉	실패 (79.27ms, 96.5MB)
    ```
    

- 풀이3(성공)
    
    ```java
    public  String solution(String[] participant, String[] completion) {
            Map<String, Integer> participantMap = toMap(participant);
    
            for (String name: completion) {
                participantMap.computeIfPresent(name, (key, count) -> count-1);
            }
    
            return participantMap.entrySet().stream().filter(entry -> entry.getValue() > 0).map(Map.Entry::getKey).findFirst().get();
        }
    
        private Map<String, Integer> toMap(String[] participant) {
            Map<String, Integer> map = new HashMap<>();
            for (String s : participant) {
                map.put(s, map.getOrDefault(s, 0) + 1);
            }
    
            return map;
        }
    ```
    
    ```java
    테스트 1 〉	통과 (43.47ms, 81.2MB)
    테스트 2 〉	통과 (79.55ms, 98MB)
    테스트 3 〉	통과 (79.55ms, 94.8MB)
    테스트 4 〉	통과 (94.29ms, 111MB)
    테스트 5 〉	통과 (84.50ms, 96.4MB)
    ```
    

- array sort 사용
    
    ```java
    import java.util.Arrays;
    
    class Solution {
    	public String solution(String[] participant, String[] completion) {
    		Arrays.sort(participant);
    		Arrays.sort(completion);
    
    		for (int i = 0; i < completion.length; i++) {
    			if (!participant[i].equals(completion[i])) {
    				return participant[i];
    			}
    		}
    
    		return participant[participant.length - 1];
    	}
    }
    ```
