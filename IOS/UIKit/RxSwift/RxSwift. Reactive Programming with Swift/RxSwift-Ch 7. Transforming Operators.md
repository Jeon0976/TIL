### 날짜: 2022-11-13 16:06

### 주제: RxSwift is magic! 
---
### 메모: 
> Transforming Operators are one of the most important categories of operators
#### Transforming elements
1. **toArray**
	- Observables emit elements individually, but you will frequently want to work with collections, such as when you're binding an observable to a table or collection view
	- A convenient way to transform an observable of individual elements into an array of all those elements is by using toArray. 
	- The toArray operator returns a Single. 
	![[스크린샷 2022-11-13 16.51.42.png|400]]
	- example Code 
		~~~swift 
			let disposeBag = DisposeBag()
			
			Observable.of("A", "B", "C")
				.toArray()
				.subscribe(onSuccess: { 
					print($0)
				})
				.disposed(by: disposeBag)
				// ["A", "B", "C"]
		~~~
2. **map**
	- RxSwift's map operator works just like Swift's standard map, except is on observables. 
	![[스크린샷 2022-11-13 16.54.52.png|400]]
	- example Code
		~~~ swift 
			let disposeBag = DisposeBag()
			let formatter = NumberFormatter()
			formatter.numberStyle = .spellout
			
			Observable<Int>.of(1, 2, 3)
				.map { 
					formatter.string(for: $0) ?? "" 
				}
				.subscribe(onNext : { 
					print($0)
				})
				.disposed = (by: disposeBag)
				// one 
				// two
				// three 
		~~~
3. **compactMap**
	- The compactMap operator is a combination of the map and filter operators that specifically filters out nil values, similar to its counterpart in the Swift standard library.
	- example Code
		~~~ swift
			let  disposeBag = DisposeBag()
			
			Observable.of("To", "be", nil, "or", "not", "To", "be", nil)
				.compactMap { $0 }
				.toArray()
				.map { $0.joined(separator: " ")}
				.subscribe(onSuccess: { 
					print($0)
				}) 
				.disposed(by: disposeBag)
				// To be or not To be
		~~~
#### Transforming inner observables 
1. **flatMap** 
	- Projects each element of an observable sequence to an observable sequence and merges the resulting observable sequences into one observable sequence.
		![[스크린샷 2022-11-14 13.30.59.png|500]]
	- flatMap은 한 번에 여러 스트림을 사용할 수 있으며, 여러 스트림에서 방출된 아이템에 대해 누락 없이 구독이 가능하다. 
	- example Code
		~~~ swift
			struct Student { 
				let score: BehaviorSubject<Int>
			}
			
			let disposeBag = DisposeBag()
			
			let laura = Student(score: BehaviorSubject(value: 80))
			let charlotte = Student(score: BehaviorSubject(value: 90))
			
			let student = PublishSubject<Student>()
			
			student
				.flatMap { 
					$0.score
				}
				.subscribe(onNext: { 
					print($0)
				})
				.disposed(by: disposeBag)
			
			student.onNext(laura)
			laura.score.onNext(85)
			student.onNext(charlotte)
			charlotte.score.onNext(95)
			charlotte.score.onNext(100)
			laura.score.onNext(110)
			
			// 80
			// 85
			// 90
			// 95
			// 100
			// 110
		~~~
		- *한 번 구독하면 중간에 다른 Observable을 구독 해도 기존 Observable의 최신 값이 들어 올 때 그 값을 계속 방출 할 수 있음*
	- flatMap의 이러한 특성으로 Map을 활용하여 두 개 이상의 Observable을 합칠때 유용하게 사용할 수 있다. 
	- example Code
		~~~ swift
			let observable1 = Observalbe.of("A","B","C")
			let observable2 = Observable.of("a","b","c")
			observable1.
				.flatMap { observable1 -> Observable<String> in 
					return observable2.map{observable1 + $0}
				}
				.subscribe(onNext: { 
					print($0)
				})
				.disposed(by:disposeBag)
				// Aa
				// Ab
				// Ba
				// Ac
				// Bb
				// Ca 
				// Bc
				// Cb
				// Cc
		~~~
		*위 결과를 보면 알겠지만 flatMap은 순서를 보장하지 않는다. 
		merge로 두 observable을 합쳤을 경우 결과는 'A a B b C c' 이고
		flatMapLatest로 두 observable을 합쳤을 경우 결과는 'Aa Ba Ca Cb Cc'이다.*
2. **flatMapLatest**
	- To recap, flatMap keeps projecting changes from each observable. However, **when you only want to keep up with the latest element in the source observable**, use the flatMapLatest operator.
	- The flatMapLatest operator is actually a combination of two operators: map and switchLatest. 
	![[스크린샷 2022-11-14 14.18.24.png|500]]
	- example Code 
		~~~ swift
			struct Student { 
				let score: BehaviorSubject<Int>
			}
			
			let disposeBag = DisposeBag()
			
			let laura = Student(score: BehaviorSubject(value: 80))
			let charlotte = Student(score: BehaviorSubject(value: 90))
			
			let student = PublishSubject<Student>()
			
			student
				.flatMapLatest { 
					$0.score
				}
				.subscribe(onNext: { 
					print($0)
				})
				.disposed(by: disposeBag)
			
			student.onNext(laura)
			laura.score.onNext(85)
			student.onNext(charlotte)
			charlotte.score.onNext(95)
			charlotte.score.onNext(100)
			laura.score.onNext(110)
			// 80
			// 85
			// 90
			// 95
			// 100
			// laura의 최신값을 반영 안됨.
		~~~
#### Observing events
1. **materialize** 
2. **dematerialize**
### 출처(참고문헌) 
- RxSwift. Reactive Programming with Swift
- https://github.com/fimuxd/RxSwift
- https://velog.io/@horeng2/Swift-RxSwift-map-flatMap의-차이점과-용도
### 연결문서 
### Tag
- #IOS/RxSwift/Operators/toArray
- #IOS/RxSwift/Operators/map 
- #IOS/RxSwift/Operators/compactMap 
- #IOS/RxSwift/Operators/flatMap
- #IOS/RxSwift/Operators/flatMapLatest 
- #IOS/UIKit 