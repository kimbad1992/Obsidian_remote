
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
