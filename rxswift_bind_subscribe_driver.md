## subscribe 
- Observable 의 모든 이벤트(next, error, completed) 를 처리할 수 있음, 직접 스레드 관리와 에러 처리를 해야함
- 세밀한 제어가 필요하거나 에러 처리가 중요할 때 
```swift
observable.subscribe(onNext: { value in
    print(value)
}, onError: { error in
    print(error)
}, onCompleted: {
    print("Completed")
})
.disposed(by: disposeBag)
```

## bind 
- 주로 UI 요소에 데이터를 쉽게 연결할 때 사용, 메인 스레드에서 실행되지만, 에러 처리 기능이 없어 주의가 필요
- 간단한 UI 바인딩에 적합
```swift
observable
    .bind(to: label.rx.text)
    .disposed(by: disposeBag)
```

## drive 
- UI 작업에 가장 적합한 방법, 항상 메인 스레드에서 실행되며, 에러를 방출하지 않아 안전, 또한 기본적으로 리소스를 공유하여 효율적
- 안전하고 호율적인 UI 업데이트가 필요할 때 
```
observable
    .asDriver(onErrorJustReturn: "")
    .drive(label.rx.text)
    .disposed(by: disposeBag)
```
