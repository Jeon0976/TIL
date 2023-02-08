### 날짜: 2022-12-06 17:59

### 주제:  SwiftUI, 상태와 데이터의 흐름 
---
### 메모: 
- **@State** 
	- View State
	- String, Int, Bool과 같은 *간단한 값을 저장*하고 *View의 현재 상태를 표시*하기 위해 사용.
	- 단순히 화면에 표시하기 위한 수단으로 사용하면 좋다. 
	- 그러므로 정의할 때 private로 설정한다.
	~~~ swift
		import SwiftUI
		
		struct Sample: View { 
			@State private var currentText = ""
			@State private var isDisabled = true
			
			var body: some View { 
				VStack { 
					TextField("Plese input the text", text: $currentText)
					Text(currentText)
					Toggle(isOn: $isDisabled, label: {Text("button is Disabled")})
					Button("Button") { }
								.disabled(isDisabled)
				}
			}
		}
	~~~
	- *TextField에서 값을 입력하면 Text에서도 같이 변경 됨.*
	- *Toggle값을 false하면 button이 활성화 됨.*
- **@Binding** 
	- *ChildView에서 ParentView의 값을 표시*하고, 능동적으로 값이 변화할 때 사용. 
	- ChildView에서 사용됨.
	~~~ swift
		import SwiftUI
		
		struct ParentView: View { 
			@State private var isDisabled = true
			var body: some View { 
				ChildView(isDisabled: $isDisabled)
			}
		}
		
		struct ChildView: View { 
			@Binding var isDisabled: Bool
			
			var body: some View { 
				VStack { 
					Toggle(isOn: $isDisabled, label: {Text("Button is Disabled")})
					Button("Button") { } 
								.disabled(isDisabled)
				}
			}
		}
		
		struct Sample_Previews: PreviewProvider { 
			static var previews: some View { 
				ParentView()
			}
		}
	~~~
	- ParentView에서 Bool값을 저장할 State를 생성
	- ChildView에서 Binding으로 ParentView의 State 값을 읽어드림 
- **@ObservedObject**
	- Model Data
	- ObservableObject를 class 모댈로 선언하면 어떤 struct view이든 ObservedObject 선언으로 구독할 수 있음 
	~~~ swift 
		import SwiftUI
		
		class ButtonModel: ObservableObject {
			@published var isDisabled = true
		} 
		
		struct ParentView: View { 
			@ObservedObject var buttonModel = ButtonModel()
			var body: some View { 
				ChildView(isDisabled: $buttonModel.isDisabled)
			}
		}
		
		struct ChildView: View { 
			@Binding var isDisabled: Bool
			
			var body: some View { 
				VStack { 
					Toggle(isOn: $isDisabled, label: {Text("Button is Disabled")})
					Button("Button") { } 
								.disabled(isDisabled)
				}
			}
		}
		
		struct Sample_Previews: PreviewProvider { 
			static var previews: some View { 
				ParentView()
			}
		}	
	~~~
	- ObservableObject 에서 published로 isDisabled 초기값을 true로 선언
	- ObservedObject를 사용하여 published로 선언한 값을 구독하여 활용할 수 있음
### 출처(참고문헌) 
- 패스트켐퍼스 ios 앱 개발 5-3

### 연결문서 
- 
### Tag
- #IOS/SwiftUI/State
- #IOS/SwiftUI/Binding 
- #IOS/SwiftUI/ObservedObject