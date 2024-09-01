## PublishSubject
- 초기값 없음
- 구독 이후 이벤트만 방출: 새로운 observer 가 구독을 시작하면, 구독 이후에 발생하는 이벤트만 받는다.
- 값 방출: onNext() 메소드를 통해 새로운 값을 추가할 수 있으며, 이 값은 즉시 모든 현재 구독자에게 방출
- 에러 처리: onError 이벤트가 발생하면 PublishSubject는 종료되며, 더 이상 값을 방출하지 않는다.
- 완료 처리: onCompleted 이벤트가 발생하면 PublishSubject 는 종료되며, 더 이상 값을 방출하지 않는다.
* 주로 실시간 이벤트를 처리할 때 유용하며, 사용자의 실시간 입력, 네트워크 요청의 결과, 센서 데이터 스트림을 다룰 때 적합 

```swift
Copyimport RxSwift

let disposeBag = DisposeBag()

// PublishSubject 생성
let publishSubject = PublishSubject<String>()

// 첫 번째 구독
publishSubject.subscribe(onNext: { value in
    print("Observer 1: \(value)")
}).disposed(by: disposeBag)

// 값 방출
publishSubject.onNext("A")
publishSubject.onNext("B")

// 두 번째 구독
publishSubject.subscribe(onNext: { value in
    print("Observer 2: \(value)")
}).disposed(by: disposeBag)

// 추가 값 방출
publishSubject.onNext("C")
publishSubject.onNext("D")

// 완료 이벤트 발생
publishSubject.onCompleted()

// 완료 이후 구독
publishSubject.subscribe(onNext: { value in
    print("Observer 3: \(value)")
}, onCompleted: {
    print("Observer 3: Completed")
}).disposed(by: disposeBag)

// 완료 이후 값 방출 시도 (이 값은 방출되지 않음)
publishSubject.onNext("E")
```

### VS PublishRelay 
1. 값 변경 방식:
   - PublishSubject: onNext() 메소드를 사용하여 값 방출
   - PublishRelay: accept() 메소드를 사용하여 값 방출
2. 종료 처리:
   - PublishSubject: onComplete() 또는 onError() 를 통해 종료
   - PublishRelay: 종료되지 않고 에러를 발생시키거나 완료될 수 없음
3. 에러 처리:
   - PublishSubject: 에러 이벤트를 발생시킬 수 있음
   - PublishRelay: 에러 이벤트를 발생시킬 수 없음
4. 사용 목적:
   - PublishSubject: 일반적인 Rx 스트림 처리에 사용됨
   - PublishRelay: 주로 UI 이벤트 처리나 에러나 완료 없이 지속적인 스트림이 필요한 경우에 사용
5. 가변성:
   - PublishSubject: 변경 가능한 속성을 가짐
   - PUblishRelay: 읽기 전용 속성을 가지며, accept() 메소드로만 값을 변경할 수 있음


