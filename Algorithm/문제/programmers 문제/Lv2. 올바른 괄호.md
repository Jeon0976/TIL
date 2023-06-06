### 날짜: 2023-06-06 13:33

### 주제: 스택의 활용 좀 더 효율적인 방법 찾기
---
### 메모: 
#### 문제 설명
- 괄호가 바르게 짝지어졌다는 것은 '(' 문자로 열렸으면 반드시 짝지어서 ')' 문자로 닫혀야 한다는 뜻입니다. 예를 들어
	- "()()" 또는 "(())()" 는 올바른 괄호입니다.
	- ")()(" 또는 "(()(" 는 올바르지 않은 괄호입니다.
- '(' 또는 ')' 로만 이루어진 문자열 s가 주어졌을 때, 문자열 s가 올바른 괄호이면 true를 return 하고, 올바르지 않은 괄호이면 false를 return 하는 solution 함수를 완성해 주세요.
##### 제한사항
- 문자열 s의 길이 : 100,000 이하의 자연수
- 문자열 s는 '(' 또는 ')' 로만 이루어져 있습니다.
---
##### 입출력 예

|s|answer|
|---|---|
|"()()"|true|
|"(())()"|true|
|")()("|false|
|"(()("|false|

#### 풀이
- '(' 가 없을 때를 대비해서 guard문 작성, '('가 더 많을 경우 대비해서 count 갯수 추가 
- append, removeLast를 활용해서 '()' 괄호 충족 조건 확인하기 
``` swift 
import Foundation

func solution(_ s:String) -> Bool
{    
    guard s.contains("(") else { return false }
    
    var stack = [Character]()
    var stackCount = 0
    
    s.forEach { 
        if $0 == "(" { 
            stack.append($0)
        } else { 
            if !stack.isEmpty { 
                stack.removeLast()
                stackCount += 2
            }
        }
    } 

    return s.count == stackCount ? true : false 
}
```
### 출처(참고문헌) 
- https://school.programmers.co.kr/learn/courses/30/lessons/12909

### 연결문서 
- [[1. Stack]]

### Tag
- #Algorithm/Programmers/Level2 
- #DataStructure/Stack 