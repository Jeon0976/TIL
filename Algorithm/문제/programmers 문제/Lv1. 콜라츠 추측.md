### 날짜: 2023-03-16 22:48

### 주제:  문제 푸는 방식을 좀 더 Swift 스럽게 (삼항 연산자 사용 & 함수 생성)
---
### 메모: 
#### 문제 
937년 Collatz란 사람에 의해 제기된 이 추측은, 주어진 수가 1이 될 때까지 다음 작업을 반복하면, 모든 수를 1로 만들 수 있다는 추측입니다. 작업은 다음과 같습니다. 

```
1-1. 입력된 수가 짝수라면 2로 나눕니다. 
1-2. 입력된 수가 홀수라면 3을 곱하고 1을 더합니다. 
2. 결과로 나온 수에 같은 작업을 1이 될 때까지 반복합니다. 
```

예를 들어, 주어진 수가 6이라면 6 → 3 → 10 → 5 → 16 → 8 → 4 → 2 → 1 이 되어 총 8번 만에 1이 됩니다. 위 작업을 몇 번이나 반복해야 하는지 반환하는 함수, solution을 완성해 주세요. 단, 주어진 수가 1인 경우에는 0을, 작업을 500번 반복할 때까지 1이 되지 않는다면 –1을 반환해 주세요. 
##### 제한 사항
-   입력된 수, `num`은 1 이상 8,000,000 미만인 정수입니다. 
#### 기존 문제 풀이 방식
~~~ swift 
func solution(_ num:Int) -> Int {
    if num == 1 {return 0}

    var calNum = num
    var count:Int = 0

    while calNum != 1 {
        count += 1 
        if calNum % 2 == 0 {
            calNum = calNum / 2
        } else if calNum % 2 == 1 {
            calNum = calNum * 3 + 1
        }
    }
    if count > 500 { 
        return -1
    }
    return count
}
~~~
- 기존 푼 방식의 코드를 보면 직관적이지 못함 
#### 개선된 코드 문제 풀이 방식
~~~ swift
func odd(_ n: Int) -> Int { 
	return n * 3 + 1 
}

func even(_ n: Int) -> Int { 
	return n / 2 
}

func solution(_ num: Int) -> Int { 
	var calNum = num
	var count = 0 
	
	while calNum != 1 && count < 500 { 
		calNum = calNum % 2 == 0 ? even(calNum) : odd(calNum)
		count += 1
	}
	return count >= 500 ? -1 : count 
}
~~~
### 출처(참고문헌) 
- https://school.programmers.co.kr/learn/courses/30/lessons/12943

### 연결문서 
- 

### Tag
- #Algorithm/Programmers/Level1  
