### 날짜: 2022-11-17 11:37

### 주제: How to combine the data within each sequence.
---
### 메모: 
> RxSwift is all about working with and mastering asynchronous sequences. But you’ll often need to make order out of chaos! There is a lot you can accomplish by combining observables.
#### Prefixing and concatenating
1. **startWith** 
	- When working with observables, the first and most obvious need is to guarantee that an observer receives an initial value. **There are situations where you’ll need the “current state” first. Good use cases for this are “current location” and “network connectivity status.”** These are some observables you’ll want to prefix with the current state.
		![[스크린샷 2022-11-17 11.48.52.png|500]]
	- example Code
		~~~ swift
			let disposeBag = DisposeBag()
			
			let numbers = Observable.of(2,3,4)
			
			numbers
				.startWith(1)
				.subscribe(onNext: { 
					print($0)
				})
				.disposed(by: disposeBag)
				// 1
				// 2
				// 3
				// 4 
		~~~
2. **concat**
	- As it turns out, startWith(\_:) is the simple variant of the more general concat family of operators. Your initial value is a sequence of one element, to which RxSwift appends the sequence that startWith(\_:) chains too. **The Observable.concat(_:) static function chains two sequences.**
		![[스크린샷 2022-11-17 12.03.48.png|500]]
	- example Code
		~~~ swift
			let first = Observable.of(1,2,3)
			let second = Observable.of(4,5,6)
			
			let concat: () = Observable.concat([first,second])
				.subscribe(onNext: { 
					print("$0")
				})
				.disposed(by: disposeBag)
				// 1
				// 2
				// 3
				// 4 
				// 5 
				// 6 
		~~~
#### Merging
1. **merge**
	- A merge() observable subscribes to each of the sequences it receives and emits the elements as soon as they arrive — **there’s no predefined order.**
	- merge() completes after its source sequence completes and all inner sequences have completed. 
	- The order in which the inner sequences complete is irrelevant.
	- If any of the sequences emit an error, the merge() observable immediately relays the error, then terminates.
		![[스크린샷 2022-11-17 13.31.49.png|400]]
	- example Code
		~~~ swift
			let first = Observable.of(1,2,3)
			let second = Observable.of(4,5,6)
			
			let merge: () = Observable.merge(first,second)
				.subscribe(onNext: { 
					print("$0")
				})
				.disposed(by: disposeBag)
				// 1 
				// 4
				// 2 
				// 5 
				// 3 
				// 6 
		~~~
#### Combining elements
1. **combineLatest**
	- Every time one of the inner (combined) sequences emits a value, it calls a closure you provide. You receive the last value emitted by each of the inner sequences. This has many concrete applications, **such as observing several text fields at once and combining their values, watching the status of multiple sources, and so on.**
	![[스크린샷 2022-11-17 13.44.03.png|500]]
	- example Code 
		~~~ swift
			let left = PublishSubject<String>()
			let right = PublishSubject<String>() 
			
			let observable = Observable.combinLatest(left,right) { lastLeft, lastRight in
				"\(lastLeft), \(lastRight)"
			}
			
			_ = observable.subscribe(onNext: { value in
				print(value)
			})
			
			left.onNext("Hello,")
			right.onNext("world")
			right.onNext("RxSwift")
			left.onNext("Have a good day,")
			left.onCompleted()
			right.onCompleted()
			
			// Hello, world
			// Hello, RxSwift
			// Have a good day, Rxswift
		~~~
2. **zip**
	- Subscribed to the observables you provided. 
	- Waited for each to emit a new value.
	- Called your closure with both new values.
	- if no next value from one of the inner observables is available at the next logical position, **zip won't emit anything anymore.**
	![[스크린샷 2022-11-17 14.04.50.png|500]]
	- example Code
		~~~ swift
			enum Weather {
			    case cloudy
			    case sunny
			}
			let left : Observable<Weather> = Observable.of(.sunny,.cloudy,.cloudy,.sunny)
			let right = Observable.of("Lisbon", "Copenhagen", "London", "Madrid", "Vienna")
			let test: () = Observable.zip(left, right) { weather, city in
			    return "It's \(weather) in \(city)"}
				.subscribe(onNext: {
				    print($0)
				})
				.disposed(by: disposeBag)
			// It's sunny in Lisbon
			// It's cloudy in Copenhagen
			// It's cloudy in London
			// It's sunny in Madrid
		~~~
		- *Vienna 출력 불가능*
#### Triggers
1. **withLatestFrom** 
	- withLatestFrom is a useful companion tool when dealing with user interfaces, among other things. 
	- withLatestFrom(\_:) is useful in all situations **where you want the current (latest) value emitted from an observable, but only when a particular trigger occurs.**
	![[스크린샷 2022-11-17 15.46.44.png|500]]
	- example Code
		~~~ swift
			let button = PublishSubject<Void>()
			let textField = PublishSubject<String>() 
			
			let observable = button.withLatestFrom(textField)
				.subscribe(onNext: { 
					print($0)
				})
			
			textField.onNext("Par")
			textField.onNext("Pari")
			textField.onNext("Paris")
			button.onNext(())
			button.onNext(())
			
			// Paris
			// Paris
		~~~
2. **sample**
	- A close relative to withLatestFrom(\_:) is the sample(\_:) operator.
	- It does nearly the same thing with just one variation: each time the trigger observable emits a value, **sample(\_:) emits the latest value from the “other” observable**, but only if it arrived since the last trigger. If no new data arrived, sample(\_:) won’t emit anything.
			![[스크린샷 2022-11-17 15.51.29.png]]
#### Switches
1. **amb**
	- The amb(\_:) operator subscribes to the left and right observables. It waits for any of them to emit an element, then unsubscribes from the other one. **After that, it only relays elements from the first active observable.** It really does draw its name from the term ambiguous: at first, you don’t know which sequence you’re interested in, and want to decide only when one fires.
		![[스크린샷 2022-11-17 15.59.47.png|500]]
	- example Code
		~~~ swift
			let ambRight = PublishSubject<String>()
			let ambLeft = PublishSubject<String>()  
			let ambObservable = ambRight.amb(ambLeft)
			    .subscribe(onNext: {
			        print($0)
			    })
			    .disposed(by: disposeBag)
			ambRight.onNext("Seoul")
			ambLeft.onNext("Lisbon")
			ambLeft.onNext("London")
			ambLeft.onNext("Busan")
			ambRight.onNext("Vienna")
			// Seoul
			// Vienna
		~~~
2. **switchLatest**
	![[스크린샷 2022-11-17 16.03.06.png|500]]
	- flatMapLatest, switchLatest do pretty much the same thing: flatMapLatest maps the latest value to an observable, then subscribes to it. It keeps only the latest subscription active, just like switchLatest.
#### Combining elements within a sequence
1. **reduce**
		![[스크린샷 2022-11-17 16.05.58.png]]
2. **scan**
		![[스크린샷 2022-11-17 16.06.10.png]]
	~~~ swift 
		let source = Observable.of(1,3,5,7,9)
		let reduceObservable: () = source.reduce(0, accumulator: +)
		    .subscribe(onNext: {
		        print("reduce:\($0)")
		    })
		    .disposed(by: disposeBag)
		let scanObservable: () = source.scan(0, accumulator: +)
		    .subscribe(onNext: {
		        print("scan:\($0)")
		    })
		    .disposed(by: disposeBag)
		   // reduce: 25 
		   // scan: 1
		   // scan: 4 
		   // scan: 9 
		   // scan: 16 
		   // scan: 25 
	~~~
### 출처(참고문헌) 
- RxSwift. Reactive Programming with Swift
- https://github.com/fimuxd/RxSwift/blob/master/Lectures/09_Combining%20Operators/Ch9.CombiningOperators.md

### 연결문서 

### Tag
- #IOS/RxSwift/Operators/startWith 
- #IOS/RxSwift/Operators/concat
- #IOS/RxSwift/Operators/merge
- #IOS/RxSwift/Operators/combineLatest
- #IOS/RxSwift/Operators/zip
- #IOS/RxSwift/Operators/withLatestFrom
- #IOS/RxSwift/Operators/sample
- #IOS/RxSwift/Operators/amb
- #IOS/RxSwift/Operators/switchLatest
- #IOS/RxSwift/Operators/reduce
- #IOS/RXSwift/Operators/scan
- #IOS/UIKit 