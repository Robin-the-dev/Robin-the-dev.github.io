---
layout: post
title: React 기초
subtitle: React Reference
categories: React
tags: [React, state, hooks, props, useState]
---

React 기초
------------

3주체 회고에서도 언급했다시피 3주차에는 React와 재귀에 대해서 배웠다.

이번 포스트는 React 기초에 대한 Reference를 작성해보려고 한다.

아직까지 크게 어려운 부분은 없기에 바로 본문에서 정리를 해보겠다.

### React의 기본 ###

#### Single Page Application ####

여기에서는 React의 기본적인 부분에 대해 정리를 해보겠다.

일단 React로 흔히들 말하는 Single Page Application (이하 SPA)을 만들 수 있다.

SPA는 기존의 HTML을 이용해서 만드는 웹페이지와는 다르게 해당 웹의 필요한 부분만 렌더링을 해주면서 보다 효율적으로 웹페이지를 구성할 수 있게 해주는 방식이다.

무슨말이냐 하면은 기존의 HTML을 이용해서 웹페이지를 만들면 현재 페이지에서 다른 페이지로 넘어 갈 때 해당 페이지를 모두 가져오기 때문에 쓸데없이 트래픽이 증가하게 되고 이와 같이 해당 페이지를 다시 브라우저에

띄워줘야 하기 때문에 다른 페이지로 넘어갈때 마다 새로고침이 일어나게 되어 사용자 경험 (UX)를 저하 시켰다.

반면에 SPA는 Re-rendering이 필요한 부분만 새로고침 없이 일어나기 때문에 더 나은 UX를 제공하였다.

아래에 SPA의 장점과 단점에 대해 정리 해보겠다.

장점:
1. 웹페이지 내에서 이동시 Re-rendering이 필요한 곳만 일어나기 때문에 기존의 HTML로 만들어진 웹페이지 보다 빠르다.
2. 서버에서는 요청 받은 데이터만 보내주면 되기 때문에 트랙픽 문제가 줄어든다.
3. 전체 페이지를 rendering 하지 않기 때문에 더 나은 UX를 제공한다.

단점:
1. Search Engine Optimisation (SEO)가 좋지 않다. 왜냐하면 index.html 파일 자체는 처음에 서버로 부터 받아올때는 텅빈 HTML 파일이기 때문에 검색엔진이 이를 이해하기 힘들다. 이것을 해결하기 위해 Server Side Rendering이라는 것이 필요한데 말 그대로 클라이언트 사이드로 보내기전에 렌더링을 거친 다음 렌더링이 완료된 파일을 보내는 것이다. 대표적인 Framework로 Next.js가 있는데 이번에 Release 될 React 18에 서버 사이드 컴포넌트라는 기술이 포함되어 있다고 하여 해당 부분을 배우면 될것 같기도 하다.
2. 처음 렌더링 할때 모든 JSX 파일을 다운로드 해야되기 때문에 처음 로딩이 오래 걸린다. 이 문제도 SSR과 Code splitting을 이용하면 해결 할 수 있다.

이렇게 장점과 단점에 대해 정리를 해보았는데 단점 부분을 보면 대부분 SSR을 이용하면 이러한 단점들이 해소가 되기때문에 React는 굉장히 편하고 좋은 Framework라고 생각 할 수 있다.

#### One-way data flow (단방향 데이터 흐름)와 props ####

React는 Component를 이용해 페이지를 구성하게 되는데 Component 간의 데이터의 흐름이 단방향이다.

말그대로 한 방향으로만 데이터를 전해줄 수 있다는 말이고 그 방향은 항상 위에서 아래이다.

즉 부모 컴포넌트가 자식 컴포넌트에게 데이터를 전해줄 수 있다.

React의 이러한 특성 때문에 처음 웹페이지를 구성할때 이러한 부분을 잘 생각해서 데이터의 흐름까지 체계를 미리 잡아놓는것이 권장 된다.

React에서 데이터를 보낼때는 functional component를 기준으로 props라는 객체를 통해 부모가 자식에게 데이터를 보낼 수 있다.

```jsx
// App.js
import React from 'react';
import Content from './Content';

const App = () => {
  return (
	<div>
	  <Content content={'Hello World!'} />
	</div>
  )
}

export default App;

// Content.js
import React from 'react';

const Content = ({content}) => {
  return (
	<div>{content}</div>
  );
}

export default Content;
```

props는 객체이기 때문에 위의 코드처럼 Destructuring assignment (구조분해 할당)를 이용해서 바로 접근도 가능하고 `props.content` 로도 접근이 가능하다.

#### React의 state와 hooks ####

현재 정리하려는 React의 state와 hooks는 functional component 기준이다.

State, 즉 상태는 React에서 변할 수 있는 값을 말한다.

이 state를 기준으로 관련있는 component들이 re-rendering이 되기 때문에 매우 유용하다고 할 수 있다.

State를 만드는 방법은 React의 useState라는 hook을 사용해서 만들 수 있다.

기본적인 사용법은 아래 코드를 보자!

```jsx
// App.js
import React, {useState} from 'react';

const App = () => {
  const [state, setState] = useState('newState');
  // 위의 코드와 동일한 코드이다.
  // const newState = useState('newState');
  // const state = newState[0];
  // const setState = newState[1];

  return (
	<div>
	  <div>{state}</div>
	</div>
  );
}

export default App;
```

코드와 같이 useState를 함수로서 사용하고 반환되는 값은 배열이기에 구조분해할당으로 받을 수 있다.

useState의 반환값의 첫번째 요소는 상태 변수이고 두번째 요소는 그 상태를 변경하기위한 setter 함수이다.

상태 변수는 절대로 직접적으로 조작해서는 안되고 반듯이 setter 함수를 통해 변화를 주어야한다.

그래야만 해당 상태와 관련있는 component들이 상태가 변한 것을 인식하고 다시 렌더링 할 수 있다.

그리고 useState 함수의 인자로 해당 상태의 초기값을 줄 수 있다. 위의 코드는 초기값을 문자열 'newState'로 주었기 때문에 state 변수의 초기값은 'newState'이다.

자식 컴포넌트에서 해당 상태를 변경하기 위해서는 props를 이용해 setter 함수를 자식에게 전해주면 된다.

```jsx
// Content.js (자식 컴포넌트라고 가정)
// 코드 생략
const Content = ({setState}) => {
  return (
	<div>
	  <button onClick={() => setState(prevState => prevState + ' button clicked!')}></button>
	</div>
  );
}
// 코드 생략
```

이런식으로 props를 통해 setter 함수를 보내줄 수 있고 setter 함수의 인자로 callback function을 줄수가 있는데 위의 코드와 같이 해당 state의 값을 참조해서 state를 바꿀 수 도 있다.

State는 문자열, 숫자와 같은 primitive data types 이외에도 배열 혹은 객체와 같은 reference data types도 가질 수 있기 때문에 블로그 같은 웹 어플리케이션을 만든다고 가정 할 때

데이터로 포스트들의 배열 (요소는 객체인)을 가져와 useState로 상태를 만들어주면 간단하게 블로그 페이지를 만들 수 있다.

### 마치며 ###

이렇게 React의 기초에 대해 정리를 해보았다.

실제로 포트폴리오를 만들면서 알고 있었던 부분이지만 이렇게 블로그로 정리 해놓으니까 그 동안 단순히 도구로서 쓰기만 했던것에 비해 그 도구가 어떤것이지 알게 되었다.

이것이 블로깅의 장점이지 않나 싶다.

계속해서 꾸준히 블로깅을 할 수 있도록 해야겠다.
