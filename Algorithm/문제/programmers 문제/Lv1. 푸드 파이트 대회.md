### 날짜: 2023-03-16 22:34

### 주제:  String, 배열 for 문 돌리기
---
### 메모: 
#### 인사이트
- 배열 for 문 돌리기 할때 기존 `for i in 0..<array.count` 대신  `for i in array.indices` 사용 or `for arr in array`
- The indices that are valid for subscripting the collection, in ascending order.
- String 연속 출력 -> String(repeating: , count: )
#### 문제
- 예를 들어, 3가지의 음식이 준비되어 있으며, 칼로리가 적은 순서대로 1번 음식을 3개, 2번 음식을 4개, 3번 음식을 6개 준비했으며, 물을 편의상 0번 음식이라고 칭한다면, 두 선수는 1번 음식 1개, 2번 음식 2개, 3번 음식 3개씩을 먹게 되므로 음식의 배치는 "1223330333221"이 됩니다. 따라서 1번 음식 1개는 대회에 사용하지 못합니다.
- 수웅이가 준비한 음식의 양을 칼로리가 적은 순서대로 나타내는 정수 배열 `food`가 주어졌을 때, 대회를 위한 음식의 배치를 나타내는 문자열을 return 하는 solution 함수를 완성해주세요.
##### 제한사항 
-   2 ≤ `food`의 길이 ≤ 9
-   1 ≤ `food`의 각 원소 ≤ 1,000
-   `food`에는 칼로리가 적은 순서대로 음식의 양이 담겨 있습니다.
-   `food[i]`는 i번 음식의 수입니다.
-   `food[0]`은 수웅이가 준비한 물의 양이며, 항상 1입니다.
-   정답의 길이가 3 이상인 경우만 입력으로 주어집니다.
#### 코드
~~~ swift 
func solution2(_ food:[Int]) -> String {
    var result = ""
    for i in food.indices {
        result += String(repeating: String(i), count: food[i] / 2)
	}
    return result + "0" + result.reversed()
}
~~~
### 출처(참고문헌) 
- https://school.programmers.co.kr/learn/courses/30/lessons/134240

### 연결문서 
- 

### Tag
- #Algorithm/Programmers/Level1 