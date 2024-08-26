## PublishRelay 
 - PublishRelay 는 **onError** 이벤트를 발생하지 않는다.
 - 이는 스트림이 에러로 인해 종료되는것을 방지 
 - **onCompleted** 이벤트를 발생하지 않는다
 - 이로 인해 스트림이 완료되어 종료되는 일이 없음
 - 초기값이 없음, 구독 시점 이후의 이벤트만 전달받는다.
 - accept() 메서드로 값 전달

### PublishRelay 를 사용하는 경우 
1. UI 이벤트 처리 
   - 버튼 탭, 스와이프 제스처 등 UI 이벤트를 처리할 때 사용된다.
   - 에러나 완료로 인해 이벤트 스트림이 중단되는 것을 원하지 않을 때 유용  
2. 지속적인 데이터 스트림
   - 검색 기능에서 사용자 입력을 처리할 때 사용
   - 네트워크 상태 모니터링과 같이 지속적으로 업데이트되는 데이터를 처리할 때 유용
3. 이벤트 브로드캐스팅
   - 여려 구독자에게 동일한 이벤트를 전달해야 할 때 사용
4. 에러 핸들링이 필요 없는 경우 
   - 에러 발생 가능성이 없거나, 에러 처리가 필요 없는 간단한 데이터 흐름에 적합
5. 메모리 관리
   - Relay 는 RxCococa 에 포함되어 있어, 메모리 관리가 자동으로 이루어짐
  
### 사용 시 주의사항 
1. 초기값이 필요한 경우에는 BehaviorRelay 를 사용하는 것이 더 적합 
2. 복잡한 에러 처리가 필요한 경우에는 일반 Subject 나 Observable 을 사용하는 것이 좋다

```swift
import RxSwift
import RxCocoa

class ViewModel {
    let buttonTaps = PublishRelay<Void>()
    let searchText = PublishRelay<String>()
    
    private let disposeBag = DisposeBag()
    
    init() {
        // 버튼 탭 이벤트 처리
        buttonTaps
            .throttle(.milliseconds(300), scheduler: MainScheduler.instance)
            .subscribe(onNext: { [weak self] in
                self?.handleButtonTap()
            })
            .disposed(by: disposeBag)
        
        // 검색어 처리
        searchText
            .debounce(.milliseconds(300), scheduler: MainScheduler.instance)
            .distinctUntilChanged()
            .subscribe(onNext: { [weak self] query in
                self?.performSearch(query: query)
            })
            .disposed(by: disposeBag)
    }
    
    private func handleButtonTap() {
        print("Button tapped")
    }
    
    private func performSearch(query: String) {
        print("Searching for: \(query)")
    }
}

class ViewController: UIViewController {
    let viewModel = ViewModel()
    let disposeBag = DisposeBag()
    
    let button = UIButton()
    let searchBar = UISearchBar()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        button.rx.tap
            .bind(to: viewModel.buttonTaps)
            .disposed(by: disposeBag)
        
        searchBar.rx.text.orEmpty
            .bind(to: viewModel.searchText)
            .disposed(by: disposeBag)
    }
}



```

