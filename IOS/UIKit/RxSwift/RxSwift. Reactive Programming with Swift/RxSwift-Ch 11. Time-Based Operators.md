### 날짜: 2022-11-19 20:35

### 주제: Timing is everything.
---
### 메모: 
#### Buffering operators
- They will either replay past elements to new subscribers or buffer them and deliver them in bursts. 
- **They allow you to control how and when past and new elements get delivered**
##### Replaying past elements
- When a sequence emits items, **you'll often need to make sure that a future subscriber receives some or all of the past items.** This is the purpose of the replay(\_:) and replayAll() operators.
- **If you use replay, replayAll. You must use a connect operator.** 
-  **replay**
	- example Code
		~~~ swift
			let disposeBag = DisposeBag()
			
			let hello = PublishSubject<String>()
			let repeating = hello.replay(2)
			
			repeating.connect()
			
			hello.onNext("1")
			hello.onNext("2")
			hello.onNext("3")
			hello.onNext("4")
			
			repeating
				.subscribe(onNext: { 
					print($0)
				})
				.disposed(by: disposeBag)
			
			hello.onNext("5")
			// 3 
			// 4 
			// 5 
		~~~
		*replay 2로 인해 subscribe한 시점에서 두 개 과거 onNext 실시하며, 다음 내용도 onNext 실시*
##### Unlimited replay
- **replayAll**
	- The second replay operator you can use is replayAll(). This one should be used with caution. **only use it in scenarios where you know the total number of buffered elements will stay reasonable.** 
	- For example, it's the appropriate memory impact of retaining the data returned by a query. 
	- On the other hand, using replayAll() on a sequence that **may not terminate and may produce a lot of data will quickly clog your memory.** This could grow to the point where the OS jettisons your application.
	- It is used to keep a cache of the emissions from a specific observable sequence and replay them to new subscribers. It can use up a large amount of memory if the emissions are large or the number of subscribers is high. 
	- Therefore, It's recommended to use replayAll with caution, and only in cases where it's absolutely necessary. 
	- **This can be useful when you want to ensure that new subscribers receive all of the previous emissions, even if they subscribed after the sequence has completed or produced an error.**
	- example Code
		~~~ swift
			let disposeBag = DisposeBag()
			
			let hello = PublishSubject<String>()
			let repeating = hello.replayAll
			
			repeating.connect()
			
			hello.onNext("1")
			hello.onNext("2")
			hello.onNext("3")
			hello.onNext("4")
			
			repeating
				.subscribe(onNext: { 
					print($0)
				})
				.disposed(by: disposeBag)
			
			hello.onNext("5")
			
			// 1 
			// 2 
			// 3 
			// 4 
			// 5 
		~~~
##### Controlled buffering
- **buffer**
	- buffer(timeSpan: count: scheduler: ) 
		- timeSpan: 시간의 최대값
		- count: 갯수의 최대값 
		- scheduler: 작동 방식
	![[스크린샷 2022-11-29 14.15.14.png|500]]
	- example Code 
		~~~ swift 
			let source = PublishSubject<String>()
			
			var count = 0 
			let timer = DispatchSource.makeTimerSource() // Timer 만들기 
			
			// 시작 후 5초뒤 타이머 작동, 1초마다 반복
			timer.schedule(deadline: .now() + 5, repeating: .seconds(1)) 
			timer.setEventHandler { 
				count += 1 
				source.onNext("\(count)")
			}
			timer.resume()  // 지속 반복을 위한 조치
			
			// buffer 최대 시간 2초, 최대 갯수 5개, 메인스케쥴러에서 작동
			source 
				.buffer(timeSpan: .seconds(2), 
							count: 5,
							scheduler: MainScheduler.instance)
				.subscribe(onNext: { 
					print($0)
				})
				.disposed(by: disposeBag)
				
				// []
				// [] 
				// ["1", "2"]
				// ["3", "4"]
				// ["5", "6"]
				// .....
		~~~
##### Windows of buffered observables
- **window**
	- A last buffering technique very close to buffer(timeSpan:count:scheduler:) is window(timeSpan:count:scheduler:). **It has roughly the same signature and does nearly the same thing. The only difference is that it emits an Observable of the buffered items, instead of emitting an array.**
	- buffer는 array를 방출하는 대신에, window는 observable를 방출함.
	![[스크린샷 2022-11-29 14.27.30.png|500]]
	- example Code
		~~~ swift 
			let maxObservable =  5
			let makingTime = RxTimeInterval.seconds(5)
			
			let window = PublishSubject<String>()
			
			var windowCount = 0 
			let windowTimeSource = DispatchSource.makeTimeSource()
			windowTimeSource.schedule(deadline = .now() + 2, repeating: .second(1))
			windowTimeSource.setEventHandler { 
				windowCount +=  1
				window.onNext("\(windowCount)")
			}
			windowTimeSource.resume()
			
			window
				.window(timeSpan: makingTime,
								count: maxObservable, 
								scheduler: MainScheduler.instance)
				// Observable Type을 flatMap을 활용하여 index와 element로 나눠서 방출 
				// 만약 flatMap 사용 없이 print할 경우 
				// RxSwift.AddRef<Swift.String> 라는 값 방출
				.flatMap { windowObservable -> Observable<(index: Int, element: String)> in 
					return windowObservable.enumerated()
				}
				.subscribe(onNext: { 
					print($0)
				})
				.disposed(by: disposeBag) 
				// (index: 0, element: "1")
				//(index: 1, element: "2")
				//(index: 2, element: "3")
				//(index: 3, element: "4")
				//(index: 0, element: "5")
				//(index: 1, element: "6")
				//(index: 2, element: "7")
				//(index: 3, element: "8")
				//(index: 4, element: "9")
				//(index: 0, element: "10")
				//(index: 1, element: "11")
				//(index: 2, element: "12")
				//(index: 3, element: "13")
				//(index: 4, element: "14"
				//   ....
		~~~
#### Time-shifting operators
- Every now and again, you need to travel in time. While RxSwift can’t help with fixing your past relationship mistakes, it has the ability to freeze time for a little while to let you wait until self-cloning is available.
##### Delayed subscriptions
- **delaySubscription**
	- 구독을 지연시킨다.
	![[스크린샷 2022-11-29 16.38.42.png|300]]
	- example Code 
		~~~ swift 
			let delaySource = PublishSubject<String>()
			
			var delayCount = 0
			let delayTimeSource = DispatchSource.makeTimerSource()
			
			delayTimeSource.schedule(deadline: .now() + 2, repeating: .seconds(1))
			delayTimeSource.setEventHandler { 
				delayCount += 1
				delaySource.onNext("\(delayCount)")
			}
			delayTimeSource.resume()
			
			delaySource
				.delaySubscription(.seconds(5), scheduler: MainScheduler.instance)
				.subscribe(onNext: { 
					print($0)
				})
				
				// 4
				// 5 
				// 6 
				// 7 
				// 8 
				// .....
		~~~
##### Delayed elements
- **delay**
	- delaySubscription은 구독을 지연시킨다면, delay는 전체 시퀀스를 뒤로 미룬다. 
	![[스크린샷 2022-11-29 16.39.11.png|300]]
	- example Code 
		~~~ swift 
			let delaySource = PublishSubject<String>()
			
			var delayCount = 0
			let delayTimeSource = DispatchSource.makeTimerSource()
			
			delayTimeSource.schedule(deadline: .now() + 2, repeating: .seconds(1))
			delayTimeSource.setEventHandler { 
				delayCount += 1
				delaySource.onNext("\(delayCount)")
			}
			delayTimeSource.resume()
			
			delaySource
				.delay(.seconds(5), scheduler: MainScheduler.instance)
				.subscribe(onNext: { 
					print($0)
				})
				
				// 1
				// 2 
				// 3 
				// 4
				// 5 
				// .....
		~~~
#### Timer operators
- A common need in any kind of application is a timer. iOS and macOS come with several timing solutions. Historically, Timer did the job, but had a confusing ownership model that made it tricky to get just right. More recently, the dispatch framework offered timers through the use of dispatch sources.
##### Intervals
- **interval**
	- 주어진 시간 간격을 두고 주기마다, emit되는 observable을 생성한다. Interval 연산자의 경우 completed 되지 않고, 무한한 시퀀스를 생성한다. 
	- **즉, 직접 dispose 시키지 않는다면 구독 이후에 계속 반복된다.**
	- 매초마다 API를 실행할 때 유용한 Operator이다.
	- Interval timers are incredibly easy to create with RxSwift. Not only that, but they are also easy to cancel: since Observable.interval(\_:scheduler:) generates an observable sequence, you can simply dispose() the returned disposable to cancel the subscription and stop the timer.
	![[스크린샷 2022-11-30 14.53.07.png|500]]
	- example Code
		~~~ swift
			Observable<Int>
				.interval(.seconds(1), scheduler: MainScheduler.instance)
				.subscribe(onNext: {
					print($0)
				})
				.disposed(by: disposeBag)
				// 0
				// 1
				// 2 
				// 3 
				// ..... 
		~~~
##### One-shot or repeating timers
- **timer**
	- timer operator is a more powerful timer observable. it is very much like Observable.interval() but adds the following features:
		- **You can specify a “due date” as the time that elapsed between the point of subscription and the first emitted value.**
		- The repeat period is optional. If you don't specify one, the timer observable will emit once, then complete.
	- example Code
		~~~ swift 
			Observable<Int>
				.timer(.seconds(3), 
							period: .seconds(2), 
							scheduler: MainScheduler.instance)
				.subscribe(onNext: { 
					print($0)
				})
				.disposed(by: disposeBag)
				// 0
				// 1
				// 2 
				// ... 
				// 프로그램 실행 후 3초뒤 코드 실행, 간격은 2초
		~~~
##### Timeouts
- **timeout**
	- its primary purpose is to semantically distinguish an actual timer from a timeout (error) condition. **Therefore, when a timeout operator fires, it emits a RxError.** TimeoutError error event, if not caught, it terminates the sequence.
	- 정해진 시간을 초과하면 에러 방출
	- example Code
		~~~ swift 
			import RxSwift 
			import RxCocoa
			import UIKit
			import PlaygroundSupport
			
			let disposeBag = DisposeBag()
			
			let notButtonTappedError = UIButton(type: .system)
			notButtonTappedError.setTitle("Click", for: .normal)
			notButtonTappedError.sizeToFit()
			
			playgroundPage.current.liveView = notButtonTappedError
			
			notButtonTappedError.rx.tap
				.do(onNext: { 
					print("Tap!")
				})
				.timeout(.seconds(5), scheduler: MainScheduler.instance)
				.subscribe(onNext: { 
					print($0)
				})
				.disposed(by: disposeBag)
				// 버튼을 누를 시 
				// Tap! 
				// Tap! 출력 
				// 5초 경과시 Error 출력
				// Unhandled error happend: Sequence timeout. 
		~~~
### 출처(참고문헌) 
- RxSwift. Reactive Programming with Swift
- https://github.com/fimuxd/RxSwift
### 연결문서 

### Tag
- #IOS/RxSwift/Operators/replay
- #IOS/RxSwift/Operators/replayAll
- #IOS/RxSwift/Operators/buffer
- #IOS/RxSwift/Operators/window
- #IOS/RxSwift/Operators/delaySubscription
- #IOS/RxSwift/Operators/delay
- #IOS/RxSwift/Operators/interval
- #IOS/RxSwift/Operators/timer
- #IOS/RxSwift/Operators/timeout  
- #IOS/UIKit 

![[스크린샷 2022-11-30 20.47.09.png]]
![[donghua-li_rxswift-operators.bw.pdf]]