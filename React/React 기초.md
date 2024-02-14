
## 초기설정

`npx create-react-app 프로젝트명`

Node Package Manager로 create-react-app 라이브러리를 통해 프로젝트 생성
__Node.js__ 설치를 필요로 함(Node Package Manager는 딸려옴)

프로젝트 생성 후 초기 디렉토리

```
>node_modules // 프로젝트 라이브러리 보관함
>public // static 파일 보관함
>src // 실제로 코드짜는 곳(컴포넌트 등)
```

`package.json` - 프로젝트 정보

## JSX 문법

Javascript로 HTML 문서를 만들 수 있게 함

### className

class는 JSX에서는 `className` 이라는 속성으로 작성
```jsx
<div className="black-nav">  
    <h4>블로그임</h4>  
</div>
```

해당 컴포넌트에서 사용할 CSS는
`import './App.css';`와 같이 작성한다

### 데이터 바인딩

```jsx
let blog = '블로그임';
<div className="black-nav">  
    <h4>{ blog }</h4>  
</div>
```
중괄호를 이용해 변수 삽입이 가능하다.
id, className 등 속성에도 사용이 가능하다.

### style

```jsx
<h4 style={{color: 'darkred', fontSize: '16px'}}>{post}</h4>
```
중괄호 내부에 오브젝트 자료형으로 넣어준다
CSS는 `font-size` 라는 형태로 작성하면 JS 문법 상 마이너스로 인식하기 때문에
카멜케이스(`fontSize`)로 작성한다.

### 병렬 태그 불가

각 컴포넌트의 `return()` 내에는 병렬로 2개 이상의 태그를 기입하지 않는다

```jsx
return (
	<div>
		...
	</div>
	// 아래에 동일한 Depth의 div 태그가 병렬로 존재하면 에러
	<div>
	</div>
)
```


## State

`import {useState} from 'react'`
Javascript의 Destructuring 문법으로 state 선언
`let [작명1, 작명2] = useState(보관할 자료)`

```jsx
<h4>{작명1}</h4>

// 작명1은 state에 보관된 자료를 출력함
// 작명2는 state의 변경을 도와주는 함수
```

### Destructuring 문법

```js
let num = [1, 2];
// 위의 있던 값을 변수로 빼고 싶을 경우
let a = num[0];
let c = num[1];

// Destructuring 문법의 경우
let [a, c] = [1, 2];
```

### State를 사용하는 이유

일반 Javascript 변수의 경우 값이 변경되면 HTML에 반영이 되지 않는다.
State의 경우 값이 변경되면 HTML이 다시 재렌더링된다(HTML에 반영이 된다.)

### 이벤트 핸들러

```jsx
let [like, setLike] = useState(0)
...
<h4>{title[0]} <span onClick={}>🫰</span> {like} </h4>
```

onClick 핸들러 내부에는 함수만 넣어야한다.

`<span onClick={console.log('하이')}>🫰</span>`
는 동작하지 않는다

`<span onClick={() => {console.log('하이')}}>🫰</span>`
는 동작한다.


### State 변경

```jsx
let [like, setLike] = useState(0)
...
<h4>{title[0]} <span onClick={()=>{ setLike(like+1) }}>🫰</span> {like} </h4>
```
state 변경 함수를 통해 변경될 값을 전달한다.

```jsx
let [title, setTitle] = useState(['남자코트 추천', '강남 우동 맛집', '파이썬 독학']);
...
<button onClick={() => {
	let copy = title;
	copy[0] = '여자코트 추천';
	setTitle(copy);
}}

 /*
 위의 경우 동작하지 않음
 state 변경 함수의 경우 기존 state가 신규 state와 동일하면 변경이 이루어지지 않는다
 데이터는 변경되었지만 title Array의 포인터가 가리키는 주소가 변하지 않았기 때문에
 setState에서 동일한 state로 인식하기 때문
 */

<button onClick={() => {
	let copy = [...title];
	copy[0] = '여자코트 추천';
	setTitle(copy);
}}

/*
위의 경우는 변경됨
Spread 연산자를 통해 값을 새로운 Array로 생성하기 때문에 주소값이 변경되고
state도 변경된다.
*/
```

__위와 같이 state가 array/object인 경우 shallow copy를 만들어서 수정해야함__

반복적인 HTML을 축약할 때
큰 페이지인 경우
자주 변경되는 경우
