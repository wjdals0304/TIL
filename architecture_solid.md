## SOLID 원칙 
### 1. 단일 책임 원칙: 각 클래스는 하나의 책임만 가져야 한다 .
```swift
// 잘못된 예:
class UserManager {
    func createUser() { /* ... */ }
    func saveUserToDatabase() { /* ... */ }
    func sendWelcomeEmail() { /* ... */ }
}

// 개선된 예:
class UserCreator {
    func createUser() { /* ... */ }
}

class DatabaseManager {
    func saveUser() { /* ... */ }
}

class EmailService {
    func sendWelcomeEmail() { /* ... */ }
}
```
### 2. 개방-폐쇄 원칙: 소프트웨어 개체는 확장에는 열려 있어야 하고, 수정에는 닫혀 있어야한다. 
```swift
protocol Shape {
    func area() -> Double
}

class Rectangle: Shape {
    let width: Double
    let height: Double
    
    init(width: Double, height: Double) {
        self.width = width
        self.height = height
    }
    
    func area() -> Double {
        return width * height
    }
}

class Circle: Shape {
    let radius: Double
    
    init(radius: Double) {
        self.radius = radius
    }
    
    func area() -> Double {
        return .pi * radius * radius
    }
}
```

### 3. 리스코프 치환 원칙: 하위 클래스는 상위 클래스를 대체할 수 있어야 한다. 
```swift
class Bird {
    func fly() {
        print("새가 날아갑니다.")
    }
}

class Sparrow: Bird {
    override func fly() {
        print("참새가 날아갑니다.")
    }
}

// 펭귄은 날 수 없으므로 Bird를 상속받지 않습니다.
class Penguin {
    func swim() {
        print("펭귄이 수영합니다.")
    }
}
```
### 4. 인터페이스 분리 원칙: 클라이언트는 자신이 사용하지 않는 인터페이스에 의존하지 않아야한다.
```swift
protocol Workable {
    func work()
}

protocol Eatable {
    func eat()
}

class Human: Workable, Eatable {
    func work() {
        print("일합니다.")
    }
    
    func eat() {
        print("먹습니다.")
    }
}

class Robot: Workable {
    func work() {
        print("일합니다.")
    }
}
```
### 5. 의존성 역전 원칙: 고수준 모듈은 저수준 모듈에 의존해서는 안되고 둘다 추성화에 의존해야 한다.
```swift
protocol MessageSender {
    func send(message: String)
}

class EmailSender: MessageSender {
    func send(message: String) {
        print("이메일로 전송: \(message)")
    }
}

class SMSSender: MessageSender {
    func send(message: String) {
        print("SMS로 전송: \(message)")
    }
}

class NotificationService {
    private let sender: MessageSender
    
    init(sender: MessageSender) {
        self.sender = sender
    }
    
    func notify(message: String) {
        sender.send(message: message)
    }
}

// 사용 예:
let emailNotifier = NotificationService(sender: EmailSender())
emailNotifier.notify(message: "안녕하세요!")

let smsNotifier = NotificationService(sender: SMSSender())
smsNotifier.notify(message: "안녕하세요!")
```

