### 날짜: 2023-04-13 17:52

### 주제:  최솟값을 구하라? DP 생각해보자 
---
### 메모: 
#### 문제
##### 문제 설명
- 아래와 같이 5와 사칙연산만으로 12를 표현할 수 있습니다.
	- 12 = 5 + 5 + (5 / 5) + (5 / 5)  
	- 12 = 55 / 5 + 5 / 5  
	- 12 = (55 + 5) / 5
- 5를 사용한 횟수는 각각 6,5,4 입니다. 그리고 이중 가장 작은 경우는 4입니다.  
- 이처럼 숫자 N과 number가 주어질 때, N과 사칙연산만 사용해서 표현 할 수 있는 방법 중 N 사용횟수의 최솟값을 return 하도록 solution 함수를 작성하세요.
##### 제한사항
-   N은 1 이상 9 이하입니다.
-   number는 1 이상 32,000 이하입니다.
-   수식에는 괄호와 사칙연산만 가능하며 나누기 연산에서 나머지는 무시합니다.
-   최솟값이 8보다 크면 -1을 return 합니다.
##### 입출력 예
| N   | number | return |
| --- | ------ | ------ |
| 5   | 12     | 4      |
| 2   | 11     | 3       |

##### 입출력 예 설명
- 예제 #1  
	- 문제에 나온 예와 같습니다.
- 예제 #2  
	- `11 = 22 / 2`와 같이 2를 3번만 사용하여 표현할 수 있습니다.
#### 풀이
~~~ swift 
import Foundation

// N으로 표현
func solution(_ N: Int, _ number: Int) -> Int{
    if N == number {
        return 1
    }
    
    // Set x 8 초기화
    var dp = Array(repeating: Set<Int>(), count: 8)
    
    var answer = 0
    // 각 Set마다 기본 수 'N' * i 수 초기화
    for i in 1...8 {
        dp[i-1].insert(Int(String(repeating: "\(N)", count: i))!)
    }
        
    // n 일반화
    // 계산은 최소 숫자 2개 이상이여야 하니깐 1번째 배열부터 조건 할 수 있도록 for문 조정
    // 모든 계산 경우의 수 Set에 넣기 
    for i in 0...7 {
        for j in 0..<i {
            for op1 in dp[j] {
                for op2 in dp[i-j-1] {
                    dp[i].insert(op1 + op2)
                    dp[i].insert(op1 - op2)
                    dp[i].insert(op1 * op2)
                    if op2 != 0 {
                        dp[i].insert(op1 / op2)
                    }
                }
            }
        }
        if dp[i].contains(number) {
            answer = i + 1
            break
        } else {
            answer = -1
        }
    }
    return answer
}
~~~

### 출처(참고문헌) 
- https://school.programmers.co.kr/learn/courses/30/lessons/42895

### 연결문서 
- [[8.  DP(Dynamic Programming)]]

### Tag
- #Algorithm/Dynamic_Programming 
- #Algorithm/Programmers/Level3