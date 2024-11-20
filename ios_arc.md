## ARC
- iOS ARC (Automatic Reference Counting)는 메모리 관리를 자동화하는 시스템 

```swift
class Person {
    var name: String
    var apartment: Apartment?
    
    init(name: String) { self.name = name }
    deinit { print("\(name) deinitialized") }
}

// 참조 카운트가 증가/감소
var person1: Person? = Person(name: "John") // count: 1
var person2 = person1 // count: 2
person1 = nil // count: 1
person2 = nil // count: 0, 메모리 해제
```
1. 강한 순환 참조와 해결 방법 
```swift
// 1. 강한 순환 참조가 발생하는 상황
class Person {
    let name: String
    var apartment: Apartment?  // 강한 참조
    
    init(name: String) {
        self.name = name
        print("\(name) initialized")
    }
    
    deinit {
        print("\(name) deinitialized")  // 호출되지 않음
    }
}

class Apartment {
    let unit: String
    var tenant: Person?  // 강한 참조
    
    init(unit: String) {
        self.unit = unit
        print("Apartment \(unit) initialized")
    }
    
    deinit {
        print("Apartment \(unit) deinitialized")  // 호출되지 않음
    }
}
// 강한 순환 참조 발생
var john: Person? = Person(name: "John")       // Person RC: 1
var unit4A: Apartment? = Apartment(unit: "4A") // Apartment RC: 1

john?.apartment = unit4A    // Apartment RC: 2
unit4A?.tenant = john      // Person RC: 2

// nil 할당해도 메모리 해제 안됨
john = nil     // Person RC: 1
unit4A = nil   // Apartment RC: 1

- weak var tenant: Person?  // weak로 변경하여 해결
- weak: 참조 대상이 nil이 될 수 있는 경우
- unowned: 참조 대상이 항상 존재한다고 확실할 때 
```

2. 클로저에서의 메모리 관리
```swift
class ViewController {
    var handler: (() -> Void)?
    
    func setupHandler() {
        // 강한 순환 참조 방지
        handler = { [weak self] in
            guard let self = self else { return }
            self.view.backgroundColor = .red
        }
    }
}
```

