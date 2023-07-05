### 날짜: 2023-06-13 14:20

### 주제:  깊이 우선 탐색의 기본
---
### 메모: 
#### 깊이 우선 탐색 1 
- 노드의 방문 순서를 출력하며 시작 노드는 1, 시작 정점에서 방문할 수 없는 경우 0을 출력
~~~ swift 
import Foundation

let testCase = readLine()!.components(separatedBy: " ").map { Int($0)! }

var graph = [[Int]](repeating:[Int](),count: testCase[0] + 1)
var visited = [Int](repeating: 0, count: testCase[0] + 1)

for _ in 0..<testCase[1] {
    let testArray = readLine()!.components(separatedBy: " ").map { Int($0)! }
    let n = testArray[0]
    let m = testArray[1]
    
    graph[n].append(m)
    graph[m].append(n)
}

var order = 1

var start = testCase[2]

func dfs(start: Int) {
    visited[start] = order
    
    for i in graph[start].sorted() {
        if visited[i] == 0 {
            order += 1
            dfs(start: i)
        }
    }
}

dfs(start: start)

print(visited[1...].map { String($0) }.joined(separator: "\n"))
~~~
#### 깊이 우선 탐색 2 
- 깊이 우선 탐색 1과 같으며, 내림차순으로 출력
~~~ swift 
import Foundation

let testCase = readLine()!.components(separatedBy: " ").map { Int($0)! }

var graph = [[Int]](repeating:[Int](),count: testCase[0] + 1)
var visited = [Int](repeating: 0, count: testCase[0] + 1)

for _ in 0..<testCase[1] {
    let testArray = readLine()!.components(separatedBy: " ").map { Int($0)! }
    let n = testArray[0]
    let m = testArray[1]
    
    graph[n].append(m)
    graph[m].append(n)
}

var order = 1

var start = testCase[2]

func dfs(start: Int) {
    visited[start] = order
    
    for i in graph[start].sorted(by: >) {
        if visited[i] == 0 {
            order += 1
            dfs(start: i)
        }
    }
}

dfs(start: start)

print(visited[1...].map { String($0) }.joined(separator: "\n"))
~~~
#### 깊이 우선 탐색 3
- 노드의 깊이를 출력, 시작 정점의 깊이는 0이고 방문 되지 않는 노드의 깊이는 -1
~~~ swift 
import Foundation

let testCase = readLine()!.components(separatedBy: " ").map { Int($0)! }

let N = testCase[0]
let M = testCase[1]
let start = testCase[2]

var graph = [[Int]](repeating: [Int](), count: N+1)
var result = [Int](repeating: -1, count: N+1)

for _ in 0..<M {
    let testArray = readLine()!.components(separatedBy: " ").map { Int($0)! }
    let from = testArray[0]
    let to = testArray[1]
    
    graph[from].append(to)
    graph[to].append(from)
}

var depth = 0

func dfs(_ start: Int, _ depth: Int) {
    result[start] = depth
    
    for i in graph[start].sorted() {
        if result[i] == -1 {
            dfs(i, depth + 1)
        }
    }
}

dfs(start, depth)

print(result[1...].map { String($0) }.joined(separator: "\n") )
~~~
#### 깊이 우선 탐색 4
- 깊이 우선 탐색 3의 내림차순
~~~ swift 
import Foundation

let testCase = readLine()!.components(separatedBy: " ").map { Int($0)! }

let N = testCase[0]
let M = testCase[1]
let start = testCase[2]

var graph = [[Int]](repeating: [Int](), count: N+1)
var result = [Int](repeating: -1, count: N+1)

for _ in 0..<M {
    let testArray = readLine()!.components(separatedBy: " ").map { Int($0)! }
    let from = testArray[0]
    let to = testArray[1]
    
    graph[from].append(to)
    graph[to].append(from)
}

var depth = 0

func dfs(_ start: Int, _ depth: Int) {
    result[start] = depth
    
    for i in graph[start].sorted(by: >) {
        if result[i] == -1 {
            dfs(i, depth + 1)
        }
    }
}

dfs(start, depth)

print(result[1...].map { String($0) }.joined(separator: "\n") )
~~~

### 출처(참고문헌) 
- https://www.acmicpc.net/problem/24479
- https://www.acmicpc.net/problem/24480
- https://www.acmicpc.net/problem/24481
- https://www.acmicpc.net/problem/24482

### 연결문서 
- [[26. Depth-First Search]]

### Tag
- #Algorithm/BaekJoon/Silver 
- #Algorithm/DFS 
- #Algorithm/Graph 