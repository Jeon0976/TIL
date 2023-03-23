### 날짜: 2023-03-23 14:11

### 주제: 배열의 정렬활용
---
### 메모: 
#### 문제
###### 문제 설명
> 정수 배열 `numbers`가 매개변수로 주어집니다. `numbers`의 원소 중 두 개를 곱해 만들 수 있는 최댓값을 return하도록 solution 함수를 완성해주세요.
---
##### 제한사항
-   0 ≤ `numbers`의 원소 ≤ 10,000
-   2 ≤ `numbers`의 길이 ≤ 100
---
##### 입출력 예
~~~
numbers
result
[1, 2, 3, 4, 5]
20
[0, 31, 24, 10, 1, 9]
744
~~~
---
##### 입출력 예 설명
입출력 예 #1
-   두 수의 곱중 최댓값은 4 * 5 = 20 입니다.
입출력 예 #1
-   두 수의 곱중 최댓값은 31 * 24 = 744 입니다.
#### My Solution
~~~ swift 
func solution(_ numbers:[Int]) -> Int {
    if numbers.filter ({ $0 == numbers.max() }).count > 1 {
        let max = numbers.max()!
        return max * max
    }
    let max1 = numbers.max()!
    let numbers2 = numbers.filter { $0 != max1 }
    let max2 = numbers2.max()!
    
    return max1 * max2
}
~~~
#### 좋아요가 많은 코드 
~~~ swift 
func solution(_ numbers:[Int]) -> Int { 
	let n = numbers.sorted()
	return (n[n.count - 1] * n[n.count - 2])
}
~~~
#### 인사이트
- 문제에서 최대값과 그 다음 값을 곱하는 형식으로 나눠져있다. 
- 최댓값을 뽑는다는 생각으로 단순하게 최대값을 뽑고 그 값을 지운다음 다음 배열에서 최대값을 뽑는 행위는 가독성 측면에서 두 번째 코드보다 떨어진다. 코드를 짤 때 시간,공간 복잡도를 생각하는 것도 중요하지만 코드의 가독성 또한 중요하다.
- 최댓값과 그 다음 값을 뽑아낸다고 생각했을 때 **배열의 정렬을 먼저 떠오르는 것이 중요하다.**

### 출처(참고문헌) 
- https://school.programmers.co.kr/learn/courses/30/lessons/120847

### 연결문서 
- 

### Tag
- #Algorithm/Programmers/Level0 