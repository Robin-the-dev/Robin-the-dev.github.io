---
layout: post
title: React useEffect
subtitle: React Reference
categories: React
tags: [React, hooks, useEffect]
---

React useEffect Hook
------------

아직 React useEffect Hook에 대해서는 부트캠프에서 배우지 않았지만 스스로 포트폴리오를 React로 만들고 했을 때

useState와 함께 많이 쓰이는 Hooks 중 하나이고 부트캠프 동기분과 함께 블로깅을 해나가기로 해서 약속 한대로 이렇게 미리 

useEffect에 관해 정리를 해보려고 한다.

#### useEffect란? ####

useEffect Hook은 컴포넌트가 mount, unmount, 그리고 update 될때 특정 작업을 할 수 있게끔 해주는 Hook이다.

가장 제일 많이 쓰이는 예를 들자면 React로 블로그 웹 어플리케이션을 만들었다고 가정하고 Posts 라는 컴포넌트가

게시글을 담은 Post라는 컴포넌트를 자식으로 두고 있을때 Posts가 렌더링이 될때마다 서버에서 새로운 게시글이 있는지

AJAX 통신으로 데이터를 가져와야하기 때문에 이럴때 useEffect를 많이 활용한다.

#### useEffect의 사용법  ####

일단 useEffect는 함수이고 총 두개의 인자를 받는다.

첫번째 인자는 Callback function으로 컴포넌트가 **처음 렌더링(mount) 될때 실행**된다. 그리고 그 Callback function은 함수를 리턴할 수 있는데

그렇게 리턴된 함수는 컴포넌트가 **사라질때(unmount) 실행**된다. 이러한 함수를 `cleanup 함수`라고 부른다.

두번째 인자로 `deps`라고 불리는 의존값을 가진 배열을 받을 수 있다. 이 배열을 빈 배열로 두면 위의 썼던 대로 컴포넌트가 처음 렌더링 될때

Callback function의 내용이 실행되고 컴포넌트가 사라질때 cleanup 함수가 실행된다.

하지만 배열안에 변화할 수 있는 의존값(state나 props)을 주게 되면 **의존값이 바뀔때도  Callback function의 내용이 실행**되고 

**의존값이 바뀌기 직전에도 cleanup 함수가 실행**된다.

deps 배열때문에 뭔가 헷갈릴 수 있는데 사실 크게 복잡한 내용은 아니고 아래의 코드를 보면 쉽게 이해할 수 있을것이다.

#### useEffect 사용 예제  ####

```jsx
// App.js
import React from 'react';
import Users from './Users';

const App = () => {
  return (
	<>
	  <Users />
	</>
  );
}

export default App;

// Users.js
import React, {useState} from 'react';
import User from './User';
import usersData from './usersData';

const Users = () => {
  const [data, setData] = useState(usersData);

  const onRemove = (id) => {
	setData(prevData => {
	  return prevData.filter(data => data.id !== id);
	});
  }

  const onToggle = (id) => {
	setData(prevData => {
	  return prevData.map(data => {
		if(data.id !== id) return data;
		else return {...data, isActive: !data.isActive};
	  });
	});
  }

  return (
	data.map(user => {
	  return <User key={user.id} user={user} onRemove={onRemove} onToggle={onToggle}/>
	})
  );
}

export default Users;

// User.js
import React, {useEffect} from 'react';

const User = ({user, onRemove, onToggle}) => {
  useEffect(() => {
    console.log('컴포넌트가 화면에 나타남 혹은 값이 바뀜');
    return () => {
      console.log('컴포넌트가 화면에서 사라짐 혹은 값이 바뀌기 직전임');
    };
  }, [user]);

  return (
	<div>
	  <span style={{
		color: user.isActive ? 'green' : 'red'
	  }}>{user.name}</span>
	  <span>{user.age}</span>
	  <button onClick={() => onRemove(user.id)}>Delete</button>
	  <button onClick={() => onToggle(user.id)}>Toggle Active</button>
	</div>
  );
}

export default User;

//userData.js (Dummy Data)
const usersData = [
  {
	id: 1,
	name: 'James',
	age: 15,
	isActive: false
  },
  {
	id: 2,
	name: 'Elsa',
	age: 17,
	isActive: false
  },
  {
	id: 3,
	name: 'Snowman',
	age: 25,
	isActive: false
  }
]

export default usersData;
```

useEffect의 사용 예를 조금 길게 쓰게 됐는데 deps 배열안의 요소가 바뀔때마다 어떤부분이 실행되는지를 보여줄려고 코드를 작성하다보니 조금 길어졌다.

중요한 부분은 useEffect를 사용한 User.js 쪽인데 보면 알다시피 useEffect의 첫번째 인자로 Callback function을 넘겨 주었고 그 함수의 내용은

`컴포넌트가 화면에 나타남 혹은 값이 바뀜`을 콘솔 로그로 찍는 것이다.

그리고 그 함수는 또 함수를 리턴하는데 리턴하는 함수의 내용은 `컴포넌트가 화면에서 사라짐 혹은 값이 바뀌기 직전임`을 콘솔 로그로 찍는 것이다.

그래서 User 컴포넌트가 처음 렌더링 될때와 사라질때는 당연히 Callback function의 내용과 cleanup function의 내용이 상황에 맞게 각각 실행 될것이고 

User 컴포넌트의 Toggle Active 버튼을 눌러서 state에 변화를 주면 그 **state의 값이 변경되기 직전에 cleanup function의 내용이 실행**되고 

**state의 값이 변경되면 Callback function의 내용이 실행**된다는 것을 알 수 있다.

deps 배열안에 값을 넣어줬을때 어떠한 순서로 무엇이 작동되는지를 아는 것이 중요하다!

#### deps가 없을 때 ####

위쪽에서 deps 즉 두번째 인자를 주는 경우만 이야기해서 무조건 넣어줘야한다고 생각할수도있는데 사실 두번째 인자인 deps는 optional 이다.

위에서 deps를 빈배열로 뒀을때 배열안에 state나 props 같이 변화하는 값을 줬을때에 대한 내용은 이미 적어두었고

두번째 인자로 배열 조차 주지않았을때에 대해서 얘기해보겠다.

deps를 주지 않으면 그냥 단순히 컴포넌트가 리렌더링 될때마다 Callback function의 내용이 계속해서 실행된다.

그래서 정리를 해보면

```jsx
// deps가 빈 배열인 경우
useEffect(() => {
  console.log('컴포넌트가 Mount 될 때 실행');
  
  return () => {
	console.log('컴포넌트가 Unmount 될 때 실행');
  }
}, []);

// deps의 요소로 변화하는 값이 있는 경우
useEffect(() => {
  console.log('컴포넌트가 Mount 될 때와 state가 Update 되면 실행');

  return () => {
	console.log('컴포넌트가 Unmount 될 때와 state가 Update 되기 직전에 실행');
  }
}, [state]);

// deps가 없는 경우
useEffect(() => {
  console.log('컴포넌트가 Mount 될 때와 컴포넌트가 Re-rendering 될 때 실행');

  return () => {
	console.log('컴포넌트가 Unmount 될 때 실행');
  }
});
```

### 마치며 ###

이번에는 미리 useEffect Hook에 대해 정리를 해봤다.

React를 사용하게 되면 정말 많이 쓰는 Hooks 중 하나이기 때문에 이렇게 정리를 하고 나니 뿌듯하다.

계속해서 이렇게 블로깅을 할 수 있도록 노력해야겠다.
