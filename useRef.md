## useRef
- React의 훅중 하나로, 컴포넌트의 상태를 변경하지도 않고 값을 유지할 수 있도록 해줍니다.

### 사용 목적
1. DOM 요소 직접 접근 (document.getElementById 대체)
2. 렌더링 없이 변수 값 유지 (불필요한 리렌더링 방지)
3. 이전 값 저장 및 비교 (useEffect 와 함께 사용)

```javascript
import { useRef } from "react";

function InputFocus() {
  const inputRef = useRef(null);

  const focusInput = () => {
    inputRef.current.focus(); // input 요소에 포커스를 줌
  };

  return (
    <div>
      <input ref={inputRef} type="text" />
      <button onClick={focusInput}>포커스 주기</button>
    </div>
  );
}

```
