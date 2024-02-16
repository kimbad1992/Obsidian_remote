
## 상태 변경

```jsx
import { createStore } from 'redux';

const 체중 = 100; // props

function reducer(state=체중, action) {
	if (action.type === '증가') {
		return state++;
	} else if (action.type === '감소') {
		return state--;
	}
	...
}
```

