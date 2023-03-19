### 날짜 : 2022-10-20 15:25

### 주제: What is RxSwift?
---
### 메모 : 
> RxSwift is a library for composing asynchronous and event-based code by using observable sequences and functional style operators, allowing for parameterized execution via schedulers.

> RxSwift, in its essence, simplifies developing asynchronous programs by allowing your code to react to new data and process it in a sequential, isolated manner.

### Asynchronous programming glossary 
1. State, and specifically, shared mutable state
	- When you start your laptop it runs just fine, but, after you use it for a few days or even weeks, it might start behaving weirdly or abruptly hang and refuse to speak to you. The hardware and software remains the same, but what’s changed is the state. As soon as you restart, the same combination of hardware and software will work just fine once more.
	- 상태란, 하드웨어와 소프트웨어는 그대로 이지만 자원을 계속 사용하면서 상태는 계속 변함. 
	- 상태가 변함에 찌꺼기가 남을 수 있음 
2. Imperative programming
	- Imperative programming is a programming paradigm that uses statements to change the program’s state. Much like you would use imperative language while playing with your dog — “Fetch! Lay down! Play dead!” — you use imperative code to tell the app exactly when and how to do things.
	~~~ swift
	override func viewDidAppear(_ animated: Bool) {
 	super.viewDidAppear(animated)
 	
 	setupUI()
 	connectUIControls()
 	createDataSource()
 	listenForChanges()
	 }
	~~~
	- There’s no telling what these methods do. Do they update properties of the view controller itself? More disturbingly, are they called in the right order? Maybe somebody inadvertently swapped the order of these method calls and committed the change to source control. Now the app might behave differently due to the swapped calls.
	- 명령형 프로그래밍의 문제점은 위 코드만 보면 각각의 method 들이 무슨 동작을 하는지 전혀 알 수 없다.  
	- 각각의 method가 순서대로 잘 실행되는지 보증할 수 없다. 
	- 누군가 실수로 method의 순서를 바꿨다면 이로 인해 앱이 다르게 작동되어 버릴 수도 있다. 
3. Side effects 
	- connectUIControls() probably attaches some kind of event handler to some UI components. This causes a side effect, as it changes the state of the view: The app behaves one way before executing connectUIControls() and differently after that.
	- Any time you modify data stored on disk or update the text of a label on screen, you cause a side effect.
	- Side effects are not bad in themselves. After all, causing side effects is the ultimate goal of any program! You need to change the state of the world somehow after your program has finished executing.
	- The important aspect of producing side effects is doing so in a controlled way.
	- RxSwift tries to address the issues (or problems) listed above by tackling the following couple of concepts.
4. Declarative code
	- In imperative programming, you change state at will. In functional programming, you aim to minimize the code that causes side effects. Since you don’t live in a perfect world, the balance lies somewhere in the middle. RxSwift combines some of the best aspects of imperative code and functional code.
	- Declarative code lets you define pieces of behavior. RxSwift will run these behaviors any time there’s a relevant event and provide an immutable, isolated piece of data to work with.
5. Reactive systems
	- Reactive systems is a rather abstract term and covers web or iOS apps that exhibit most or all of the following qualities:
	- Responsive(반응): Always keep the UI up to date, representing the latest app state.
	- Resilient(복원력): Each behavior is defined in isolation and provides for flexible error recovery.
	- Elastic(탄력성): The code handles varied workload, often implementing features such as lazy pull-driven data collections, event throttling, and resource sharing.
	- Message-driven(메시지 전달): Components use message-based communication for improved reusability and isolation, decoupling the lifecycle and implementation of classes.
### Foundation of RxSwift
- Reactive programming isn’t a new concept; it’s been around for a fairly long time, but its core concepts have made a noticeable comeback over the last decade.
- The three building blocks of Rx code are **observables, operators**, and **schedulers.** The sections below cover each of these in detail.
1. **Observables**
	- the ability to asynchronously produce a sequence of events that can “carry” an immutable snapshot of generic data of type Element. In the simplest words, it allows consumers to subscribe for events, or values, emitted by another object over time.
	- The Observable class allows one or more observers to react to any events in real time and update the app's UI, or otherwise process and utilize new and incoming data.
	- The ObservableType protocol (to which Observable conforms) is extremely simple. An Observable can emit (and observers can receive) only three types of events:
		-   **A next event**: An event that “carries” the latest (or "next") data value. This is the way observers “receive” values. An Observable may emit an indefinite amount of these values, until a terminating event is emitted.
		-   **A completed event**: This event terminates the event sequence with success. It means the Observable completed its lifecycle successfully and won’t emit additional events.
		-   **An error event**: The Observable terminates with an error and will not emit additional events.
	- To get an idea about some real-life situations, look at two different kinds of observable sequences: **finite** and **infinite**.
	- **Finite observable sequences**
		- Some observable sequences emit zero, one or more values, and, at a later point, either terminate successfully or terminate with an error.
		- In an iOS app, consider code that downloads a file from the internet:
			- First, you start the download and start observing for incoming data.
			- you then repeatedly receive chunks of data as parts of the file arrive.
			- In the event the network connection goes down, the download will stop and the connection will time out with an error.
			- Alternatively, if the code downloads all the file’s data, it will complete with success.
		~~~ swift
		API.download(file: "http://www....")
			.subscribe(
				onNext: { data in 
					//Append data to a temporary file
					},
				onError: { error in 
					// Display error to the user 
					},
				onCompleted: { 
					// Use downloaded filed
					}
				)
		~~~
	- **Infinite observable sequences**
		- Unlike file downloads or similar activities, which are supposed to terminate either naturally or forcefully, there are other sequences that are simply infinite. Often, UI events are such infinite observable sequences.
		- For example, consider the code you need to react to device orientation changes in your app:
			- You add your class as an observer to UIDeviceOrientationDidChange notifications from NotificationCenter.
			- You then need to provide a method callback to handle orientation changes. It needs to grab the current orientation from UIDevice and react accordingly to the latest value.
			- This sequence of orientation changes does not have a natural end. As long as there is a device, there is a possible sequence of orientation changes. Further, since the sequence is virtually infinite and stateful, you always have an initial value at the time you start observing it.
		~~~ swift
			UIDevice.rx.orientation 
				.subscribe(onNext: { current in 
					switch current { 
						case .landscape :
							// Re-arrange UI for landscape
						case .portrait:
							// Re-arrange UI for portrait
							}
						})
		~~~
2. **Operators**
	- ObservableType and the implementation of the Observable class include plenty of methods that abstract discrete pieces of asynchronous work and event manipulations, which can be composed together to implement more complex logic. Because they are highly decoupled and composable, these methods are most often referred to as operators.
	- Since these operators mostly take in asynchronous input and only produce output without causing side effects, they can easily fit together, much like puzzle pieces, and work to build a bigger picture.
	~~~ swift
	UIDevice.rx.orientation 
		.filter { $0 != .landspace }
		.map { _ in "Portrait is the best!" }
		.subscribe(onNext: { string in 
			showAlert(text : String)
		})
	~~~
	![[스크린샷 2022-10-24 11.51.49.png|300x300]]	
3. **Schedulers**
	- Schedulers are the Rx equivalent of dispatch queues or operation queues — just on steroids and much easier to use. They let you define the execution context of a specific piece of work.
	- RxSwift comes with a number of predefined schedulers, which cover 99% of use cases and hopefully means you will never have to go about creating your own scheduler.
	- For example, you can specify that you’d like to observe the next events on a SerialDispatchQueueScheduler, which uses Grand Central Dispatch to run your code serially on a given queue.
	- ConcurrentDispatchQueueScheduler will run your code concurrently, while OperationQueueScheduler will allow you to schedule your subscriptions on a given OperationQueue.
	- Thanks to RxSwift, you can schedule your different pieces of work of the same subscription on different schedulers to achieve the best performance fitting your use-case.
	- RxSwift will act as a dispatcher between your subscriptions (on the left-hand side below) and the schedulers (on the right-hand side), sending the pieces of work to the correct context and seamlessly allowing them to work with each other’s output.
	![[스크린샷 2022-10-24 12.05.00.png]]
	- To read this diagram, follow the colored pieces of work in the sequence they were scheduled (1, 2, 3, ...) across the different schedulers. For example:
		- The blue network subscription starts with a piece of code (1) that runs on a custom OperationQueue-based scheduler.
		-   The data output by this block serves as the input of the next block (2), which runs on a different scheduler, which is on a concurrent background GCD queue.
		-   Finally, the last piece of blue code (3) is scheduled on the Main thread scheduler in order to update the UI with the new data.
### App Architecture
- It’s worth mentioning that RxSwift doesn’t alter your app’s architecture in any way; it mostly deals with events, asynchronous data sequences, and a universal communication contract.
- It’s also important to note that you definitely do not have to start a project from scratch to make it a reactive app; you can iteratively refactor pieces of an existing project or simply use RxSwift when building new features for your app.
- You can create apps with Rx by implementing a Model-View-Controller architecture, Model-View-Presenter or Model-View-ViewModel (MVVM), or any other pattern that makes your life easier.
### RxCocoa
- RxSwift is the implementation of the common, platform-agnostic, Rx specification. Therefore, it doesn’t know anything about any Cocoa or UIKit-specific classes.
- RxCocoa is RxSwift’s companion library holding all classes that specifically aid development for UIKit and Cocoa. Besides featuring some advanced classes, RxCocoa adds reactive extensions to many UI components so that you can subscribe to various UI events out of the box.
- For example, it’s very easy to use RxCocoa to subscribe to the state changes of a UISwitch, like so:
	~~~ swift 
		toggleSwitch.rx.isOn 
			.subscribe(onNext: { isOn in 
				print(isOn ? "It's ON" : "It's OFF")
			})
	~~~
- RxCocoa adds the rx.isOn property (among others) to the UISwitch class so you can subscribe to useful events as reactive Observable sequences.
	![[스크린샷 2022-10-24 12.16.39.png]]
### 출처(참고문헌) 
- RxSwift. Reactive Programming with Swift
- https://github.com/fimuxd/RxSwift

### 연결문서 
- 

### Tag
- #IOS/Asynchronous
- #IOS/RxSwift/Observable 
- #IOS/RxSwift/Operators
- #IOS/RxSwift/Schedulers
- #IOS/App_Architecture
- #IOS/RxSwift/RxCocoa
- #IOS/UIKit 