### 날짜: 2023-04-11 17:51

### 주제:  문제를 다 풀어보고 유형을 빠르게 파악하자
---
### 메모: 
#### 문제
##### 문제 설명
- 어떤 숫자에서 k개의 수를 제거했을 때 얻을 수 있는 가장 큰 숫자를 구하려 합니다.
- 예를 들어, 숫자 1924에서 수 두 개를 제거하면 [19, 12, 14, 92, 94, 24] 를 만들 수 있습니다. 이 중 가장 큰 숫자는 94 입니다.
- 문자열 형식으로 숫자 number와 제거할 수의 개수 k가 solution 함수의 매개변수로 주어집니다. number에서 k 개의 수를 제거했을 때 만들 수 있는 수 중 가장 큰 숫자를 문자열 형태로 return 하도록 solution 함수를 완성하세요.
##### 제한 조건
-   number는 2자리 이상, 1,000,000자리 이하인 숫자입니다.
-   k는 1 이상 `number의 자릿수` 미만인 자연수입니다.
##### 입출력 예
| number       | k   | return |
| ------------ | --- | ------ |
| "1924"       | 2   | "94"   |
| "1231234"    | 3   | "3234" |
| "4177252841" | 4   |"775841"         |

#### 풀이
- return되는 숫자를 보면 정렬되지 않고 number가 k개의 숫자 만큼 빠진 상태 그대로 나오는 것을 확인할 수 있다. 
- 즉, stack을 활용해서 숫자를 stack에 넣고 그 다음 숫자와 비교해서 작으면 pop 아니면 그대로 남기는 형태로 해서 k개 만큼 뺏을 때 숫자를 출력하면 된다. 
~~~ swift 
import Foundation

func solution(_ number: String, _ k: Int) -> String {
    var _k = k
    
    var numberArray = number.map { String($0) }
    
    var stack = [String]()
    
    for i in 0...numberArray.count - 1 {
        while i < numberArray.count, !stack.isEmpty, _k > 0 {
            if stack.last! < numberArray[i] {
                stack.removeLast()
                _k -= 1
            } else {
                break
            }
        }
        
        if stack.count < numberArray.count - k {
            stack.append(numberArray[i])
        }
    }
    
    return stack.reduce("", +)
}
~~~

### 출처(참고문헌) 
- https://school.programmers.co.kr/learn/courses/30/lessons/42883

### 연결문서 
- [[1. Stack]]

### Tag
- #Algorithm/구현 
- #Algorithm/Greedy
- #DataStructure/Stack 
