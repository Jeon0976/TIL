### 날짜: 2023-04-20 13:34

### 주제: A부터 Z까지 직접 구현해보기
---
### 메모: 
#### 1단계) storyboard 없이 UI 구현 (23.04.17 - 23.04.19)
- layout 구성 간 constraint 오류 주의 필요 
- layout 구성을 snapkit이 아닌 기본 UIKit 활용을 적극 고려할 필요성을 느낌
#### 2단계) RxSwift 사용 없이 구현 (23.04.20)
- 기존 UIKit만 활용하여 구현했을 때 Cell 내부 button 클릭이 Cell이 load가 완료되었음에도 불구하고 스크롤로 모든 데이터를 확인 한 후에야 buttton을 클릭이 가능해졌다. 
- 여러 삽질 후 에전에 내가 겪었던 문제로, cell의 content에 add subview되는 것이 아니라 cell view위에 add subview해서 제대로 작동 되지 않았던 것.
- MVC로 SnapKit만 활용하여, 구현
#### 3단계) RxSwift사용하여 MVVM 구현 (23.04.21 - 23.04.23)
- 다른 두 개의 기능이 하나의 기능으로 통해진다면 Observable.merge 이용을 고려해보자.
- bind 내부에 input data, output data의 위치 조정이 필요함 
- API 호출을 다른 기능에서도 호출하게 될 경우를 고려해서 API 호출 기능을 변수로 묶든 함수로 묶는 과정이 필요할 것 같음

### 출처(참고문헌) 
- 

### 연결문서 
- [[4-2 RxSwift 4시간 끝내기 (개인)]]

### Tag
- #IOS/RxSwift 