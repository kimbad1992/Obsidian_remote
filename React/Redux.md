
## 상태 변경

```jsx
import { createStore } from 'redux';

const 체중 = 100;

function reducer(state=체중, action) {
	if (action.type === '증가') {
		return state++;
	} else if (action.type === '감소') {
		return state--;
	}
	...
}

// state의 수정 요청을 할 경우
// 다른 컴포넌트에서..
<button onClick={()=> { dispatch({type:증가}) }}>
```

상위 컴포넌트로부터 하위 컴포넌트로 전부 props를 전달해야하는 번거로운 문제가 있다
redux는 그 부분의 어려움을 해결해줌
또한 store에서 state의 변경을 총괄하기 때문에 유지보수에도 장점이 있다.
