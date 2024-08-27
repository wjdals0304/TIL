## Driver 
- 항상 메인 스레드에서 이벤트를 전달
- 에러가 발생하지 않음 (error 이벤트를 발생시키지 않음)
- 내부적으로 share(replay:1, scope: .whileConnected) 를 사용하여 리소스를 공유

### 사용 예시 
- UI 요소의 속성을 바인딩할 때
- UI 관련 이벤트 스트림을 처리할 때

```swift
let searchResults = searchBar.rx.text.orEmpty
    .throttle(.milliseconds(300), scheduler: MainScheduler.instance)
    .distinctUntilChanged()
    .flatMapLatest { query -> Driver<[Result]> in
        if query.isEmpty {
            return Driver.just([])
        }
        return self.performSearch(query)
            .asDriver(onErrorJustReturn: [])
    }

searchResults
    .drive(resultsTableView.rx.items(cellIdentifier: "Cell")) { _, result, cell in
        cell.textLabel?.text = result.title
    }
    .disposed(by: disposeBag)
```
