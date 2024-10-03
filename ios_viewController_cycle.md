## UIViewController 생명 주기 
1. init()
   - 뷰 컨트롤러 객체가 생성될 때 호출
2. loadView()
   - 뷰 컨트롤러의 루트 뷰 생성
3. viewDidLoad()
   - 뷰 계층이 메로리에 로드된 직후 호출
   - 초기 설정, 데이터 로딩등을 수행
   - 뷰 컨트롤러 생명주기 동안 한 번만 호출됨
4. viewWillAppear()
   - 뷰가 화면에 표시되기 직전에 호출
   - 뷰가 나타날 때마다 호출
   - 데이터 새로고침, UI 업데이드 수행
5. viewDidAppear()
   - 뷰가 화면에 표시된 직후 호출
   - 애니메이션 시작, 데이터 fetch 등을 수행
6. viewWillLayoutSubviews()
   - 뷰의 하위 뷰 레이아웃을 조정하기 직전에 호출
7. viewDidLayoutSubviews()
   - 뷰의 하위 뷰 레이아웃이 조정된 후 호출
8. viewWillDisappear()
   - 뷰가 화면에서 사라지기 직전에 호출
   - 작업 중단, 데이터 저장 등을 수행
9. viewDidDisappear()
   - 뷰가 화면에서 완전히 사라진 후 호
   - 리소스 해제, 옵저버 제거등을 수행
10. deinit
    - 뷰 컨트롤러 객체가 메모리에서 해제될 때 호출

`didReceiveMemoryWarning()`
 - 메모리 부족 경고 시 호출
 - 불필요한 리소스를 해제
   
`viewWillTransition(to:with:)`
- 기기 회전등으로 인한  크기 변경 시 호출




     
  
