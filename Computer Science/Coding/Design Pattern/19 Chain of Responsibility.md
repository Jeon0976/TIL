### 날짜: 2023-07-19 19:35

### 주제: 내가 못하는 핸들은 다음 객체로 넘기기 
---
### 메모: 
> 객체 지향 디자인에서 chain of responsibility pattern은 명령 객체와 일련의 처리 객체를 포함하는 디자인 패턴이다. 각각의 처리 객체는 명령 객체를 처리할 수 있는 연산의 집합이고, 체인 안의 처리 객체가 핸들할 수 없는 명령은 다음 처리 객체로 넘겨진다. 
> 이 작동방식은 새로운 처리 객체로부터 체인의 끝가지 다시 반복된다. 
> 표준 책임 연쇄 모델이 변화하면서, 어떤 처리 방식에서는 다양한 방향으로 명령을 보내 책임을 트리 형태로 바꾸는 관제사 역할을 하기도 한다. 
> 몇몇 경우에서는, 처리 객체가 상위의 처리 객체와 명령을 호출하여 작은 파트의 문제를 해결하기 위해 재귀적으로 실행된다. 
> 이 경우 재귀는 명령이 처리되거나 모든 트리가 탐색될때까지 진행되게 된다. 
> XML(파싱되었으나 실행되지 않은) 인터프리터가 한 예이다.
#### Chain of Responsibility 패턴 이란? 
- Chain of Responsibility 패턴은 요청을 처리할 수 있는 기회를 한 객체에서 다른 객체로 넘겨주는 패턴이다. 이 패턴은 요청을 보낼 수 있는 객채와 이를 받을 수 있는 객체 사이의 결합도를 줄이는 데 유용하며, 요청을 받을 수 있는 객체가 둘 이상일 때 특히 유용합니다.
##### 패턴의 구조 
- Base Handler있는 버전
![first structure|400](https://user-images.githubusercontent.com/73867548/158128753-6d0704b1-b0e4-4a55-b9e9-f82515127479.png)
- Base Handler제거 버전 
![second|400](https://user-images.githubusercontent.com/73867548/158132234-13932417-7af7-46f5-8433-9c3238cd3f95.png)\
- **Handler** 
	- 요청을 처리하는 인터페이스를 정의하며, 다음 Handler에 대한 참조를 유지한다. 
- **Base Handler** 
	- 모든 핸들러 클래스에 공통적인 **보일러플레이트 코드**를 구현하는 클래스이다. 즉, 베이스 핸들러에서 모든 핸들러가 공통으로 사용하는 코드를 작성한다. 
	- 베이스 핸들러는 프로토콜로 정의된 Handler를 채택하여 구현하고 다음 핸들러에 대한 참조를 가지고있다. 베이스 핸들러는 선택적으로 구현이 가능하다. 
	- *Concrete Handler에서 바로 Handler를 채택해 구현 할 수 있기 때문이다. 또한 swift의 Extension을 사용해 베이스 핸들러의 역할을 대체할 수 있다.* 
- **Concrete Handler**
	- Handler 인터페이스를 구현하며, 자신이 처리할 수 없는 요청을 다음 Handler로 전달한다. 
- **Client**
	- 클라이언트가 첫 번째 핸들러를 가지고 있고 체인을 연결할 수 있다. 그리고 지정된 첫 번째 핸들러에게 요청을 보낼 수 있다. 
##### 패턴의 장단점 
###### 장점 
- **객체 간의 결합도 감소** 
	- 객체들이 요청을 처리할 것인지 넘길 것 인지만 판단하면 되기 때문에 체인의 다른 객체들끼리 서로 무엇을 하는지 알 필요가 없다. 
	- 그리고 요청의 순서를 바꾸고 싶을때 클라이언트에서 객체들의 순서만 바꾸면 되기 때문에 손쉽게 수정할 수 있다. 
-  **새로운 처리 객체 추가 용이** 
	- 새로운 요청을 처리하는 객체를 쉽게 체인에 추가할 수 있다. 
- **SOLID in Chain of Responsibility**
	- 이 패턴은 SOLID 원칙에도 연관되어 있다. 이중에서 S와 O에 해당하는데, 작업을 수행하는 클래스와 작업을 호출하는 클래스를 분리할 수 있기 때문에 SRP(단일 책임 원칙)를 지킬 수 있다.
	- 기존의 코드를 변경하지 않고 새로운 핸들러를 추가할 수 있기 때문에 OCP(개방/폐쇄 원칙)을 지킬 수 있다. 
###### 단점 
-  **요청 처리의 보장 없음** 
	- 요청이 체인의 끝까지 도달하고도 처리되지 않을 수 있다. 
-  **디버깅 및 유지보수 어려움** 
	- 요청이 여러 Handler를 거치며 디버깅이 어려울 수 있으며, 요청이 처리되는 과정을 추적하기 어렵다.
-  **무한루프 주의**
	- 체인 설계를 잘못한 경우에는 무한루프에 빠질 수 있다. 
#### 예시 코드 
##### 패턴의 예시 
``` swift 
import UIKit

// Handler
// anyobject 하는 이유 -> setNext에서 nextHandler값 대입하기 때문
// 구조체는 값을 바꿀려면 mutating해야하는데 class는 필요없다
// class만이 이 protocol를 채택할 것이라고 명시
protocol Handler: AnyObject {
    var nextHandler: Handler? { get set }
    
    func setNext(handler: Handler)
    func handle(request: String) -> String?
}

extension Handler {
    func setNext(handler: Handler) {
        if self.nextHandler == nil {
            self.nextHandler = handler
        } else {
            self.nextHandler?.setNext(handler: handler)
        }
    }
    
    func handle(request: String) -> String? {
        return nextHandler?.handle(request: request)
    }
}

// Concrete Handler
class TomatoHandler: Handler {
    var nextHandler: Handler?
    
    func handle(request: String) -> String? {
        print("토마토 기계 전달 완료")
        if request == "토마토" {
            return "tomato success"
        } else {
            if let response = nextHandler?.handle(request: request) {
                return response
            } else {
                return "요청 실패"
            }
        }
    }
}

class OnionHandler: Handler {
    var nextHandler: Handler?
    
    func handle(request: String) -> String? {
        print("양파 기계 전달 완료")
        if request == "양파" {
            return "onion success"
        } else {
            if let response = nextHandler?.handle(request: request) {
                return response
            } else {
                return "요청 실패"
            }
        }
    }
}

class LettuceHandler: Handler {
    var nextHandler: Handler?

    func handle(request: String) -> String? {
        print("양상추 기계 전달 완료")
        if request == "양상추" {
            return "양상추 손질 완성!"
        } else {
            if let response = nextHandler?.handle(request: request) {
                return response
            } else {
                return "요청에 실패했습니다."
            }
        }
    }
}

// Client
class Client {
    private var firstHandler: Handler?
    
    func request(request: String) -> String {
        return self.firstHandler?.handle(request: request) ?? "firstHandler를 설정해주세요"
    }
    
    func addHandler(handler: Handler) {
        if let firstHandler = firstHandler {
            firstHandler.setNext(handler: handler)
        } else {
            self.firstHandler = handler
        }
    }
}

let client = Client()

let tomato = TomatoHandler()
let onion = OnionHandler()
let lettuce = LettuceHandler()

client.addHandler(handler: tomato)
client.addHandler(handler: onion)
client.addHandler(handler: lettuce)

print(client.request(request: "양파"))
```
- `setNext(handler: )` 메소드는 `Handler` 프로토콜의 extension에 구현되어 있다. 이 메소드는 이미 존재하는 핸들러 체인의 끝에 새로운 핸들러를 추가하는 역할을 한다. 
- `setNext(handler:)` 메소드를 호출하면, 핸들러 체인의 각 요소들이 자신의 `nextHandler`를 확인하여 `nil`인 경우, 새로운 핸들러를 추가한다. 이는 핸들러 체인의 마지막 핸들러까지 계속 이어진다. 따라서 체인에 핸들러가 `n`개 있을 때, 새로운 핸들러를 추가하는 시간 복잡도는 `O(n)`이 된다.
- 이런 방식으로 핸들러를 추가하는 이유는, Chain of Responsibility 패턴에서 각 핸들러는 다음 핸들러에 대한 참조를 가지고 있어야 하기 때문이다.
- 이렇게 함으로써 요청을 체인 상의 다음 핸들러로 전달할 수 있다.
- 그러나 일반적으로 핸들러 체인의 크기는 그렇게 크지 않을 것이므로, 이 시간 복잡도는 실제 사용에 있어서는 큰 문제가 되지 않을 가능성이 높다.
- 핸들러 체인의 크기가 매우 크다면, 체인의 관리나 최적화 방안에 대해 고민할 필요가 있을 수 있다. 
##### UIResponder
- UIResponder는 Chain of Responsibility 패턴을 사용하여 이벤트를 처리한다.  
- 특정 이벤트가 발생하면, 이벤트는 첫 번째 응답자(키보드 입력을 받는 텍스트 필드 등)부터 시작해서 해당 응답자가 이벤트를 처리할 수 없으면 다음 응답자로 전달되며, 최종적으로 UIApplication까지 도달한다. 
``` swift 
class CustomView: UIView {
    override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
        print("Touch event on CustomView")
        next?.touchesBegan(touches, with: event)
    }
}

class CustomViewController: UIViewController {
    override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
        print("Touch event on CustomViewController")
        next?.touchesBegan(touches, with: event)
    }
}

let customView = CustomView()
let customViewController = CustomViewController()
customViewController.view.addSubview(customView)
```
- 위 코드에서 `CustomView`의 `touchesBegan` 메소드에서 이벤트를 처리하고 난 후, next responder로 이벤트를 전달하고 있다. `CustomView`의 next responder는 `CustomViewController` 이므로, `CustomViewController`의 `touchesBegan` 메소드가 호출된다. 
- 만약 이벤트를 여기에서 처리하지 않으면, 다음 responder로 계속 이벤트가 전달된다. 
##### 카테고리 선택하기
- 각각의 상품 카테고리는 `Category` 라는 객체로 표현되고, `Category` 는 여러 개의 상품을 가지고 있다. 
- 이 때 각각의 상품을 표현하는 버튼이 있고, 버튼을 누르면 해당 상품의 세부정보를 보여주는 액션을 처리하는 방식
###### MVP 패턴에서 활용 
``` swift 
protocol CategoryHandler { 
	var nextHandler: CategoryHandler? { get set }
	
	func handle(request: String) -> String?
}

extension CategoryHandler { 
	func setNext(handler: CategoryHandler) { 
		if self.nextHandler == nil { 
			self.nextHandler = handler
		} else { 
			self.nextHandler?.setNext(handler: handler)
		}
	}
}

// concrete Handlers 
class ElectronicsHandler: CategoryHandler { 
	var nextHandler: CategoryHandler? 
	
	func handle(request: String) { 
		if request  == "Electronics" { 
			return "Electronics Selected"
		} else { 
			return nextHandler?.handler(request: request)
		}
	}
}

class FashionHandler: CategoryHandler { 
	var nextHandler: CategoryHandler? 
	
	func handle(request: String) { 
		if request  == "Fashion" { 
			return "Fashion Selected"
		} else { 
			return nextHandler?.handler(request: request)
		}
	}
}

protocol CategoryProtocol: AnyObject { 
	func selectCategory(_ category: String)
	func resetButtons() 
}

class CategoryPresenter { 
	weak var viewController: CategoryProtocol? 
	
	private var electronicsHandler: CategoryHandler! 
	private var fashionHandler: CategoryHandler!
	
	init() { 
		electronicsHandler = ElectronicsHandler() 
		fashionHandler = FashionHandler()
		
		electronicsHandler.setNext(handler: fasionHandler)
	}
	
	func selectCategory(_ category: String) { 
		electronicsHandler.handle(request: category)
		viewController?.selectCategory(category)
	}
}

class CategoryViewController: UIViewController { 
	var presenter: CategoryPresenter! 
	
	var electronicsButton: UIButton! 
	var fashionButton: UIButton! 
	var homeButton: UIButton!
	var beautyButton: UIButton! 
	
	override func viewDidLoad() {
		 super.viewDidLoad()
		  // Initialize Presenter 
		  presenter = CategoryPresenter() 
		  presenter.viewController = self
		  
		// Configure buttons 
		electronicsButton.setTitle("Electronics", for: .normal) 
		electronicsButton.addTarget(self, action: #selector(buttonTapped), for: .touchUpInside) 
		fashionButton.setTitle("Fashion", for: .normal) 
		fashionButton.addTarget(self, action: #selector(buttonTapped), for: .touchUpInside)
	}
	
	@objc func buttonTapped(_ sender: UIButton) { 
		guard let category = sender.title(for: .normal) else { return }
		presenter.selectCategory(category)
	}
}

extension CategoryViewController: CategoryProtocol { 
	func selectCategory(_ category: String) { 
		resetButtons() 
		switch category { 
			case "Electronics" : 
				electronicsButton.backgroundColor = .blue
			case "Fashion" : 
				fashionButton.backgroundColor = .blue
			default: 
				break
		}
	}
	
	func resetButtons() { 
		electronicsButton.backgroundColor = .white 
		fashionButton.backgroundColor = .white
	}
}
```
###### MVVM 패턴에서 활용 
``` swift 
// CategoryHandler.swift
protocol CategoryHandler: class {
    var nextHandler: CategoryHandler? { get set }
    var category: String { get }
    var delegate: CategoryHandlerDelegate? { get set }
    
    func handle(request: String)
}

protocol CategoryHandlerDelegate: AnyObject {
    func handleCategory(_ category: String)
}

extension CategoryHandler {
    func setNext(handler: CategoryHandler) {
        if self.nextHandler == nil {
            self.nextHandler = handler
        } else {
            self.nextHandler?.setNext(handler: handler)
        }
    }
}

// Concrete Handlers
class ElectronicsHandler: CategoryHandler {
    var nextHandler: CategoryHandler?
    var category = "Electronics"
    weak var delegate: CategoryHandlerDelegate?
    
    func handle(request: String) {
        if request == category {
            delegate?.handleCategory(category)
        } else {
            nextHandler?.handle(request: request)
        }
    }
}

class FashionHandler: CategoryHandler {
    var nextHandler: CategoryHandler?
    var category = "Fashion"
    weak var delegate: CategoryHandlerDelegate?
    
    func handle(request: String) {
        if request == category {
            delegate?.handleCategory(category)
        } else {
            nextHandler?.handle(request: request)
        }
    }
}

// ViewModel
class CategoryViewModel: CategoryHandlerDelegate {
    var category: Observable<String?> = Observable(nil)
    
    private var electronicsHandler: CategoryHandler!
    private var fashionHandler: CategoryHandler!
    private var homeHandler: CategoryHandler!
    private var beautyHandler: CategoryHandler!
    
    init() {
        // Initialize and configure handlers
        electronicsHandler = ElectronicsHandler()
        fashionHandler = FashionHandler()
        homeHandler = HomeHandler()
        beautyHandler = BeautyHandler()
        
        // Set up the chain
        electronicsHandler.setNext(handler: fashionHandler)
        fashionHandler.setNext(handler: homeHandler)
        homeHandler.setNext(handler: beautyHandler)

        // Assign delegates
        electronicsHandler.delegate = self
        fashionHandler.delegate = self
        homeHandler.delegate = self
        beautyHandler.delegate = self
    }
    
    func selectCategory(_ category: String) {
        electronicsHandler.handle(request: category)
    }
    
    func handleCategory(_ category: String) {
        self.category.value = category
    }
}

// ViewController
class MyViewController: UIViewController {
    var viewModel: CategoryViewModel!
    
    var electronicsButton: UIButton!
    var fashionButton: UIButton!
    var homeButton: UIButton!
    var beautyButton: UIButton!

    override func viewDidLoad() {
        super.viewDidLoad()
        
        viewModel = CategoryViewModel()

        electronicsButton.setTitle("Electronics", for: .normal)
        electronicsButton.addTarget(self, action: #selector(buttonTapped), for: .touchUpInside)

        fashionButton.setTitle("Fashion", for: .normal)
        fashionButton.addTarget(self, action: #selector(buttonTapped), for: .touchUpInside)
        
        
        // Binding 
        viewModel.category.bind { [weak self] category in
            self?.resetButtons()
            switch category {
            case "Electronics":
                self?.electronicsButton.backgroundColor = .blue
            case "Fashion":
                self?.fashionButton.backgroundColor = .blue
            default:
                break
            }
        }
    }

    @objc func buttonTapped(_ sender: UIButton) {
        guard let category = sender.title(for: .normal) else { return }
        viewModel.selectCategory(category)
    }

    func resetButtons() {
        electronicsButton.backgroundColor = .white
        fashionButton.backgroundColor = .white
    }
}
```

### 출처(참고문헌) 
- https://yagom.net/courses/design-pattern-in-swift/lessons/행위-패턴/topic/chain-of-responsibility/
- ChatGPT

### 연결문서 
- [[18. Boilerplate Code (x)]]
- [[26. UIResponder (x)]]

### Tag
- #CS/Design_Patterns/Behavioral/Chain_of_Responsibility
- #IOS/UIKit/UIResponder 
- #기능구현/택일버튼