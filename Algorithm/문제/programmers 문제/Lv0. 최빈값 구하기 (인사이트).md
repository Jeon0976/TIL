### 날짜: 2023-03-21 13:14

### 주제: reduce, Dictionary(grouping: ) 
---
### 메모: 
#### 기존 풀이 코드 
~~~ swift 
func manyTimeInt(_ array: [Int]) -> [Int] { 
	var count = 0 
	var maxValue = 0 
	
	// key : array의 요소, value: 빈도수
	var manyTimeInt: [Int:Int] = array.reduce(into: [:]) { partialResult, value in 
		partialResult[value, default:0] += 1
	}

	for dicValue in manyTimeInt { 
		if dicValue.value == manyTimeInt.values.max() { 
			count += 
			maxValue = dicValue.key
		}
	}
	return count == 1 ? maxValue : -1
}
~~~
#### 다른 사람이 푼 코드 중 좋아요가 가장 많았던 코드
~~~ swift 
func manyTimeInt(_ array: [Int]) -> [Int] { 
	let sorted = Dictionary(grouping: array) {$0}.sorted {$0.value.count > $1.value.count}
	return sorted.count > 1 && sorted[0].value.count == sorted[1].value.count ? -1 : sorted[0].key
}
~~~
#### 인사이트
- 처음 문제를 받고 풀때 배열을 딕셔너리로 변환하는 작업을 거친후 조건에 맞게 설계하면 되겠다고 생각했었다. 그러던 중 배열을 딕셔너리로 변환하면서 최빈값을 쉽게 뽑아낼 수 있는 방법인 reduce 고차함수를 생각했고 그것을 활용하였다. 
- 하지만 고차함수를 활용하여 뽑아낸 dictionary에서 최빈값을 뽑아내기 위해선 for문을 생각할 수 밖에 없었고 for문을 최소화로 사용하자는 내 생각과는 달라진 코드를 만들게 되었다. 
- 문제를 푼 후 다른 사람이 푼 코드 중 for문을 사용안한 코드를 찾다가 좋아요가 가장 많은 위의 코드를 확인하였고 Dictionary(grouping: )을 활용하여 배열을 dictionary로 변환하여 문제를 푼 코드였다. 
- 내가 원했던 for문을 사용하지 않고 문제를 푸는 방법의 코드였고 아래의 코드를 분석하던 찰라 두 코드 사이의 시간 복잡도 차이가 궁금해졌다.
##### 첫번째 코드의 시간복잡도 
- 첫 번째 코드는 먼저 배열의 각 요소들을 카운트하여 딕셔너리 형태로 저장한 후, 딕셔너리에서 가장 빈도수가 높은 요소를 찾는 과정을 반복문으로 수행한다. 이 방법의 시간복잡도는 O(n)이라고 생각한다. 
1. reduce(into: \_:) 메서드를 활용해서 시간 복잡도 O(n)
2. dictionary key-value 값 쌍을 순회하는 for-in 루프에서 dictionary 크기만큼 n번 반복. 
3. **최종적으로 전체 시간 복잡도는 O(n)**
##### 두번째 코드의 시간복잡도
1. 두 번째 코드는 Dictionary(grouping: by:) 메서드를 활용하여 시간복잡도 O(n)
2. 그리고, 'sorted' 메서드를 활용하여 그룹화된 요소들을 정려하는 부분의 시간 복잡도는 O(nlogn)이여서 
3. **최종적으로 전체 시간 복잡도는 O(nlogn)이다.**
##### 결과
- 그러므로 코드가 간결하지 못하다고 해서 덜 효율적이라는 생각은 버릴 필요가 있다. 또한 문제의 정답은 정말 많이 다양하다는 것을 알게되었다. 
### 출처(참고문헌) 
- https://school.programmers.co.kr/learn/courses/30/lessons/120812

### 연결문서 
- 

### Tag
- #Algorithm/Programmers/Level0 