
## 프로젝트 생성

- `npx create-react-app pwa-react --template cra-template-pwa`

## CSS

전역을 관장하는 CSS같은 경우 `index.css`
이외에는 `App.css`등 사용함

```js
import ReactDOM from 'react-dom/client';  
import 'bootstrap/dist/css/bootstrap.min.css';  
import './index.css';
...
```
부트스트랩 아래로 커스텀 CSS를 import해야 커스텀 CSS를 인식함

```sh
npm install react-bootstrap
```
리액트 전용 부트스트랩을 사용할 수 있음


## 컴포넌트

```jsx
import React from 'react';  
import { Link } from 'react-router-dom';  
import { Navbar, Nav } from 'react-bootstrap';  
  
function NavigationBar() {  
    return (  
        <Navbar bg="light" expand="lg" sticky="top" className="topbar">  
            <Navbar.Brand as={Link} to="/" style={{ fontSize: '1.2em' }}>MyApp</Navbar.Brand>  
            <Navbar.Toggle aria-controls="basic-navbar-nav" />  
            <Navbar.Collapse id="basic-navbar-nav">  
                <Nav className="ml-auto">  
                    <Nav.Link as={Link} to="/" style={{ fontSize: '1.1em' }}>홈</Nav.Link>  
                    <Nav.Link as={Link} to="/notice" style={{ fontSize: '1.1em' }}>공지사항</Nav.Link>
                </Nav>  
            </Navbar.Collapse>  
        </Navbar>  
    );  
}  
  
export default NavigationBar;
```

리액트 Router를 통해 라우팅 처리를 할 수 있다.


## 변수

```js
import React, { useState, useEffect } from 'react';  
import { Carousel } from 'react-bootstrap';  
  
function EventCarousel() {  
    const [events, setEvents] = useState([]);
    ...
```

상태 변화 감지가 필요한 변수에 대해서 위와 같이 사용이 가능하다.

## 추가

```sh
npm install react-items-carousel # 리액트 캐러셀
npm install axios # 비동기 fetch 호출용 라이브러
npm install react-router-dom # 라우터

```