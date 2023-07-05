### 날짜: 2023-06-13 14:12

### 주제:  너비 우선 탐색의 기본 
---
### 메모: 
#### 너비 우선 탐색 1
- 주어진 간선에서 오름차순으로 인접 정점을 방문하면서 방문 순서를 출력하라.
##### 입력
- 첫째 줄에 정점의 수 _N_ (5 ≤ _N_ ≤ 100,000), 간선의 수 _M_ (1 ≤ _M_ ≤ 200,000), 시작 정점 _R_ (1 ≤ _R_ ≤ _N_)이 주어진다.
- 다음 _M_개 줄에 간선 정보 `_u_ _v_`가 주어지며 정점 _u_와 정점 _v_의 가중치 1인 양방향 간선을 나타낸다. (1 ≤ _u_ < _v_ ≤ _N_, _u_ ≠ _v_) 모든 간선의 (_u_, _v_) 쌍의 값은 서로 다르다.
##### 출력
- 첫째 줄부터 _N_개의 줄에 정수를 한 개씩 출력한다. _i_번째 줄에는 정점 _i_\의 방문 순서를 출력한다. 시작 정점의 방문 순서는 1이다. 시작 정점에서 방문할 수 없는 경우 0을 출력한다.
##### 코드
~~~ swift 
import Foundation 

struct queueArray { 
	private var leftArray = [Int]()
	private var rightArray = [Int]()
	
	var isEmpty: Bool { 
		leftArray.isEmpty && rightArray.isEmpty 
	}
	
	mutating func push(_ value: Int) { 
		rightArray.append(value)
	}
	
	mutating func pop() -> Int { 
		if leftArray.isEmpty { 
			leftArray = rightArray.reversed() 
			rightArray.removeAll() 
		}
		
		return leftArray.popLast()!
	}
}

let testArray = readLine()!.components(separatedBy: " ").map { Int($0)! }

var graph = [[Int]](repeating: [Int](), count: testArray[0] + 1)
var visited = [Int](repeating: 0, count: testArray[0] + 1)

for _ in 0..<testArray[1] { 
	let testCase = readLine()!.components(separatedBy: " ").map { Int($0)! }
	
	graph[testCase[0]].append(testCase[1])
	graph[testCase[1]].append(testCase[0])
}

var depth = 1 

func bfs(start: Int) { 
	var queue = [start]
	var index = 0 
	
	visited[start] = depth
	
	while queue.count > index { 
		let currentNode = queue[index]
		
		for i in graph[currentNode].sorted() { 
			if visited[i] == 0 { 
				depth += 1 
				visited[i] = depth
				queue.append(i)
			}
		}
		index += 1 
	}
}

bfs(start: testArray[2])

print(visited[1...].map { String($0)}.joined(separator: "\n"))
~~~
#### 너비 우선 탐색 2
- 주어진 간선에서 내림차순으로 인접 정점을 방문하면서 방문 순서를 출력하라.
- 입 출력 조건은 1과 같음 
##### 코드 
~~~ swift 
import Foundation 

struct queueArray { 
	private var leftArray = [Int]()
	private var rightArray = [Int]()
	
	var isEmpty: Bool { 
		leftArray.isEmpty && rightArray.isEmpty 
	}
	
	mutating func push(_ value: Int) { 
		rightArray.append(value)
	}
	
	mutating func pop() -> Int { 
		if leftArray.isEmpty { 
			leftArray = rightArray.reversed() 
			rightArray.removeAll() 
		}
		
		return leftArray.popLast()!
	}
}

let testArray = readLine()!.components(separatedBy: " ").map { Int($0)! }

var graph = [[Int]](repeating: [Int](), count: testArray[0] + 1)
var visited = [Int](repeating: 0, count: testArray[0] + 1)

for _ in 0..<testArray[1] { 
	let testCase = readLine()!.components(separatedBy: " ").map { Int($0)! }
	
	graph[testCase[0]].append(testCase[1])
	graph[testCase[1]].append(testCase[0])
}

var depth = 1 

func bfs(start: Int) { 
	var queue = [start]
	var index = 0 
	
	visited[start] = depth
	
	while queue.count > index { 
		let currentNode = queue[index]
		
		for i in graph[currentNode].sorted(by: >) { 
			if visited[i] == 0 { 
				depth += 1 
				visited[i] = depth
				queue.append(i)
			}
		}
		index += 1 
	}
}

bfs(start: testArray[2])

print(visited[1...].map { String($0)}.joined(separator: "\n"))
~~~
#### 너비 우선 탐색 3 
- 모든 노드의 깊이를 출력하라 
- 방문 되지 않는 노드의 깊이는 -1로 출력
- 시작 노드의 깊이는 0이 된다.
~~~ swift 
import Foundation 

struct queueArray { 
	private var leftArray = [(Int,Int)]()
	private var rightArray = [(Int,Int)]()
	
	var isEmpty: Bool { 
		leftArray.isEmpty && rightArray.isEmpty 
	}
	
	mutating func push(_ value: (Int,Int)) { 
		rightArray.append(value)
	}
	
	mutating func pop() -> (Int,Int) { 
		if leftArray.isEmpty { 
			leftArray = rightArray.reversed() 
			rightArray.removeAll() 
		}
		
		return leftArray.popLast()!
	}
}

let testArray = readLine()!.components(separatedBy: " ").map { Int($0)! }

var graph = [[Int]](repeating: [Int](), count: testArray[0] + 1)
var visited = [Bool](repeating: false, count: testArray[0] + 1)
var visitedDepth = [Int](repeating: -1, count: testArray[0] + 1)

for _ in 0..<testArray[1] { 
	let testCase = readLine()!.components(separatedBy: " ").map { Int($0)! }
	
	graph[testCase[0]].append(testCase[1])
	graph[testCase[1]].append(testCase[0])
}

func bfs(_ start: Int) { 
	var queue = queueArray()
	let depth = 0
	
	visited[start] = true
	visitedDepth[start] = depth
	queue.push((start, depth))
	
	while !queue.isEmpty { 
		let value = queue.pop()
		let currentNode = value.0
		let depth = value.1
		
		visitedDepth[currentNode] = depth
		
		for nextNode in graph[currentNode] { 
			if !visited[nextNode] { 
				queue.push((nextNode, depth + 1))
				visited[nextNode] = true
			}
		}
	}
}

bfs(testArray[2])

print(visited[1...].map { String($0) }.joined(separator: "\n") )
~~~
#### 너비 우선 탐색 4
- 오름차순으로 방문하면서, 노드의 깊이 * 노드의 방문 순서 총합을 구하라 
~~~ swift 
import Foundation

struct queueArray {
    private var leftArray = [(Int,Int)]()
    private var rightArray = [(Int,Int)]()
    
    var isEmpty: Bool {
        leftArray.isEmpty && rightArray.isEmpty
    }
    
    mutating func push(_ value: (Int,Int)) {
        rightArray.append(value)
    }
    
    mutating func pop() -> (Int,Int) {
        if leftArray.isEmpty {
            leftArray = rightArray.reversed()
            rightArray.removeAll()
        }
        
        return leftArray.popLast()!
    }
}


let testArray = readLine()!.components(separatedBy: " ").map { Int($0)! }

var graph = [[Int]](repeating: [Int](), count: testArray[0] + 1)
var visited = [Bool](repeating: false, count: testArray[0] + 1)
var visitedDepth = [Int](repeating: -1, count: testArray[0] + 1)

var visitedOrder = [Int](repeating: 0, count: testArray[0] + 1)

for _ in 0..<testArray[1] {
    let testCase = readLine()!.components(separatedBy: " ").map { Int($0)! }

    graph[testCase[0]].append(testCase[1])
    graph[testCase[1]].append(testCase[0])
}

func bfs(_ start: Int) {
    var count = 0
    var queue = queueArray()
    
    visited[start] = true
    queue.push((start,0))
    
    
    while !queue.isEmpty  {
        count += 1
        let value = queue.pop()
        let currentNode = value.0
        let depth = value.1
        
        visitedOrder[currentNode] = count
        visitedDepth[currentNode] = depth
        
        for nextNode in graph[currentNode].sorted() {
            if !visited[nextNode] {
                queue.push((nextNode, depth + 1))
                visited[nextNode] = true
            }
        }
    }
}
 
bfs(testArray[2])

var result = 0

for i in 1...testArray[0] {
   result += visitedDepth[i] * visitedOrder[i]
}

print(result)
~~~

### 출처(참고문헌) 
- https://www.acmicpc.net/problem/24444
- https://www.acmicpc.net/problem/24445
- https://www.acmicpc.net/problem/24446
- https://www.acmicpc.net/problem/24447

### 연결문서 
- [[25. Breadth-First Search]]

### Tag
- #Algorithm/BaekJoon/Silver 
- #Algorithm/BFS 
- #Algorithm/Graph 