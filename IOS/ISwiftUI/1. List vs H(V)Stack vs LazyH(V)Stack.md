### 날짜: 2022-12-04 15:16

### 주제: List vs Stack vs LazyStack
---
### 메모: 
> 사용법이 아닌 특징으로 구분짓는 가장 큰 것은  **Reuse**의 사용 유무이다.
##### H(V)Stack 
- 초기화 시점에 *모든 View를 생성함. (유저가 안 보이는 화면도 마찬가지)*
~~~ swift 
	struct SampleHStack: View { 
		var body: some View { 
			ScrollView(.horizontal) { 
				HStack { 
					 Text("How to use Hstack")
					 Text("How to use Hstack")
					 Text("How to use Hstack")
					 Text("How to use Hstack")
				}
			}
		}
	}
	
	struct SampleHStack_previews: PreviewProvider { 
		static var previews: some View { 
			SampleHStack()
		}
	}
~~~
- 많은 수의 컨텐츠를 구현하려고 하면 메모리 무리가 갈 수 있음 
##### LazyH(V)Stack / LazyHGrid
- 초기화 시점에 모든 Cell을 생성하지 않음. 
- *최대 index 31까지의 데이터의 Cell(View)를 생성.*
- Reuse를 기본으로 사용함
~~~ swift 
struct SampleHLazyStack: View { 
	struct Number: Identifiable { 
		let value: Int
		var id: Int { value }
	}
	
	let numbers: [Number] = (0...100).map { 
		Number(value: $0)
	}
	
	var body: some View { 
		ScrollView(.horizontal) { 
			LazyHGrid(rows: 
										[GridItem(.fixed(15)), 
										GridItem(.fixed(15)),
										GridItem(.fixed(15))]
										) {
				ForEach(numbers) { number in 
					Text("\(number.value)")
				}
			}
		}
	}
}
struct SampleHLazyStack_previews: PreviewProvider { 
	static var previews: some View { 
		SampleHLazyStack()
	}
}
~~~
##### List 
- 초기화 시점에 모든 Cell을 생성하지 않음.
- UITableView와 비슷함. *보여질 필요가 있는 Cell(View)만 생성.*
- **Cell의 삭제/추가 기능이 있음.**
~~~ swift 
struct SampleList: View { 
	struct Number: Idntifiable { 
		let value: Int 
		var id: Int {value}
	}
	
	let numbers: [Number] = (0...100).map { Nmber(value:$0)}
	
	var body: some View { 
		List { 
			Section(header: Text("First Header")) { 
				ForEach(numbers) { number in 
					Text("\(number.value)")
				}
			Section(header: Text("First Header")) { 
				ForEach(numbers) { number in 
					Text("\(number.value)")
				}
			}
		}
	}
}

struct SampleList_Previews: PreviewProvider { 
											 static var previews: some View { 
												 SampleList()
											 }
											}
~~~
### 연결문서 


### Tag
- #IOS/SwiftUI/Stack 
- #IOS/SwiftUI/Grid
- #IOS/SwiftUI/List