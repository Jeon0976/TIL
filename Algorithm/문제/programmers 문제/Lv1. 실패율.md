### 날짜: 2023-04-12 21:23

### 주제:  2019년 KAKAO BLIND TEST
---
### 메모: 
#### 문제 설명
![failture_rate1.png](https://grepp-programmers.s3.amazonaws.com/files/production/bde471d8ac/48ddf1cc-c4ea-499d-b431-9727ee799191.png)
- 슈퍼 게임 개발자 오렐리는 큰 고민에 빠졌다. 그녀가 만든 프랜즈 오천성이 대성공을 거뒀지만, 요즘 신규 사용자의 수가 급감한 것이다. 원인은 신규 사용자와 기존 사용자 사이에 스테이지 차이가 너무 큰 것이 문제였다.
- 이 문제를 어떻게 할까 고민 한 그녀는 동적으로 게임 시간을 늘려서 난이도를 조절하기로 했다. 역시 슈퍼 개발자라 대부분의 로직은 쉽게 구현했지만, 실패율을 구하는 부분에서 위기에 빠지고 말았다. 오렐리를 위해 실패율을 구하는 코드를 완성하라.
-   실패율은 다음과 같이 정의한다.
    -   스테이지에 도달했으나 아직 클리어하지 못한 플레이어의 수 / 스테이지에 도달한 플레이어 수
- 전체 스테이지의 개수 N, 게임을 이용하는 사용자가 현재 멈춰있는 스테이지의 번호가 담긴 배열 stages가 매개변수로 주어질 때, 실패율이 높은 스테이지부터 내림차순으로 스테이지의 번호가 담겨있는 배열을 return 하도록 solution 함수를 완성하라.
##### 제한사항
-   스테이지의 개수 N은 `1` 이상 `500` 이하의 자연수이다.
-   stages의 길이는 `1` 이상 `200,000` 이하이다.
-   stages에는 `1` 이상 `N + 1` 이하의 자연수가 담겨있다.
    -   각 자연수는 사용자가 현재 도전 중인 스테이지의 번호를 나타낸다.
    -   단, `N + 1` 은 마지막 스테이지(N 번째 스테이지) 까지 클리어 한 사용자를 나타낸다.
-   만약 실패율이 같은 스테이지가 있다면 작은 번호의 스테이지가 먼저 오도록 하면 된다.
-   스테이지에 도달한 유저가 없는 경우 해당 스테이지의 실패율은 `0` 으로 정의한다.
##### 입출력 예
| N   | stages                   | result      |
| --- | ------------------------ | ----------- |
| 5   | [2, 1, 2, 6, 2, 4, 3, 3] | [3,4,2,1,5] |
| 4   | [4,4,4,4,4]              | [4,1,2,3]   |

##### 입출력 예 설명
입출력 예 #1  
1번 스테이지에는 총 8명의 사용자가 도전했으며, 이 중 1명의 사용자가 아직 클리어하지 못했다. 따라서 1번 스테이지의 실패율은 다음과 같다.
-   1 번 스테이지 실패율 : 1/8
2번 스테이지에는 총 7명의 사용자가 도전했으며, 이 중 3명의 사용자가 아직 클리어하지 못했다. 따라서 2번 스테이지의 실패율은 다음과 같다.
-   2 번 스테이지 실패율 : 3/7
마찬가지로 나머지 스테이지의 실패율은 다음과 같다.
-   3 번 스테이지 실패율 : 2/4
-   4번 스테이지 실패율 : 1/2
-   5번 스테이지 실패율 : 0/1
각 스테이지의 번호를 실패율의 내림차순으로 정렬하면 다음과 같다.
-   [3,4,2,1,5]
입출력 예 # 2
모든 사용자가 마지막 스테이지에 있으므로 4번 스테이지의 실패율은 1이며 나머지 스테이지의 실패율은 0이다.
-   [4,1,2,3]
#### 풀이 법
#####  최초 해결 법 
~~~ swift 
import Foundation 

func solution(_ N:Int, _ stages:[Int]) -> [Int] { 
    var failureDic:[Int:Double] = [:]
    
    // stage count 변수
    var accumulateCount: Double = 0.0
    // stage same level 변수
    var sameCount: Double = 0.0
    
    
    for i in 1...N {
        for stage in stages {
            if stage >= i {
                accumulateCount += 1
            }
            if i == stage {
                sameCount += 1
            }
        }
        failureDic[i] = sameCount/accumulateCount
        accumulateCount = 0
        sameCount = 0
    }
    // 값이 가장 큰 순서대로 먼저 정렬 후, 그 다음 key값의 오름차순으로 정렬
    let sortedFailure = failureDic.sorted(by: <).sorted(by: {$0.1 > $1.1})
    
    let result = sortedFailure.map { $0.key }
    
    return result
}
~~~
- 이중 for 문을 활용하여 직접 순차적으로 하나씩 대입해서 풀었음. 
- 결과는 맞았으나, 3문제에서 시간 초과 발생
- for문을 분리할 필요성을 느낌 -> stage의 길이가 최대 200,000이니 stage의 값을 하나의 배열의 인덱스로 나타내서 배열에 값을 넣으므로 for문 분리 가능

~~~ swift 
import Foundation 

func solution(_ N:Int, _ stages:[Int]) -> [Int] { 
    var failureDic:[Int:Double] = [:]
    
    var countArr = Array(repeating: 0, count: N + 2) 
    
    var count = Double(stages.count)
    
    for num in stages { 
        countArr[num] += 1
    }
    
    for i in 1...N { 
        if countArr[i] == 0 { 
            failureDic[i] = 0.0
        } else { 
            failureDic[i] = Double(countArr[i]) / count
            
            count = count - Double(countArr[i])
        }
    }
    
    // 값이 가장 큰 순서대로 먼저 정렬 후, 그 다음 key값의 오름차순으로 정렬
    let sortedFailure = failureDic.sorted(by: <).sorted(by: {$0.1 > $1.1})
    
    let result = sortedFailure.map { $0.key }
    
    return result
}
~~~
#### 결론 
- 주워진 값이 정수이고, 그 정수 값도 많으며, 정수 값의 개수를 이용해서 푸는 문제에 대해서는 배열의 인덱스를 이용해서 문제를 해결하면 이중 for문을 쓰는것보다 보다 더 효율적으로 문제를 해결할 수 있다. 

### 출처(참고문헌) 
- https://school.programmers.co.kr/learn/courses/30/lessons/42889

### 연결문서 
- 

### Tag
- #Algorithm/Programmers/Level1 
- #Algorithm/KAKAO
- #Algorithm/구현