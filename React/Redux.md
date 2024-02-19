

## store

- store
	- state - 직접적으로 접근할 수 없다, 따라서 state를 변경하기 위해 사용하는 함수가 존재
		- dispatch
		- subscribe
		- getState
- reducer
	- 리듀서를 작성하는 것이 redux의 절반임
- render
	- UI를 만들어주는 우리가 작성할 코드

---

```js
var state = store.getState();

...

function render() {
	...
	document.querySelector('#app').innerHTML = `
		<h1>HELLO</h1>
	`
}
```

`render()`가 내부적으로 `getState()`를 호출해 state를 가져옴

`store.subscribe(render);`
store의 state값이 바뀔 때마다 render 함수가 호출되며 재렌더링을 할 수 있다.

```js
<form onsubmit="
	...
	store.dispatch({type:'create', payload:{title:title, desc:desc:}})
```



## Redux Toolkit

```jsx
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