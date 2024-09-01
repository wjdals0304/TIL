## BehaviorSubject 
- 초기값: 생성 시 초기값을 가져야함
- 최신값 저장: 항상 최신 값을 저장
- 구독 시 최신값 즉시 방출: 새로운 observer 가 구독을 시작하면, 즉시 최신값을 방출
- 값 업데이트: onNext() 메서드를 통해 새로운 값을 추가할 수 있으며, 이 값은 즉시 모든 observer에게 방출
- 에러 처리: onError 이벤트가 발생하면 BehaviorSubject 는 종료되며, 더 이상 값을 방출하지 않는다.

```swift
import RxSwift

let disposeBag = DisposeBag()

// 초기값 0으로 BehaviorSubject 생성
let behaviorSubject = BehaviorSubject<Int>(value: 0)

// 첫 번째 구독
behaviorSubject.subscribe(onNext: { value in
    print("Observer 1: \(value)")
}).disposed(by: disposeBag)

// 새로운 값 1 추가
behaviorSubject.onNext(1)

// 두 번째 구독
behaviorSubject.subscribe(onNext: { value in
    print("Observer 2: \(value)")
}).disposed(by: disposeBag)

// 새로운 값 2 추가
behaviorSubject.onNext(2)

Observer 1: 0
Observer 1: 1
Observer 2: 1
Observer 1: 2
Observer 2: 2
```
### VS BehaviorRelay  
1. 값 변경 방식
   - BehaviorSubject: onNext() 메소드를 사용하여 값 변경
   - BehaviorRelay: accept() 메소드를 사용하여 값 변경
2. 현재 값 변경
   - BehaviorSubject: value 속성을 통해 현재 값 접근, throwable
   - BehaviorRelay: value 속성을 통해 현재 값에 접근, non-throwable
3. 종료 처리:
   - BehaviorSubject: onCompleted() 또는 onError() 를 통해 종료
   - BehaviorRelay: 종료되지 않고 에러를 발생시키거나 완료될 수 없다.
4. 사용 목적:
   - BehaviorSubject: 일반적인 Rx 스트림 처리에 사용
   - BehaviorRelay: 주로 UI 바인딩이나 상태 관리에 사용
5. 가변성:
   - BehaviorSubject: 변경 가능한 속성을 가짐
   - BehaviorRelay: 읽기 전용 속성을 가지며, accept() 메서드로만 값을 변경할 수 있음
