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
let intPublisher = [1,2,3].publisher
let stringPublisher = ["A","B","C"].publisher

intPublisher
 .flatMap { intValue in
   stringPublisher.map { "\(intValue)\($0)" }
 }.sink { value in
    print("Received value: \(value)" )
 }

Received value: 1A
Received value: 1B
Received value: 1C
Received value: 2A
```

## 필터 연산자

### 1. Filter 
- 클로저의 조건과 일치하는 값을 발행하는 Publisher
```swift
let numbers = [1, 2, 3, 4, 5, 6].publisher

numbers
    .filter { $0 % 2 == 0 }
    .sink { print($0) }

// 출력: 2, 4, 6
```

### 2. CompactMap 
- 클로저에서 nil 이 아닌 값을 발행하는 Publisher
```swift
let numbers = ["1", "2", "three", "4", "5"].publisher

numbers
    .compactMap { Int($0) }
    .sink { print($0) }

// 출력: 1, 2, 4, 5
```

## 조합 연산자 

### 1. Zip 
- 두개 이상의 Upstream Publisher 로부터 나온 값을 받아 조합하고 그 값을 방출해주는 Publisher
```swift
let numbers = [1, 2, 3].publisher
let letters = ["A", "B", "C"].publisher

numbers.zip(letters)
    .sink { print("\($0): \($1)") }

// 출력:
// 1: A
// 2: B
// 3: C
```
