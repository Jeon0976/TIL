### 날짜: 2022-11-03 10:00

### 주제: Add new values onto an observable during runtime to emit to subscribers. 
---
### 메모: 
> Observables are a fundamental part of RxSwift, but they're essentially read-only. You may only subscribe to them to get notified of new events they produce.
> A common need when developing apps is to manually add new values onto an observable during runtime to emit to subscribers. What you want is something that can act as both an observable and as an observer. That something is called a Subject.
#### What are subjects? 
- Subjects act as both as **observable and an observer**.
- **PublishSubject**
	- Starts empty and *only emits new elements to subscribers.*
- **BehaviorSubject**
	- Starts with an *initial value* and replays it or the latest element to new subscribers. 
- **ReplaySubject**
	- Initialized with *buffer size* and will maintain a buffer of elements up to that size and replay it to new subscribers.
- **AsyncSubject**
	- Emits *only the last next event* in the sequence, and *only when the subject receives a completed event*. This is a seldom-used kind of subject 
- RxSwift also provides a concept called Relays. RxSwift provides two of these, named **PublishRelay and BehaviorRelay.** These wrap their respective subjects, but only accept and relay next events. **You cannot add a completed or error event onto relays at all, so they’re great for non-terminating sequences.**
#### PublishSubject
- Publish subjects come in handy **when you simply want subscribers to be notified of new events** from the point at which they subscribed, until either they unsubscribe, or the subject has terminated with a completed or error event.
	![[스크린샷 2022-11-03 13.39.25.png|600]]
	- *The first subscriber subscribes after 1 is added to the subject, so it doesn’t receive that event. It does get 2 and 3, though. And because the second subscriber doesn’t join in until after 2 is added, it only gets 3.*
	~~~ swift 
		let subject = PublishSubject<String>()
		
		subject.onNext("1")
		
		let subscriptionOne = subject
			.subscribe(onNext: { string in 
				print("1)", string)
			})
		
		subject.onNext("2")
		
		let subscriptionTwo = subject
			.subscribe(onNext: { string in 
				print("2)", string)
			})
		
		subject.onNext("3")
		
		subscriptionOne.dispose()
		subscriptionTwo.dispose()
		subject.onCompleted()
		// 1) 2
		// 1) 3
		// 2) 3
	~~~
- You might use a publishSubject when you’re modeling time-sensitive data, such as in an online bidding app. It wouldn’t make sense to alert the user who joined at 10:01 am that at 9:59 am there was only 1 minute left in the auction.
#### BehaviorSubject
- Behavior subjects work similarly to publish subjects, except they will replay the latest next event to new subscribers.
	![[스크린샷 2022-11-03 13.50.34.png]]
	-  *The first line at the top is the subject. The first subscriber on the second line down subscribes after 1 but before 2, so it receives 1 immediately upon subscription, and then 2 and 3 when they’re emitted by the subject. Similarly, the second subscriber subscribes after 2 but before 3, so it receives 2 immediately and then 3 when it’s emitted.*
	~~~ swift 
		import RxSwift
		
		let disposeBag = DisposeBag()
		
		let subject = BehaviorSubject(value: "Initial Value")
		
		subject
			.subscribe { 
				print("1. ", $0)
			}
			.disposed(by: disposeBag)
		
		subject.onNext("1")
		
		subject
			.subscribe { 
				print("2. ", $0)
			}
			.disposed(by: disposeBag)
		
		subject.onNext("2")
		
		subject.onCompleted()
		
		// 1. next(Initial Value)
		// 1. next(1)
		// 2. next(1)
		// 1. next(2)
		// 2. next(2)
		// 1. completed
		// 2. completed
	~~~
- Behavior subjects are useful when you want to pre-populate a view with the most recent data. For example, you could bind controls in a user profile screen to a behavior subject, so that the latest values can be used to pre-populate the display while the app fetches fresh data.
- Behavior subjects replay their latest value to new subscribers. This makes them a good choice to model state such as "request is currently loading," or "the time is now 9:41."
#### ReplaySubject 
- What if you wanted to show more than the latest value? For example, on a search screen, you may want to show the most recent five search terms used. This is where replay subjects come in.
- Replay subjects will temporaily cache, or *buffer*, the latest elements they emit, up to a specified size of your choosing. They will then replay that buffer to new subscribers. 	
		![[스크린샷 2022-11-03 16.11.00.png|500]]
	-  *The first subscriber (middle line) is already subscribed to the replay subject (top line) so it gets elements as they’re emitted. The second subscriber (bottom line) subscribes after 2, so it gets 1 and 2 replayed to it.*
> Keep in mind, when using a replay subject, that this buffer is held in memory. You can definitely shoot yourself in the foot here, such as if you set a large buffer size for a replay subject of some type whose instances each take up a lot of memory, like images.
~~~ swift
	let dispseBag = DisposeBag()
	
	let subject = ReplaySubject<String>.create(bufferSize: 3)
	
	subject.onNext("1")
	subject.onNext("2")
	subject.onNext("3")
	
	subject
		.subscribe(onNext: { 
			print("1. ", $0)
		})	
		.disposed(by: disposeBag)
	
	subject.onNext("4")
	
	subject
		.subscribe(onNext : { 
			print("2. ", $0)
		})
		.disposed(by: disposeBag)
	
	subject.onNext("5")
	
	subject.onCompleted()
	
	// 1.  1 
	// 1.  2 
	// 1.  3 
	// 1.  4
	// 2.  2
	// 2.  3
	// 2.  4 
	// 1.  5
	// 2. 5 
~~~
- The replay subject is terminated with an error, which will re-emit to new subscribers — you learned this earlier. But the buffer is also still hanging around, so it gets replayed to new subscribers as well before the stop event is re-emitted.
#### Working with relays
- **You add a value onto a relay by using the accept( _ : ) method. In other words, you don't use onNext(\_:). This is because relays can only accept values, you cannot add an error or completed event onto them.**
- A PublishRelay wraps a PublishSubject and a BehaviorRelay wraps a BehaviorSubject. What sets relays apart from their wrapped subjects it that **they are guaranteed to never terminate.** 
- you must import RxCocoa before using relays 
~~~ swift
		let disposeBag = DisposeBag()
		
		let relay = PublishRelay<String>()
		
		relay.accept("HI, anyone home?")
		
		relay
		    .subscribe(onNext: {
		        print($0)
		    })
		    .disposed(by: disposeBag)
		
		relay.accept("Yes~")
		
		let relay2 = BehaviorRelay<String>(value: "Initial Value")
		
		relay2.accept("New Initial Value")
		
		relay2
		    .subscribe(onNext: {
		        print($0)
		    })
		    .disposed(by: disposeBag)
		
		relay2.accept("HI~")
		
		//Yes~ 
		//New Initial Value
		// HI~
~~~
### 출처(참고문헌) 
- RxSwift. Reactive Programming with Swift
- https://github.com/fimuxd/RxSwift
- https://velog.io/@iammiori/RxSwift-4-4.-AsyncSubject
### 연결문서 
- [[RxSwift)Ch 2. Observables]]
- [[RxSwift)Ch 1. Hello, RxSwift]]

### Tag
- #IOS/RxSwift/Subject/PublishSubject
- #IOS/RxSwift/Subject/BehaviorSubject
- #IOS/RxSwift/Subject/ReplaySubject 
- #IOS/RxSwift/Subject/AsyncSubject
- #IOS/RxSwift/Subject/PublishRelay
- #IOS/RxSwift/Subject/BehaviorRelay    
- #IOS/UIKit 