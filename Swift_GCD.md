## GCD 
- Apple이 개발한 동시성 프로그래밍 프레임워크

## Dispatch Queues
디스패치 큐(Dispatch Queues): 작업을 실행하는 FIFO 큐

1. 메인 큐: UI 작업을 위한 순차적 큐
특징:
- 앱의 메인 스레드에서 실행됨
- 순차적으로 작업 처리
  
```swift
  DispatchQueue.main.async {
      // UI 업데이트 코드
  }
```

2. 클로벌 큐: 시스템에서 관리하는 동시성 큐
특징:
 - 시스템에서 관리하는 동시성 큐
 - 여러 작업을 동시에 처리 가능
 - QoS에 따라 다양성 우선순위 지정 가능

```swift
.userInteractive: 가장 높은 우선순위, 즉각적인 반응이 필요한 작업
.userInitiated: 사용자가 시작한 작업, 빠른 결과 필요
.default: 기본 우선순위
.utility: 시간이 걸리는 작업, 진행 상황 표시 필요
.background: 백그라운드 작업, 시간에 민감하지 않음
.unspecified: 레거시 API 지원용

DispatchQueue.global(qos: .background).async {
    // 백그라운드 작업
}
```

3. 커스텀 큐: 개발자가 생성하는 큐
특징:
- 개발자가 직접 생성하고 관리하는 큐

```swift
let customQueue = DispatchQueue(label: "com.example.customQueue", qos: .utility, attributes: .concurrent)
customQueue.async {
    // 커스텀 큐에서 실행될 작업
}
```
## Dispatch Groups
여러 작업을 그룹화하여 관리 
```swift
let group = DispatchGroup()

group.enter()
DispatchQueue.global().async {
    // 작업 1
    group.leave()
}

group.enter()
DispatchQueue.global().async {
    // 작업 2
    group.leave()
}

group.notify(queue: .main) {
    print("모든 작업 완료")
}
```

### 디스패치 세마포어 (Dispatch Semaphores): 리소스 접근 제어
### 디스패치 소스 (Dispatch Sources): 시스템 이벤트 처리
