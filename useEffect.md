
## useEffect 
- React의 Hook중 컴포넌트의 생명 주기 동안 특정 작업을 수행할때 사용

### useEffect의 두 번째 매개변수로 배열 [] 을 전달하여 실행 조건을 컨트롤 
#### 1. 의존성 배열이 없는 경우 
```javascript
useEffect(() => {
  console.log("컴포넌트가 렌더링될 때마다 실행됨!");
});
```
#### 2. 빈 배열 
```javascript
useEffect(() => {
  console.log("컴포넌트가 처음 마운트될 때만 실행됨!");
}, []);
```

#### 3. 특정 값이 변경될 때만 실행
- count 값이 변경될 때만 실행됨
```javascript
useEffect(() => {
  console.log(`count 값이 변경됨: ${count}`);
}, [count]);
```

#### 4. 클린업 함수
```
useEffect(() => {
  const timer = setInterval(() => {
    console.log("타이머 실행 중...");
  }, 1000);

  return () => {
    clearInterval(timer);
    console.log("타이머 정리됨!");
  };
}, []);
```
