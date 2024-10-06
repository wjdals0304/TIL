## Publisher 
### 1. Just
- 각 subsriber 에게 한 번 값을 방출한 후 완료되는 Publisher
- 실패가 없으므로 , Failure = Never

### 2. Empty
- 값을 생성하지 않고 완료만 하는 Publisher

### 3. Fail 
- 특정 에러로 즉시 종료되는 Publisher

### 4. Future 
- 최종적으로 하나의 값을 생성한 후 완료되거나 실패하는 Publisher 


## Subscriber 

### 1.sink 
- 구독 시 무제한 개수의 값을 요청하는 Subscriber
- receiveCompletion: 완료되었을 때 실행
- receiveValue: 값을 받았을 때 실행

### 2. Assign 
- 지정된 key path 로 값을 프로퍼티에 할당하는 Subscriber 
