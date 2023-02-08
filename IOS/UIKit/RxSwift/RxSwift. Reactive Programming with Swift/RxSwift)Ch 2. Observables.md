### 날짜: 2022-10-24 12:25

### 주제: Observables are the heart of Rx.
---
### 메모: 
> Observables are the heart of Rx. You’ll spend some time discussing what observables are, how to create them, and how to use them.
#### What is observable? 
- “observable”, “observable sequence” and “sequence” are used interchangeably in Rx. And, really, they’re all the same thing.
- In RxSwift or something that works with a sequence. And an Observable is just a sequence, with some special powers. One of these powers — in fact, the most important one — is that it is asynchronous. Observables produce events over a period of time, which is referred to as emitting. Events can contain values, such as numbers or instances of a custom type, or they can be recognized gestures, such as taps.
- One of the best ways to conceptualize this is by using marble diagrams, which are just values plotted on a timeline.
		 ![[스크린샷 2022-10-25 10.51.05.png]]
#### Lifecycle of an observable
- In the previous marble diagram, the observable emitted three elements. When an observable emits an element, it does so in what’s known as a next event.
- Here’s another marble diagram, this time including a vertical bar that represents the end of the road for this observable.
	![[스크린샷 2022-10-25 11.29.14.png]]
- This observable emits three tap events, and then it ends. This is called a completed event, that is, it’s terminated. For example, perhaps the taps were on a view that was dismissed. The important thing is that the observable is terminated, and can no longer emit anything. This is normal termination. However, sometimes things can go wrong.
	![[스크린샷 2022-10-25 11.30.38.png]]
- An error occurred in this marble diagram, represented by the red X. The observable emitted an error event containing the error. This is the same as when an observable terminates normally with a completed event. If an observable emits an error event, it is also terminated and can no longer emit anything else.
	- Obervable은 어떤 구성요소를 가지는 **next** 이벤트를 계속해서 방출할 수 있다. 
	- Observable은 **종료 이벤트(error 또는 complete)** 을 방출하기 전까지 계속 진행 할 수 있다. 
	- Observable이 종료될 때 더이상 방출 이벤트를 수행하지 않는다. 
#### Creating observables
~~~ swift
	let one = 1 
	let two = 2 
	let three = 3 

	let observable:Observable<Int> = Observable<Int>.just(one)

	// element  1
~~~
- Create an observable sequence of type Int using the just method with the one integer constant.
- The **just** method is aptly named because all it does is create an observable sequence containing just a single element. It is a static method on Observable.
- 단일 요소 뽑아내기
~~~ swift 
	let observable2 = Observable.of(one,two,three)
	// 1
	// 2
	// 3
~~~
- This time, you didn’t explicitly declare the type. You might think because you give it several integers, the type is Observable of [Int].
- However, Option-click on observable2 to show its inferred type, and you’ll see that it’s an Observable of Int, not an array
- That’s because of the operator has a variadic parameter, and Swift can infer the Observable’s type based on it.
- 단일 요소들을 집합으로 묶지만 Int 타입 
~~~ swift 
	let observable3 = Observable.of([one,two,three])
	// [1, 2, 3]
~~~
- Option-click on observable3 and you’ll see that it is indeed an Observable of [Int]. The just operator can also take an array as its single element, which may seem a little weird at first. However, it’s the array that is the single element, not its contents.
- 단일 요소들을 집합으로 묶고 [Int] 타입
~~~ swift 
	let observable4 = Observable.from([one,two,three])
	// 1 
	// 2
	// 3
~~~
- The from operator creates an observable of individual elements from an array of typed elements. Option-click on observable4 and you’ll see that it is an Observable of Int, not [Int]. The from operator only takes an array.
- 집합에 있는 요소들을 단일 요소로 뽑기 
- Your console is probably looking quite bare at the moment. That’s because you haven’t printed anything except the example header. Time to change that by subscribing to observables.
#### Subscribing to observables
- From your experience as an iOS developer, you are likely familiar with NotificationCenter; it broadcasts notifications to observers. However, these observed notifications are different than RxSwift Observables. Here’s an example of an observer of the UIKeyboardDidChangeFrame notification, with a handler as a trailing closure
~~~ swift 
	let observer = NotificationCenter.default.addObserver(
		  forName: UIResponder.keyboardDidChangeFrameNotification,
		  object: nil,
		  queue: nil) { notification in
		  // Handle receiving notification
~~~
- Subscribing to a RxSwift observable is fairly similar; you call observing an observable subscribing to it. So instead of addObserver(), you use subscribe(). Unlike NotificationCenter, where developers typically use only its .default singleton instance, each observable in Rx is different.
- More importantly, an observable won’t send events or perform any work, until it has a subscriber.
- Subscribing to observables is more streamlined. You can also add handlers for each event type an observable can emit. Recall that an observable emits next, error and completed events. A next event passes the element being emitted to the handler, and an error event contains an error instance.
- Option-click on the subscribe operator, and observe it takes a closure parameter that receives an Event of type Int and doesn’t return anything, and subscribe returns a Disposable.
~~~ swift 
	observable2.subscribe { event in 
		print(event)
		}

	observable3.subscribe { event in 
		print(event) 
		}
		
//next(1)
//next(2)
//next(3)
//completed
//next(1)
//next(2)
//next(3)
//completed
~~~
- The observable emits a next event for each element, then emits a completed event and is terminated. When working with observables, you’ll usually be primarily interested in the elements emitted by next events, rather than the events themselves.
~~~ swift
	observable3.subscribe(onNext: { element in 
		print(element)
		})
	
	//1
	//2
	//3
~~~
- Now you’re only handling next event elements and ignoring everything else. The onNext closure receives the next event’s element as an argument. 
- Now you know how to create observable of one element and of many elements. But what about an observable of zero elements? The empty operator creates an empty observable sequence with zero elements; it will only emit a completed event.
~~~ swift 
	let observable = Observable<Void>.empty()

	observable.subscribe( 
		onNext: { element in 
			print(element)
		},
		
		onCompleted: { 
			print("Completed")
		}
	)
~~~
1.  Handle next events, just like you did in the previous example.
2.  Simply print a message, because a .completed event does not include an element.
- *What use is an empty observable? They’re handy when you want to return an observable that immediately terminates or intentionally has zero values.*
- *As opposed to the empty operator, the never operator creates an observable that doesn’t emit anything and never terminates. It can be used to represent an infinite duration.*
~~~ swift
	let observable = Observable<Void>.never()

	observable.subscribe( 
		onNext: { element in 
			print(element)
		},
		onCompleted: { 
			print("Completed")
		}
	)
~~~
- Nothing is printed, except for the example header. Not even "Completed".
- it’s also possible to generate an observable from a range of values.
~~~ swift 
	let observable = Observable<Int>.range(start: 1, count : 10)

	observable
	    .subscribe(onNext: { i in
	      let n = Double(i)
      
	      let fibonacci = Int(
	        ((pow(1.61803, n) - pow(0.61803, n)) /  2.23606).rounded())
	      print(fibonacci)
	  })
~~~
1.  Create an observable using the range operator, which takes a start integer value and a count of sequential integers to generate.
2.  Calculate and print the nth Fibonacci number for each emitted element.
#### Disposing and terminating
- **Remember that an observable doesn’t do anything until it receives a subscription**. It’s the subscription that triggers an observable’s work, causing it to emit new events until an error or completed event terminates the observable. However, you can also manually cause an observable to terminate by canceling a subscription to it.
- To explicitly cancel a subscription, call dispose() on it. After you cancel the subscription, or dispose of it, the observable in the current example will stop emitting events.
~~~ swift 
	subscription.dispose()
~~~
- **Managing each subscription individually would be tedious, so RxSwift includes a DisposeBag type.** A dispose bag holds disposables — typically added using the disposed(by:) method — and will call dispose() on each one when the dispose bag is about to be deallocated. deallocate: 할당을 해제하다
~~~ swift 
	let disposeBag = DisposeBag()

	Observable.of("A","B","C")
		.subscribe { 
			print($0)
			}
		.disposed(by: disposeBag)
~~~ 
1.  Create a dispose bag.
2.  Create an observable.
3.  Subscribe to the observable and print out the emitted events using the default argument name $0.
4.  Add the returned Disposable from subscribe to the dispose bag.
- This is the pattern you’ll use most frequently: creating and subscribing to an observable, and immediately adding the subscription to a dispose bag.
- Why bother with disposables at all?
- **If you forget to add a subscription to a dispose bag, manually call dispose on it when you’re done with the subscription, or in some other way cause the observable to terminate at some point, you will probably leak memory.**
- In the previous examples, you created observables with specific next event elements. **Using the create operator is another way to specify all the events an observable will emit to subscribers.**
~~~ swift 	
	let disposeBag = DisposeBag() 

	Observable<String>.create { observer in 
		observer.onNext("1")
		observer.onCompleted()
		observer.onNext("?")
		return Disposables.create() 
	}.subscribe(
	    onNext: { print($0)},
	    onError: { print($0)},
	    onCompleted: {print("Completed")},
	    onDisposed: {print("Disposed")}
	).disposed(by: DisposeBag())

 // 1
 // completed 
 //Disposed
~~~
- Do you think the second onNext element (?) could ever be emitted to subscribers? Why or why not?
- You subscribed to the observable, and implemented all the handlers using default argument names for element and error arguments passed to the onNext and onError handlers, respectively. The result is, the first next event element, "Completed" and "Disposed" are printed out. The second next event is not printed because the observable emitted a completed event and terminated before it is added.
~~~ swift 
		enum MyError: Error { 
			case anError
		}

	observser.onError(MyError.anError)
	
	// 1 
	// anError
	// Disposed
~~~
- What would happen if you did not add a completed or error event, and also didn’t add the subscription to disposeBag? Comment out the observer.onError, observer.onCompleted and disposed(by: disposeBag) lines of code to find out.
- Congratulations, you’ve just leaked memory! The observable will never finish, and the disposable will never be disposed.
- Feel free to uncomment the line that adds the completed event or the code that adds the subscription to the disposeBag if you just can’t stand to leave this example in a leaky state.
#### Creating observable factories
- Rather than creating an observable that waits around for subscribers, it’s possible to create observable factories that vend a new observable to each subscriber.
~~~ swift 
	let disposeBag = DisposeBag()
	  var flip = false
	  let factory: Observable<Int> = Observable.deferred {
    flip.toggle()
    if flip {
      return Observable.of(1, 2, 3)
    } else {
      return Observable.of(4, 5, 6)
      }
  }
~~~
1.  Create a Bool flag to flip which observable to return.
2.  Create an observable of Int factory using the deferred operator.
3.  Toggle flip, which happens each time factory is subscribed to.
4.  Return different observables based on whether flip is true or false.
- **deferred 연산자는 Observable을 만들어내는 팩토리 클로저를 인자로 받습니다. 그리고 실제 구독이 일어나는 시점에서야 실제 Observable을 만들어냅니다. 즉, 실제 Observable이 만들어지는 시점이 미루어 진다고 해서 'deferred' 입니다.** 
- Externally, an observable factory is indistinguishable from a regular observable. Add this code to the bottom of the example to subscribe to factory four times:
~~~ swift 
	for _ in 0...3 { 
		factory.subscribe(onNext: { 
			print($0, terminator: "")
		})
		.disposed(by: disposedBag)
	print()	
	}
// 123
// 456
// 123
// 456 
~~~
#### Using Traits
- Traits are observables with a narrower set of behaviors than regular observables. Their use is optional; you can use a regular observable anywhere you might use a trait instead. Their purpose is to provide a way to more clearly convey your intent to readers of your code or consumers of your API. The context implied by using a trait can help make your code more intuitive.
- There are three kinds of traits in RxSwift: Single, Maybe and Completable.
- **Singles** 
	- **Singles will emit either a success(value) or error(error) event.** success(value) is actually a combination of the next and completed events. *This is useful for one-time processes that will either succeed and yield a value or fail, such as when downloading data or loading it from disk.*
- **Completable**
	- **A Completable will only emit a completed or error(error) event.** It will not emit any values. You could use a completable when you only care that *an operation completed successfully or failed, such as a file write.*
- **Maybe**
	- **Maybe is a mashup of a Single and Completable. It can either emit a success(value), completed or error(error).** If you need to implement an operation that could either succeed or fail, and optionally return a value on success, then Maybe is your ticket.
~~~ swift
	let disposeBag = DisposeBag()

	enum FileReadError: Error { 
		case fileNotFound, unreadable, encodingFailed
		}

	func loadText(from name: String) -> Single<String> { 
		return Single.create { single in 
			let disposable = Disposables.create()
			
			guard let path = Bundle.main.path(forResource: name, ofType : "txt") else { 
				single(.error(FileReadError.fileNotFound))
				return disposable 
				}
				
			guard let data = FileManager.default.contents(atPath : Path) else { 
				single(.error(FileReadError.unreadable))
				}
			
			guard let contents = String(data: data, encoding: .utf8) else { 
				single(.error(FileReadError.encodingFailed))
				return disposable
				}
				
			single(.success(contents))
			return disposable
			}
		}

	loadText(from: "Copyright")
		.subscribe { 
			switch $0 { 
				case .success(let string): 
					print(string)
				case .error(let error): 
					print(error)
			}
		}
		.disposed(by: disposeBag)
~~~
1.  Create a dispose bag to use later.
2.  Define an Error enum to model some possible errors that can occur in reading data from a file on disk.
3. Implement a function to load text from a file on disk that returns a Single.
4. Create and return a Single.
5. **Create a Disposable, because the subscribe closure of create expects it as its return type.**
6.  Get the path for the filename, or else add a file not found error onto the Single and return the disposable you created.
7.  Get the data from the file at that path, or add an unreadable error onto the Single and return the disposable.
8.  Convert the data to a string; otherwise, add an encoding failed error onto the Single and return the disposable. Starting to see a pattern here?  
9.  Made it this far? Add the contents onto the Single as a success, and return the disposable.
10.  Call loadText(from:) and pass the root name of the text file.
12.  Subscribe to the Single it returns.
13.  Switch on the event and print the string if it was successful, or print the error if not.
### 출처(참고문헌) 
- RxSwift. Reactive Programming with Swift
- https://github.com/fimuxd/RxSwift
- https://rxmarbles.com
### 연결문서 
- [[RxSwift)Ch 1. Hello, RxSwift]]


### Tag
- #IOS/RxSwift/Observable/just
- #IOS/RxSwift/Observable/of
- #IOS/RxSwift/Observable/from
- #IOS/RxSwift/Observable/empty
- #IOS/RxSwift/Observable/never
- #IOS/RxSwift/Observable/range 
- #IOS/RxSwift/Observable/create
- #IOS/RxSwift/Observable/subscribe 
- #IOS/RxSwift/Observable/subscribe/disposable 
- #IOS/RxSwift/Observable/factory 
- #IOS/RxSwift/Observable/traits/single
- #IOS/RxSwift/Observable/traits/maybe
- #IOS/RxSwift/Observable/traits/completable
- #IOS/UIKit 