---
layout: post
title: Redux Middleware
subtitle: Redux의 꽃, 미들웨어!
categories: React
tags: [React, Redux, middleware, redux-thunk]
---

Redux!!!
------------

드디어 React의 상태관리 패키지 중 하나인 Redux를 배웠다. 예전에 포트폴리오를 만들면서 접해봤을때 정말 직관적이지가 않아서

이해하는데 굉장히 힘이 들었던 기억이 있다. 하지만 계속해서 사용해보니 Flux 패턴도 이해하고 하니 정확한 동작방식을 이해 할 수가 있었다.

그래서 사실 Redux에 관련된 포스팅을 하지 않으려고 했으나 redux-thunk 라는 리덕스의 미들웨어를 사용해봄으로서 리덕스의 미들웨어에 대해서 포스티을 하면 좋겠다는 생각이 들었다.

그래서 이렇게 한번 리덕스 미들웨어에 대해 얘기 해보려고한다.

(Redux라는 이름 때문에 React에서만 사용가능한가 라고 생각할 수 도 있지만 사실 Redux는 다양한 곳에서 사용이 가능하다!)

### Middleware ###

사실 리덕스 처럼 전역으로 상태를 관리해주는 패키지는 많이 있지만 리덕스를 사용하는 큰 이유 중 하나는 바로 미들웨어이다.

express를 배울때 미들웨어에 대해 잠깐 배웠는데 그것과 굉장히 비슷하게

`액션이 디스패치 된 다음, 리듀서가 해당 액션을 받아 상태를 변경하기 전에 추가적인 작업을 해주는 것`

이 바로 리덕스의 미들웨어이다.

추가적인 작업은 여러가지가 있는데 다른 [블로그](https://react.vlpt.us/redux-middleware/)에서 인용을 해보자면

- 특정 조건에 따라 액션이 무시되게 만들 수 있다.
- 액션을 콘솔에 출력하거나, 서버쪽에 로그를 남길 수 있다.
- 액션이 디스패치 됐을 때, 이를 수정해서 리듀서에게 전달될 수 있도록 할 수 있다.
- 특정 액션이 발생했을 때, 이에 기반하여 다른 액션이 발생되도록 할 수 있다.
- 특정 액션이 발생했을 때, 특정 자바스크립트 함수를 실행 시킬 수 있다.

등이 있다.

#### Middleware 만들기 ####

보통 실무에서 직접 미들웨어를 만들 일은 없다고 한다. 하지만 미들웨어를 어떻게 만드는지 이해하고 나면 리덕스에 대해 더 디테일하게 알게되고

결과 더 효과적으로 사용 할 수 있다고 생각하기에 여기서 어떻게 미들웨어를 만드는지 한번 알아보자!

리덕스 미들웨어를 만들때 보통 사용하는 템플릿이 있는데 다음과 같다.

```jsx
const middleware = store => next => action => {
  // what to do
}
```

express의 미들웨어와 미묘하게 닮아 있다는것도 위의 템플릿에서 확인이 가능하다.

템플릿이 굉장히 복잡해보이는데 우리는 javascript의 고차함수와 closure에 대해 배웠기 때문에 위의 템플릿이 의미하는 바가 무엇인지 알 수 있을 것이다.

위의 템플릿을 이해하기 쉽게 다시 적어보자면

```jsx
const middleware = store => {
  return next => {
	return action => {
	  // what to do
	}
  }
}
```

처럼 된다. 즉 `미들웨어는 함수이고 함수를 연달아서 두번 리터하는 함수`인 것을 알 수 있다.

이제 각 함수들의 매개변수가 무엇을 의미하는지 한번 알아보자!

##### store #####

먼저 store는 리덕스 스토어의 instance 이다. 우리는 스토어 안에 dispatch, getState, subscribe과 같은 내장 함수가 들어있다는 것을 알 수 있다.

##### next #####

next는 express의 미들웨어의 next와 비슷하게 액션을 다음 미들웨어 함수에게 전달해주는 함수이다.

그렇기에 `next(action객체)`와 같은 형태로 사용하게 된다.

만약 다음 미들웨어가 존재 하지 않는다면 바로 리듀서에게 액션 객체를 전달한다.

(뇌피셜이긴 한데 express의 미들웨어랑 비슷하게 리듀서도 결국 함수이기때문에 리덕스의 마지막 미들웨어로 생각해도 되지않을까 싶다.)

##### action #####

action은 문자그대로 액션 객체를 나타내는 것이다. 더 정확하게 말해보자면 현재 처리하고 있는 액션 객체를 뜻한다.

#### Middleware 등록 ####

redux-thunk라는 리덕스 미들웨어를 사용해보았다면 알겠지만 해당 미들웨어를 사용하기 위해서는 어딘가에 등록할 필요가 있다.

등록을 하기위해서는 처음 creatStore 함수를 이용해서 스토어를 만들때 applyMiddleware 함수로 미들웨어를 등록한다.

당연하게 리덕스 스토어는 여러 미들웨어를 등록할 수 있고 위에서 언급한대로 등록한 순서대로 미들웨어 함수가 호출된다.

next를 설명하면서 얘기했지만 `next(action객체)`를 호출하여 다음 미들웨어로 액션이 넘어가고 만약에 미들웨어 내에서 다른 액션을

추가적으로 발생시킬 필요가 있다면 `store.dispatch`를 이용해서 할 수 있다. (왜냐하면 위에서 말했다시피 미들웨어 함수는 store라는 매개변수가 있기 때문이다.)

#### Middleware 작성 ####

여기서 이제 리덕스 미들웨어를 한번 직접 작성해보겠다.

express의 미들웨어를 공부할 때 만들어보았던 로깅해주는 미들웨어를 만들어 보겠다. (redux-logger라는 좋은 미들웨어가 있으니 실제로 로그를 해주는 미들웨어를 사용할때는 이것을 사용하자!)

```jsx
const logger = store => next => action => {
  console.log(action);
  const result = next(action); // 다음 미들웨어나 리듀서에게 액션을 전달하고 반환되는 값을 result 변수에 저장한다.
  return result; // result 변수는 next(action)의 반환값 즉, dispatch(action)의 반환값과 같다. 기본은 undefined이다.
}
```

이런식으로 간단하게 미들웨어를 만들어 볼 수 있고 위에서 언급한대로 applyMiddleware 함수를 이용해서 미들웨어를 스토어에 등록 할 수 있다.

### redux-thunk ###

이렇게 까지 리덕스 미들웨어에 대해 정리를 해보니 redux-thunk 미들웨어에 대한 얘기도 하지 않을 수가 없겠다.

redux-thunk를 사용하기 전에 사용법에 대해 검색을 해보았다면 알 수 있는 것이 이 미들웨어는 14줄 밖에 되지않지만

redux의 비동기처리를 해주는 강력한 기능을 가진 미들웨어라는 얘기를 들어보았을 것이다.

이 14줄짜리 코드가 어떻게 비동기 처리를 할 수 있는지 한번 알아보자!!!

일단 redux-thunk를 사용하려면 어떻게 action creator 함수를 작성하는지부터 먼저 보겠다.

```jsx
const getData = () => async (dispatch, getState) => {
  const data = await axios.get('https://some.url.com');

  console.log(getState());

  dispatch({type: 'GET', payload: data});
}

// 파일 어디선가 데이터를 받아야할때
dispatch(getData());
```

redux-thunk를 사용해본적이 있다면 위의 코드와 같이 `함수를 반환`하는 action creator 함수를 만들었을 것이다.

그래서 코드의 마지막 줄과 같이 dispatch를 이용해서 action creator 함수를 호출하여 반환되는 값을 인자로 넣어 보내주는데

여기서 중요한 것은 `인자로 보내지는 것이 함수`라는 것이다!

이것을 이해하고 다음 redux-thunk의 코드를 보자!

```jsx
const thunk = store => next => action => {
  typeof action === `function`
	? action(store.dispatch, store.getState)
	: next(action)
}
```

redux-thunk의 코드가 위의 코드와 동일하다는 것이 아니라는 것을 말해주고 싶고

위의 action creator 함수가 반환한 것이 함수이기 때문에 redux-thunk 라는 미들웨어가 저런식으로 동작하는지 알 수 있다.

### 마치며 ###

리덕스 미들웨어에 대해 이렇게 정리를 해보았다.

이때까지는 그냥 이렇게 쓰는거니까 하면서 사용을 했었는데 이렇게 정리를 해보니 리덕스의 핵심기능 중 하나가 미들웨어인것을 알 수 있게 되었다.

미들웨어를 직접 만들어서 사용해볼일은 위에서 말한대로 없을것 같긴하지만 동작 방식을 알게되니

redux-thunk도 어떻게 동작하는지 알게되어 더 효율적으로 사용할 수 있을 것 같다.

[reference](https://react.vlpt.us/redux-middleware/)
