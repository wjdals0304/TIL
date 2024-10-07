## 변환 연산자 
### 1. MAP 
- transform 클로저를 사용하여 Upstream Publisher 에서 나오는 모든 값들을 변환하는 Publisher

```swift
let numbers = [1, 2, 3, 4, 5]
let publisher = numbers.publisher

publisher
    .map { $0 * 2 }
    .sink { print($0) }

// 출력: 2, 4, 6, 8, 10
```
### 2. TryMap 
- Map 과 유사하지만 transform 클로저에서 에러를 throw할 수 있는 Publisher
```swift
publisher.tryMap { value in
    // 변환 로직 (에러를 throw할 수 있음)
    if someCondition {
        throw SomeError.invalidValue
    }
    return transformedValue
}
```
### 3. FlatMap 
- Upstream Publisher 에서 오는 값들을 새로운 Publisher 로 변환하는 Publisher
```swift
struct User { let id: Int }

func fetchUserDetails(for user: User) -> AnyPublisher<String, Never> {
    // 사용자 상세 정보를 비동기적으로 가져오는 퍼블리셔
    return Just("User \(user.id) details").eraseToAnyPublisher()
}

let users = [User(id: 1), User(id: 2), User(id: 3)].publisher

users
    .flatMap { user in
        fetchUserDetails(for: user)
    }
    .sink { print($0) }

// 출력:
// User 1 details
// User 2 details
```

## 필터 연산자

### 1. Filter 
- 클로저의 조건과 일치하는 값을 발행하는 Publisher

### 2. CompactMap 
- 클로저에서 nil 이 아닌 값을 발행하는 Publisher

## 조합 연산자 

### 1. Zip 
- 두개 이상의 Upstream Publisher 로부터 나온 값을 받아 조합하고 그 값을 방출해주는 Publisher

