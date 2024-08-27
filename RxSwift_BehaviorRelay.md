## BehaviorRelay 
- 초기값 필수
- value 프로퍼티를 통해 현재 값을 접근할 수 있음
- accept() 메소드를 사용하여 새로운 값을 설정
- asObservable() 을 통해 읽기 전용 Observable로 변환할 수 있음
- 에러 없음
- 완료 없음

### 사용 예시 
- UI 상태 관리
- 데이터 바인딩
- 초기값 필요한 경우
- 값의 변경을 추척할 때

```swift
class ViewModel {
    private let disposeBag = DisposeBag()
    
    // BehaviorRelay 선언 및 초기화
    let counter = BehaviorRelay<Int>(value: 0)
    
    func incrementCounter() {
        // 현재 값을 가져와 1 증가시킨 후 새 값을 설정
        let newValue = counter.value + 1
        counter.accept(newValue)
    }
    
    func setupBindings() {
        counter
            .asObservable()
            .subscribe(onNext: { value in
                print("Counter value changed: \(value)")
            })
            .disposed(by: disposeBag)
    }
}

```
