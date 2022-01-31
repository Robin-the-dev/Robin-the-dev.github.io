---
layout: post
title: Node.js with Express 기초 (feat. CORS)
subtitle: Node.js with Express Reference
categories: NodeJS
tags: [NodeJS, CORS, Express]
---

Node.js with Express
------------

드디어 Backend를 시작했다! 다행히 Node.js를 이용해 Backend server를 구현하기 때문에 수월하게 진행 할 수 있었다.

이번 학습을 통해 새롭게 알게된 Node.js관련 정보를 지금 포스트에 한번 적어보려고 한다.

참고: Express를 이용해 서버를 구현하기 때문에 정확히 Vanilla Node.js에 관한 내용이 아니라 Express에 관한 내용이 되겠다.

### Middleware ###

알고 있다시피 Vanilla Node.js에서는 미들웨어 추가가 어렵기 때문에 현재 보는 내용은 Express에 관한 내용이다.

Express의 장점이라고 한다면 **자체 라우터를 지원**하고 **미들웨어 추가**가 편리하다.

그리고 일반적으로 Node.js의 http모듈을 이용해 서버를 구현하는 것보다 훨씬 간결하고 편하게 쓸 수 있다!

다시 현재 챕터에서 얘기해보려는 Middleware로 돌아와서 Express에서 Middleware는 받은 요청에 대해서

본인이 적용해놓은 Middleware를 차례대로 적용시켜 필요한 기능을 더하거나 요청이 온전치않을때 빼거나 하는 기능을

중간에서 처리하는 것이다.

중요한것은 차례대로 적용을 시킨다는 것이 중요한 포인트이다.

일반적으로 모든 요청에 대해 Middleware를 적용시킬때 `app.use();`를 사용할 수 있는데 use함수의 인자로

Middleware를 주면된다.

```nodejs
app.use((req, res, next) => {
  console.log(`HTTP: url -> ${req.url}, method -> ${req.method}`);
  next();
});
```

위의 코드는 모든 요청에 대해 로그를 남겨주는 Middleware를 직접 만들어서 app.use()를 이용해서 적용해주었다.

꼭 익명함수로 해줄 필요는 당연히 없다.

```nodejs
const logger = (req, res, next) => {
  console.log(`HTTP: url -> ${req.url}, method -> ${req.method}`);
  next();
}

app.use(logger);
```

이런식으로 해주어도 결과는 동일하다.

app.use()를 이용하여 전체 요청에 대해 적용하는 방법 말고 각 요청에 대해 특정 Middleware를 적용 시키는 방법도 있다.

```nodejs
const logger = (req, res, next) => {
  console.log(`HTTP: url -> ${req.url}, method -> ${req.method}`);
  next();
}

app.get('/', logger, (req, res) => {
  res.send('Hello World!');
});
```

이런식으로 endpoint가 '/'일때 app.get()요청을 받는 경우에만 logger Middleware를 적용 해주었다.

코드에서 보이는 것처럼 실직적으로 get요청을 처리해주는 Callback Function 앞에다가 추가해줄 Middleware를

추가해주면 된다.  그러면 logger 함수안에 보이는 next()부분에 의해서 logger가 적용 된 후에 다음 Middleware를 불러주는데

위의 코드에서 다음 Middleware는 실질적으로 get요청을 처리해주는 Callback Function이기 때문에 바로 콜백 함수가 실행된다.

Middleware는 이런식으로 적용하면 된다.

하지만 가끔씩 Middleware의 순서가 중요할때가 있다. 그 경우에 대해서는 다음 챕터에서 설명해보겠다!

### Middleware의 순서 ###

위에서 보았다시피 Middleware는 next라는 인자를 받으며 next라는 인자가 함수로써 Middleware의 안에서 실행이 되면

다음 Middleware를 불러준다.

이러한 특성때문에 Middleware의 적용순서가 중요한 경우가 있다.

코드로 한번 살펴보자!

```nodejs
const express = require('express');
const cors = require('cors');

const app = express();

const PORT = 4999;

const corsOptions = {
  origin: 'http://localhost:3000',
  methods: ['GET', 'POST', 'PUT', 'DELETE', 'OPTIONS'],
  allowedHeaders: ['Content-Type', 'Accept'],
  maxAge: 10
}

// express 자체 라우터
const router = express.Router();

// 미들웨어 순서를 항상 조심하자!!!
app.use(cors(corsOptions)); // 이 경우에는 CORS 에러 없이 정상 작동됨!
app.use(express.json({strict: false})); // for parsing application/json
app.use(express.urlencoded({ extended: true })); // for parsing application/x-www-form-urlencoded
app.use(router); // express 자체 라우터 적용

router.get('/', (req, res) => {
  res.statusCode = 200;
  res.send('OK');
});

router.post('/', (req, res) => {
  res.statusCode = 201;
  res.send('Created');
});

app.listen(PORT, () => {
    console.log(`express server listen on ${PORT}`);
});
```

위와 같이 cors() Middleware를 가장 먼저 적용해주면 CORS 에러 없이 정상 작동된다.

하지만

```nodejs
const express = require('express');
const cors = require('cors');

const app = express();

const PORT = 4999;

const corsOptions = {
  origin: 'http://localhost:3000',
  methods: ['GET', 'POST', 'PUT', 'DELETE', 'OPTIONS'],
  allowedHeaders: ['Content-Type', 'Accept'],
  maxAge: 10
}

// express 자체 라우터
const router = express.Router();

// 미들웨어 순서를 항상 조심하자!!!
app.use(express.json({strict: false})); // for parsing application/json
app.use(express.urlencoded({ extended: true })); // for parsing application/x-www-form-urlencoded
app.use(router); // express 자체 라우터 적용
app.use(cors(corsOptions)); // express 자체 라우터를 먼저 적용하고 Cors() Middleware를 적용해주면 CORS 에러가 발생한다

router.get('/', (req, res) => {
  res.statusCode = 200;
  res.send('OK');
});

router.post('/', (req, res) => {
  res.statusCode = 201;
  res.send('Created');
});

app.listen(PORT, () => {
    console.log(`express server listen on ${PORT}`);
});
```

위의 코드 처럼 express 자체 라우터를 app.use()로 먼저 적용해주고 그 후에 cors() Middleware를 적용해주면

cors() Middleware가 제대로 적용되지 않아 CORS 에러가 발생한다.

위의 코드를 보고 Middleware의 적용 순서가 중요하다고 깨달을 수 있다. router를 Middleware로 적용해주기전에

cors() Middleware를 적용해주어야지 router가 CORS 헤더가 적용되고 제대로 요청과 응답을 보낼 수 있다.

이렇게 Middleware의 적용 순서가 중요하다는 것을 알 수 있다.

P.S: express 자체 router를 사용하지 않고 그냥 app.get()이나 app.post()를 사용하면 router를 애초에 Middleware로서

적용하기 않기때문에 cors() Middleware를 언제 적용하던 상관없다. 하지만 조금만 프로그램이 커지고 받아야할 요청이 많아지면

당연히 endpoint 또한 많아지기 때문에 보통 router를 사용하여 코드를 잘 정리해주는것이 보통이다.

그렇기 때문에 항상 Middleware의 순서에 유의하며 쓸데없이 시간을 날리지 않도록 하자!

### CORS ###

CORS에 대해서 따로 포스팅을 써볼까 했지만 위에서 CORS Middleware에 대해 예를 들었기 때문에 쓴 김에 이 포스트에서

CORS까지 정리를 해보려고 한다.

CORS는 `Cross Origin Resource Sharing`의 준말으로 굳이 해석하면 **다른 오리진에 대한 자원 공유**정도로 말해 볼 수 있겠다.

이런 CORS policy가 생긴 이유를 간략하게 설명해보면 예전에는 데이터를 받는 서버와 클라이언트 사이드의 도메인이 동일하여

데이터에 대한 안정성과 신뢰성이 보장되어있었다. (즉, 같은 오리진에서 데이터를 주고 받는다는 얘기이다.)

하지만 알다시피 현대 사회에서의 웹 어플리케이션은 살짝 복잡해졌다. 무슨말이냐하면은 내가 제공받을 데이터를

나의 서버 뿐만 아니라 다른 서버에서도 받을 수 있다는 얘기이다.

즉, 오리진이 다르다는 얘기가 되겠다.

이때만해도 브라우저는 다른 오리진에 대해서 자원 공유를 금지하였다. 하지만 위의 얘기처럼 많은 웹 어플리케이션이

데이터를 다른 서버에서 들고 오는 경우가 많아졌기에 회사들의 강한 요청으로 인해 브라우저도 CORS에 대해 몇가지 조건만 지키면 허용해주기 시작했다.

그리고 그 조건은 데이터를 요청하는 측의 도메인이 서버의 white list에 등록되어 있냐는 것이다.

CORS 검사는 브라우저단에서 일어나기 때문에 서버는 request가 들어왔을때 Access-Control-Allow-Origin의 값을 response의 헤더에 넣어서

응답을 보내준다. 그러면 브라우저는 response의 헤더를 뜯어보며 Access-Control-Allow-Origin에 요청하는 측의 도메인 서버가 들어있는지를 확인한다.

만약에 들어있으면 성공적으로 응답을 받아들일것이고 만약에 없다면 해당 응답을 버려버리고 CORS 에러를 내뿜는다.

CORS가 동작하는 방법은 크게 세가지가 있다.

Simple request, Preflight request, Credential request 이다.

CORS가 동작하는 대표적인 시나리오는 Preflight request이므로 여기에서는 이것에 대해서만 얘기해보고

나머지 두가지는 좋은 글이 있는 링크를 남겨 대체하겠다.

#### Preflight request ####

Preflight(예비 비행)이라는 말 그대로 본 요청을 서버에 보내기전에 OPTIONS 라는 method를 이용해서 예비 요청을 먼저 보내

CORS 검사를 진행하는 것을 말한다.

개념 자체는 어렵지 않기 때문에 개념적인 부분 보다는 실제 프로젝트에서 이러한 상황을 직면했을때

OPTIONS method도 신경을 꼭 써서 CORS 에러가 발생하지않도록 잘 코드를 써주는 것이 중요하다고 말하고 싶다.

그리고 또 중요한 것은 Access-Control-Allow-Origin 헤더를 OPTIONS method에만 적용해주면 안되고

꼭 본 요청의 method에도 적용을 해주어야지 정상적으로 데이터를 받아볼수있다.

[CORS 잘 정리해놓은 블로그](https://evan-moon.github.io/2020/05/21/about-cors/)

[CORS MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)

### 마치며 ###

부트캠프를 진행하면서 이제 백엔드에 접어들었다.

Node.js의 express를 이용할때 Middleware가 어떻게 적용되는지 알수있는 기회를 얻게되어 정말 좋았다.

그외에도 CORS에대해서 더 자세하게 알게되어 정말 웹에 대한 기초를 탄탄히 쌓을수있게되어 정말 즐겁고 재밌었다.

계속 해서 이렇게 블로그를 정리해나가자!
