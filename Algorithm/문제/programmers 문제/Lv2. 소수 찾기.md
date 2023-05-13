### 날짜: 2023-04-11 18:55

### 주제:  permutation, prime 알고리즘 숙지 요망
---
### 메모: 
#### 문제
##### 문제 설명
- 한자리 숫자가 적힌 종이 조각이 흩어져있습니다. 흩어진 종이 조각을 붙여 소수를 몇 개 만들 수 있는지 알아내려 합니다.
- 각 종이 조각에 적힌 숫자가 적힌 문자열 numbers가 주어졌을 때, 종이 조각으로 만들 수 있는 소수가 몇 개인지 return 하도록 solution 함수를 완성해주세요.
##### 제한사항
-   numbers는 길이 1 이상 7 이하인 문자열입니다.
-   numbers는 0~9까지 숫자만으로 이루어져 있습니다.
-   "013"은 0, 1, 3 숫자가 적힌 종이 조각이 흩어져있다는 의미입니다.
##### 입출력 예
| numbers | return |
| ------- | ------ |
| "17"    | 3      |
| "011"   | 2      |

##### 입출력 예 설명
예제 #1  
[1, 7]으로는 소수 [7, 17, 71]를 만들 수 있습니다.
예제 #2  
[0, 1, 1]으로는 소수 [11, 101]를 만들 수 있습니다.
-   11과 011은 같은 숫자로 취급합니다.
#### 풀이 
- 숫자 카드를 모두 조합하여 만들 수 있는 숫자 중, 소수는 몇개인가?? 
	1. 생성 가능한 모든 숫자 조합을 만든다. (permutation)
	2. 숫자들의 조합 중 소수가 아닌 수를 전부 제거한다. (에라토스테네스의 체)
~~~ swift 
import Foundation 

func solution(_ numbers: String) -> Int {
	// 숫자 하나 하나로 나눠서 배열 생성 
	let arr = numbers.map { String($0) 
	// 결과 값 
	var primeNum = Set<Int>()
	var beforeProcessingNum = [[String]]()
	var processedNum = [String]() 
	
	// 모든 경우의 수 구하기 
	for i in 1...arr.count { 
		result += permutation(arr, i)
	}
	
	// 결과 값 가공하기
	for numberArray in beforeProcessingNum { 
		var value = String()
		for number in numberArray { 
			value += number 
		}
		processedNum.append(value)
	}
	
	// 소수 찾기 
	for num in processedNum { 
		if isPrime(Int(num)!) { 
			primeNum.insert(Int(num)!)
		}
	}
	
	return primeNum.count
}
	
// 소수 알고리즘 
func isPrime(_ num: Int) -> Bool { 
	guard num > 1 else { return false }
	guard num != 2 else { return true }
	
	for i in 2..<Int(sqrt(Double(num)) + 1) { 
		if num % i == 0 { 
		}
	}
}

// permutation 알고리즘
func permutation(_ arr: [Int], _ n: Int) -> [[Int]] { 
	var result = [[Int]]()
	
	guard array.count >= n else { return result }
	
	var visited = [Bool](repeating: false, count: arr.count)
	
	func cycle(_ now: [Int] ) { 
		if now.count == n { 
			result.append(now)
			return 
		}
		for i in 0..<array.count { 
			if visited[i] { 
				continue 
			} else { 
				visited[i] = true
				cycle(now + [array[i]])
				visited[i] = false
			}
		}
	}
	
	cycle([])
	
	return result 
}
~~~

### 출처(참고문헌) 
- https://school.programmers.co.kr/learn/courses/30/lessons/42839

### 연결문서 
- [[26. Depth-First Search]]
- [[9. 소수 찾기 알고리즘]]
- [[36. 에라토스테네스의 체]]
- [[7. 경우의 수 공식 총정리]]

### Tag
- #Algorithm/Programmers/Level2 
- #Algorithm/DFS 
- #Algorithm/에라토스테네스의체 
- #Algorithm/Math/permutation 
