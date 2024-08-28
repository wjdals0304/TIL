## DispatchGroup 
- 그룹화: 여러 비동기 작업을 하나의 그룹으로 묶을 수 있음
- 완료 알림: 그룹 내 모든 작업이 완료되면 알림을 받을 수 있음
- 대기: 그룹 내 모든 작업이 완료될 때까지 기다릴 수 있음
- 진입/퇴출 카운딩: 그룹에 작업을 추가하거나 완료했음을 수동으로 표시할 수 있음
- 여러 비동기 작업의 완료를 동기화해야할 때 매우 유용, ex) 여러 네트워크 요청을 동시에 보내고 모든 응답을 받은 후에 UI를 업데이트
  
```swift
let group = DispatchGroup()

group.enter()
someAsyncTask1 {
    // 작업 완료
    group.leave()
}

group.enter()
someAsyncTask2 {
    // 작업 완료
    group.leave()
}

group.notify(queue: .main) {
    print("모든 작업이 완료되었습니다.")
}
```
