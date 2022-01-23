---
layout: post
title: Javascript의 비동기
subtitle: Javascript Reference
categories: JavaScript
tags: [JavascriptAsync, Javascript]
---

비동기
------------

회고 부분에도 언급했지만 이번 4주차에서 배운 이 비동기라는 부분이 개인적으로 한달간 부트캠프를 진행하면서 제일 빡셌던 부분이었던것 같다.

취업 준비를 하면서 React로 포트폴리오를 만들고 했기 때문에 비동기라는 부분에 있어 간략한 개념과 사용적인 부분에 있어서는 이미 숙지가 

되어있었다. 하지만 이번 부트캠프에서 정확히 Promise와 Async/Await이 어떻게 동작하는지 세세한 부분과 개념에 대해 학습을 진행하였다.

그렇게 공부를 해보니 나는 이미 다 알고 쓰고 있다고 생각을 했는데 위의 기술들에 대해 1/4도 모르고 그냥 남들이 만들어 놓은 packages를 이용해서

사용만 하고 있었다.

그래서 이번에 제대로 위의 기술들을 학습해나가며 1주차부터 3주차까지에서 배운 것들에 비해 살짝 난이도가 있다고 느꼈다.

헷갈린 부분이 있었기에 검색과 스터디를 통해 답을 찾고 개념에 대해 완벽하게 정리를 했기 때문에 이렇게 블로그에도 배운 것들을 남겨보려고 한다.

### Javascript에서 비동기란? ###

Javascript에서 비동기 처리란

`특정 코드가 완료하는데까지 일정한 시간이 걸린다면 절차적으로 그 코드가 완료될때까지 기다렸다가 다음 코드로 진행하는 것이 아니라 해당 코드가 완료되어 실행되기 전에 다음 코드로 바로 넘어가는 특성`

을 말한다.

이러한 Javascript의 비동기 처리가 왜 필요하느냐 라고 물을 수 도 있다.

예를 한 번 들어보자!

React Web application에서 서버로부터 데이터를 받아온다고 가정하겠다. 하지만 이 데이터가 생각보다 커서 가져오는데 시간이 조금 걸린다.

만약 비동기 처리를 허용하지 않는다면 이 데이터를 가져오는데 걸리는 시간이 15초라면 이 데이터와 관련없는 컴포넌트도 렌더링이 되기까지

15초를 기다렸다가 렌더링이 되어야 한다. 15초동안 빈화면을 보고있는 것은 유저입장에서는 큰 고역이다.

이러한 경우가 아니더라도 서버로 보내는 요청이 한개가 아니라 수십개가 된다고 가정하면 동기적으로 처리하기 위해서는 수십개의 요청이

모두 다 완료되어야만 다음 스텝으로 넘어 갈 수 있다.

이러한 경우를 위해 비동기 처리가 필요하다.

### 비동기 처리방식의 문제점 ###

위와 같은 경우때문에 비동기 처리가 필요하지만 그 비동기 처리 때문에 문제가 발생하는 경우도 있다.

아래의 코드를 보면서 한번 얘기보겠다!

#### 문제점 ####

```javascript
const getData = () => {
  let data;

  var settings = {
	"url": "https://api.covid19api.com/",
	"method": "GET",
	"timeout": 0,
  };

  $.ajax(settings).done(function (response) {
	data = response;
  });

  return data;
}

console.log(getData()); // undefined
```

사실 Javascript의 fetch method를 이용해서 계속 설명하려고 해봤는데 fetch method는 애초에 리턴하는 것이 promise 객체이기 때문에

비동기 처리방식의 문제점을 설명하기가 어려워서 부트캠프에서 배우지는 않았지만 jQuery의 AJAX통신을 하는 function을 이용해서

설명을 해보려고 한다.

$.ajax() 함수는 별게 아니고 (settings 객체의 method를 GET으로 주었기에) 그냥 GET method로 서버로부터 데이터를 가져와주는 함수라고 생각하면 될듯하다.

위와 같이 코드를 쓰게되면 ajax() 함수는 비동기적으로 작동을 하기 때문에 데이터를 가져오는 사이 getData 함수의 리턴부분이

이미 실행되어버리고 그 결과물을 콘솔 로그 찍어보면 undefined가 찍히는 걸 볼 수 있다.

#### 해결 방법 ####

##### 1. Callback function #####

위와 같은 문제의 해결 방법은 여러가지 있지만 처음에는 Callback function을 활용하여 해결하는 방법부터 시작해보겠다.

```javascript
const getData = (callbackFunc) => {
  let data;

  var settings = {
	"url": "https://api.covid19api.com/",
	"method": "GET",
	"timeout": 0,
  };

  $.ajax(settings).done(function (response) {
	data = response;
	callbackFunc(data);
  });
}

getData(data => {
  console.log(data); // data object
});
```

이런식으로 콜백 함수를 활용하면 데이터를 서버로 부터 받을때까지 기다렸다가 콜백 함수가 호출이 되기 때문에 데이터를 온전히 받을 수 있다.

##### 2. Promise #####

Promise를 이용해서도 해결 할 수 있다. 여기서 부터는 fetch() 를 사용해서 얘기를 해보겠다.

fetch()는 리턴하는 것이 Promise 객체이기 때문에 then이나 catch를 이용해서 계속 chaining을 하며 동기적으로 실행 해줄수있다.

```javascript
const getData = () => {
  var requestOptions = {
    method: 'GET',
    redirect: 'follow'
  };

  return fetch("https://api.covid19api.com/", requestOptions)
    .then(response => response.json())
}

getData()
  .then(json => console.log(json))
  .catch(error => console.log('error', error));
```

Promise를 이용하면 이런식으로 chaining을 이용해 비동기 처리되는 부분을 동기적으로 처리해줄수있다.

then 함수와 catch 함수도 Promise 객체를 리턴하기 때문에 계속해서 chaining을 할 수 있다.

Promise의 더 자세한 부분은 뒤쪽에서 다시 한번 얘기해보겠다.

##### 3. Async/Await #####

어떻게 보면 가장 간단하게 코드를 간결하게 쓸 수 있는 방법이라고 생각한다.

Async/Await을 활용하면 동기적으로 진행되는 것 같이 해결 할 수 있다.

```javascript
const getData = async () => {
  var requestOptions = {
    method: 'GET',
    redirect: 'follow'
  };

  const data = await fetch("https://api.covid19api.com/", requestOptions)
    .then(response => response.json());

  console.log(data);
}

getData();
```

getData 함수가 비동기적으로 작동하는 함수니까 async라는 키워드를 써주고 getData 함수내에서

실질적으로 비동기적으로 실행되는 코드에 대해서 기다렸다가 실행하라는 의미의 await 키워드를 써주면 된다.

이렇게 쓰면 getData 함수내에서는 저 await 키워드를 사용한 줄이 동기적으로 실행된다.

그리고 await을 사용하게 되면 Promise 객체가 반환되는게 아니라 결과값이 반환이 된다.

그래서 data 변수를 콘솔 로그 찍어보면 Promise 객체가 아니라 response에 json() 함수를 적용해준 최종 결과값이 찍히는것을 확인 할 수 있다.

### Promise 객체 만들기 ###

위의 부분에서는 Promise 객체를 리턴하는 함수를 이용해서 then과 catch를 이용해서 chaining하는 방법에 대해서만 언급했다.

여기서는 Promise 객체를 어떻게 만드는지 그리고 어떻게 동작되는지에 대해서 얘기해보겠다.

```javascript
const newPromise = () => {
  return new Promise((resolve, reject) => {
  	var settings = {
  	  "url": "https://api.covid19api.com/",
  	  "method": "GET",
  	  "timeout": 0,
  	};

  	$.ajax(settings).done(function (response, status) {
	  resolve(response);
  	});
  });
}

newPromise()
  .then(res => console.log(res))
  .catch(err => console.log(err));
```

아직 나도 정말로 Javascript를 자유자재로 쓰는 정도는 아니기에 resolve와 reject를 다 사용할 수 있는 예를 만들기가 힘들어서

일단은 resolve만 사용할 수 있는 예를 만들어 봤다. (지금 단계에서는 Promise의 이해가 더 중요하기에 굳이 예를 만들기 위해 더 검색을 하진 않았다.)

(위의 done() 함수가 에러를 발생했을때는 아예 실행이 되지 않기 때문에 reject 함수를 적어주는것이 무의미하다.)

코드에서 보이는 것처럼 new 키워드를 사용해서 Promise 객체를 만들고 그것을 리턴하면 Promise 객체를 반환할 수 있다.

Promise() 생성자는 콜백 함수를 인자로 받는데 그 함수는 인자로 resolve와 reject를 받을 수 있다.

Promise 객체는 총 세개의 상태를 가지는데 대기 상태인 Pending, 실행이 되었을때의 상태인 Fulfilled, 뭔가 에러가 발생하거나 해서 거절된 상태인 Rejected가 있다.

처음에 new Promise()를 이용해서 새로운 Promise 객체가 만들어졌을때는 Pending 상태이고 콜백 함수가 실행되면서 콜백 함수내의 resolve() 함수가 실행되면

Fulfilled 상태가 되며 reject() 함수가 실행되면 Rejected 상태가 된다.

위의 코드처럼 resolve 함수의 인자로 response를 줬는데 저렇게 하면 Promise 객체 안에 결과값 (response)이 저장되어있다고 보면 되기 때문에

then 함수도 인자로 콜백 함수를 받고 그 콜백 함수에 매개변수를 지정해줘서 콜백 함수내에서 그 매개변수를 이용해서 접근이 가능하다.

콜백 함수내에서 리턴을 해주면 then 함수도 Promise 객체를 리턴해주기 때문에 그 리턴된 결과값이 저장되어있고 계속해서 chaining을 하면서

그 결과값에 접근이 가능하다.

만약에 아무것도 리턴해주지 않으면 결과값으로 undefined가 들어가게 된다.

자세한 것은 MDN 공식문서 then method 부분을 참조하면 될듯하다.

### 마치며 ###

이번 비동기 부분과 관련한 내용이 글은 길지가 않은데 직접 코드로 예제를 만들고 하고 해보니까 머리가 엄청 복잡해져서

글이 정말로 중구난방이다... 일단은 나만 알아볼수있는 Reference용으로만 보다가 내 스스로가 비동기에 더해 익숙해지고

Javascript 또한 능숙하게 사용 할 수 있게 된다면 글의 내용을 더 매끄럽게 수정할 필요가 있어 보인다.
