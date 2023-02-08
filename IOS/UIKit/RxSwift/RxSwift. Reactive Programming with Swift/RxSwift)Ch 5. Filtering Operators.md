### 날짜: 2022-11-10 10:59

### 주제: Learning a new technology stack is a bit like building a skyscraper.
---
### 메모: 
> You can use to apply conditional constraints to emitted events so that the subscriber only receives the elements it wants to deal with.
#### Ignoring operators
1. **ignoreElemets**
	- ignoreElements will ignore *all next events*. It will, however, allow stop events through, such as completed or error events.
	![[스크린샷 2022-11-10 13.23.19.png|400]]
	- example Code
		~~~ swift
			let strikes = PublishSubject<String>()
			let disposeBag = DisposeBag()
			
			strikes 
				.ignoreElements()
				.subscribe { _ in 
					print("Out!!")
				}
				.disposed(by: disposeBag)
			
			strikes.onNext("X")
			strikes.onNext("X")
			strikes.onNext("X")
			strikes.onCompleted()
			// Out!!
		~~~
		- *The ignoreElements operator is useful when you only want to be notified when an observable has terminated, via a completed or error event.*
2. **elementAt**  -> element(at: ) 사용 권장
	- elementAt *takes the index of the element* you want to receive, and ignores everything else.
	- In the marble diagram, elementAt is passed an index of 1, so it only lets through the second element.
	![[스크린샷 2022-11-10 13.38.15.png]]
	- example Code
		~~~ swift
			let strikes = PublishSubject<String>()
			let disposeBag = DisposeBag
			
			strikes
				.element(at: 2)
				.subscribe(onNext: { _ in 
					print("Out!")
				})
				.disposed(by: disposeBag) 
			
			strikes.onNext("X")
			strikes.onNext("X")
			strikes.onNext("X")
			// Out!
		~~~
3. **filter** 
	- It takes a predicate closure and applies it to every element emitted, allowing through only those elements for *which the predicate resolves to true*.
	- Check out this marble diagram, where only 1 and 2 are let through because the filter’s predicate only allows elements that are less than 3.
	![[스크린샷 2022-11-10 13.55.09.png]]
	- example Code
		~~~ swift
			Observable.of(1,2,3,4,5,6,7)
				.filter { $0.isMultiple(of: 2)}
				.subscribe(onNext: { 
					pirnt($0)
				})
				.disposed(by: disposeBag)
			// 2
			// 4
			// 6
		~~~
#### Skipping operators
1. **skip**
	- It lets you ignore the first n elements, *where n is the number you pass as its parameter.*
	- This marble diagram shows skip is passed 2, so it ignores the first 2 elements.
	![[스크린샷 2022-11-10 14.00.10.png]]
	- example Code
		~~~ swift
			let dispseBag = DisposeBag() 
			
			Observable.of("A","B","C","D","E","F")
				.skip(3)
				.subscribe (onNext: { 
					print($0)
				})
				.disposed(by: disposeBag)
				
				// D
				// E 
				// F 
		~~~
		*After skipping the first 3 elements, only D, E, and F are printed*
2. **skipWhile** -> skip(while: ) 사용 권장
	- There’s a small family of skip operators. Like filter, skipWhile lets you include a predicate to determine what is skipped.
	- However, unlike filter, which filters elements for the life of the subscription, *skipWhile only skips up until something is not skipped, and then it lets everything else through from that point on.*
	- In this marble diagram, 1 is prevented because 1 % 2 equals 1, but then 2 is allowed through because it fails the predicate, and 3 (and everything else going forward) gets through because skipWhile is no longer skipping.
	![[스크린샷 2022-11-10 14.43.20.png]]
	- example Code
		~~~ swift
			Observable.of(2,4,3,4)
			    .skip(while: {$0.isMultiple(of: 2)})
			    .subscribe(onNext: {
			        print($0)
			    })
			    .disposed(by: disposeBag)
			// 3
			// 4
		~~~ 
		- *if you are developing an insurance claims app, you could use skipWhile to deny coverage until deductible is met.*
3. **skipUntil** -> skip(until: ) 사용 권장 
	- So far, you've filtered based on a static condition. *What if you wanted to dynamically filter elements based on another observable?* 
	- skipUntil will keep skipping elements from the source observable - the one you're subscribing to - *until some other observable emits*.
	- In this marble diagram, skipUntil ignores elements emitted by the source observable on the top line until the trigger observable on second line emits a next event. Then it stops skipping and lets everything through from that point on.
	![[스크린샷 2022-11-10 14.54.44.png]]
	- example Code
		~~~ swift
		let disposeBag = DisposeBag
		
		let subject = PublishSubject<String>()
		let triiger = PublishSubject<String>()
		
		subject
		    .skip(until: trigger)
		    .subscribe(onNext:  {
		        print($0)
		    })
		    .disposed(by: disposeBag)
		    
		subject.onNext("A")
		subject.onNext("B")
		trigger.onNext("X")
		subject.onNext("C")
		// C
		~~~
#### Taking operators
- Taking is the opposite of skipping. When you want to take elements, RxSwift has you covered. 
1. **take**
	- This marble diagram depicts, will take the first of the number of elements you specified.
	![[스크린샷 2022-11-10 15.32.36.png]]
	- example Code
		~~~ swift
			let disposeBag = DisposeBag
			
			Observable.of(1,2,3,4,5,6)
				.take(3)
				.subscribe(onNext: { 
					print($0)
				})
				.disposed(by: disposeBag)
			
			// 1
			// 2
			// 3
		~~~
2. **takeWhile** -> take(while: ) 사용 권장 
	- The takeWhile operator works similarly to skipWhile, except you’re taking instead of skipping
	- Additionally, *If you want to use takeWhile, you must use the enumerated operator*. 
	![[스크린샷 2022-11-10 15.41.15.png]]
	- example Code
		~~~ swift
			let disposeBag = DisposeBag()
			
			Observable.of(2,2,3,4,4,6,6)
			    .enumerated()
			    .take(while: { index, integer in
			        integer.isMultiple(of: 2) && index < 4
			    })
			    .map(\.element)
			    .subscribe(onNext: {
			        print($0)
			    })
			    .disposed(by: disposeBag)
		// 2
		// 2 
		~~~
3. **takeUntil**
	- Conversely to takeWhile, there is a takeUntil operator that will take elements until the predicate is met. It also takes a behavior argument for its first parameter that specifies if you want to include or exclude the last element matching the predictate.
	![[스크린샷 2022-11-10 15.48.25.png]]
	- example Code
		~~~ swift
			let disposeBag = DisposeBag()
			
			Observable.of(1,2,3,4,5)
				.takeUntil(.inclusive) { $0.isMultiple(of: 4)}
				.subscribe(onNext: { 
					print($0)
				})
				.disposed(by: disposeBag)
			// 1
			// 2
			// 3
			// 4 
			// if use exclusive -> 1, 2, 3
		~~~
	- takeUntil trigger 
		~~~ swift 
			// 1
			let subject = PublishSubject<String>()
			let trigger = PublishSubject<String>()
			// 2
			subject
			    .take(until: trigger)
			    .subscribe(onNext: {
			        print($0) })
			    .disposed(by: disposeBag)
			// 3
			subject.onNext("1")
			subject.onNext("2")
			trigger.onNext("X")
			subject.onNext("3")
			// 1 
			// 2
		~~~
		- *The X stops the taking, so 3 is not allowed through and nothing more is printed.*
	- There is a way to use takeUntil with an API from the RxCocoa library to dispose of a subscription, instead of adding it to a dispose bag.
		~~~ swift
			_ = someObservable 
				.takeUntil(self.rx.deallocated)
				.subscribe(onNext: { 
					print($0)
				})
		~~~
#### Distinct operators
- Distinct operators let you prevent duplicate contiguous items from getting through. 
1. **distinctUntilChanged**
	- distinctUntilChanged only prevents duplicates that are *right next to each other*
	![[스크린샷 2022-11-10 16.09.48.png]]
	- example Code
		~~~ swift 
			let disposeBag = DisposeBag()
			
			Observable.of("A","A","B","B","A")
				.distinctUntilChanged()
				.subscribe(onNext: { 
					print($0)
				})
				.disposed(by: disposeBag)
			
			// A
			// B 
			// A
		~~~
		- *The distinctUntilChanged operator only prevents contiguous duplicates, so the second A and second B are prevented because they are equal to their previous element. However, the third A is allowed through because it is not equal to its previous element.*
### 출처(참고문헌) 
- RxSwift. Reactive Programming with Swift
- https://github.com/fimuxd/RxSwift
### 연결문서 
- 
### Tag
- #IOS/RxSwift/Operators/ignoreElements
- #IOS/RxSwift/Operators/elementAt
- #IOS/RxSwift/Operators/filter
- #IOS/RxSwift/Operators/skipWhile
- #IOS/RxSwift/Operators/skipUntil
- #IOS/RxSwift/Operators/distinctUntilChanged
- #IOS/UIKit 