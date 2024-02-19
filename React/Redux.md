

## store

1. **store**
    - `store`는 Redux의 핵심이며, 애플리케이션의 전역 상태를 보관합니다. `dispatch`, `subscribe`, `getState`와 같은 메소드를 통해 상태를 관리합니다. 여기서 `dispatch` 메소드는 액션을 발송하여 상태를 변경하도록 요청하는 역할을 하며, `subscribe` 메소드는 상태가 변경될 때마다 실행할 콜백 함수를 등록하는 역할을 합니다. `getState` 메소드는 현재의 상태를 반환합니다.
2. **reducer**
    
    - 리듀서는 상태의 변화를 일으키는 함수입니다. 액션의 타입에 따라 상태를 어떻게 변경할지 결정합니다. 리듀서는 순수 함수여야 하며, 이전 상태와 액션 객체를 파라미터로 받아 새 상태를 반환합니다.
3. **action**
    
    - 액션은 상태 변화를 일으키기 위한 정보를 가진 객체입니다. `type` 필드를 필수로 가지며, 이 외에 상태 업데이트를 위한 추가 데이터를 담을 수 있습니다.
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

-> Redux의 경우
특정 부품을 onClick하면 store에 state가 변경되었음을 전달
store가 subscriber에게 state가 변경된것을 rendering 하도록 전달

__Redux Debugging Tool__을 쓸 수 있다(Time Traveling 가능)


## Redux 예시


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

1. **configureStore**
    
    - Redux Toolkit의 `configureStore` 함수는 스토어 설정을 간소화합니다. 리듀서, 미들웨어, 개발 도구 확장 등을 쉽게 구성할 수 있습니다.
2. **createReducer와 createAction**
    
    - `createReducer`와 `createAction` 함수는 리듀서와 액션 생성자를 더 쉽게 작성할 수 있게 해 줍니다. `createAction`은 액션 타입 문자열을 기반으로 액션 생성 함수를 만들어 줍니다. `createReducer`는 리듀서 로직을 더 직관적으로 작성할 수 있게 해 주며, 내부적으로 Immer 라이브러리를 사용하여 불변성을 자동으로 관리합니다.
3. **createSlice**
    
    - `createSlice` 함수는 액션 생성자와 리듀서를 한 번에 생성할 수 있게 해 줍니다. `name`, `initialState`, `reducers` 객체를 인자로 받아, 해당 리듀서와 액션 생성자들을 포함하는 슬라이스 객체를 반환합니다. 이는 Redux 로직을 더욱 간결하게 만들어 줍니다.