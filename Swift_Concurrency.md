## Concurrency
Swift 5.5에서 도입된 새로운 동시성 프로그래밍 모델 

1. async/await:
- 비동기 함수를 정의하고 호출하는 간단한 방법을 제공
- async 키워드로 비동기 함수를 선언, await 키워드로 비동기 함수의 결과를 기다린다.
```swift
func fetchData() async throws -> Data {
    // 비동기 작업
}

Task {
    do {
        let data = try await fetchData()
        // 데이터 처리
    } catch {
        // 오류 처리
    }
}
```

2. Task:
- 비동기 작업의 단위
```swift
let task = Task {
    // 비동기 작업
}
```

3. Actor:
- 데이터 레이스를 방지하기 위한 동시성 모델
  
(데이터 레이스 - 두개 이상의 스레드가 동시에 같은 메로리 위치에 접근하고, 그 중 하나 이상이 쓰기 작업을 수행할 때 발생하는 현상)

```swift
actor BankAccount {
    private var balance: Decimal
    
    func deposit(amount: Decimal) {
        balance += amount
    }
}
```

4. MainActor:
- 메인 스레드에서 실행되는 전역 액터
- UI 업데이트와 같은 메인 스레드에서 실행되어야 하는 작업을 처리

```swift
@MainActor
class ViewController: UIViewController {
    func updateUI() {
        // 이 메서드는 자동으로 메인 스레드에서 실행됩니다.
    }
}

비동기 컨텍스트: 비동기 작업이 실행될 수 있는 환경 
func fetchAndUpdateUI() async {
    let data = await fetchData() // 백그라운드 작업
    await MainActor.run {
        updateUI(with: data) // UI 업데이트
    }
}
```



