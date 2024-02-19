

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
	store.dispatch({type:'create', payload:{title:title, desc:desc:}});
">
```

1. dispatch에 action 객체를 전달({type:'create', payload:{title:title, desc:desc:}})
2. reducer를 호출해 state의 값을 변경
reducer는 변경될 state의 값을 받아 기존 state를 변경하고, 그 값을 리턴하는 가공자가 된다
3. subscribe를 통해 render함수를 재호출하여 변경된 state를 렌더링


## Redux 왜써요

특정 부품을 onClick했을때 서로간에 영향을 주는 형태의 부품이 있다면
갯수가 많아질수록 서로간의 종속성이 올라가기 때문에 부품이 아니게 됨


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