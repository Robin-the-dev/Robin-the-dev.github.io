---
layout: post
title: Web Server와 Web Application Server
subtitle: 웹 서버와 웹 어플리케이션 서버의 차이
categories: Server
tags: [WebServer, WAS]
---

WAS가 뭐야?!
------------

이번 주 차에 Node.js를 배우면서 당연하게 웹 서버에 대한 개념도 요구 되었다.

문제는 웹 서버는 무엇인지 알겠는데 관련된 자료를 찾다보니 웹 어플리케이션 서버 (이하 WAS)라는 개념이 튀어나왔다.

이번 포스트는 웹 서버와 WAS가 무엇을 하는지 그리고 어떤 차이가 있는지에 대해서 적어보려 한다.

### Static Pages와 Dynamic Pages ###

웹 서버와 WAS가 무엇인지 알아보기 전에 먼저 알아야하는 개념이 있다.

그것은 바로 **Static Pages와 Dynamic Pages**이다.

아래에서 두가지의 개념에 대해 알아보자!

#### Static Pages (정적 페이지) ####

Static Pages는 말그대로 정적 페이지를 말하는 것이다. 유저들에게 보여줄 이미 만들어진 파일이라고 생각하면 된다.

정적 페이지의 파일은 image, HTML, CSS, javascript 등과 같이 개발자에 의해 이미 만들어져서 정적으로만 동작하는 것들이다.

웹 서버는 해당 정적 파일이 위치하는 route를 요청으로 받아 일치하는 파일을 응답으로 보내준다.

그렇기 때문에 항상 동일한 페이지를 보여주게 된다.

지금 내가 작성하고 있는 이 깃허브 블로그가 정적 페이지로 이루어진 웹 사이트다.

왜냐하면 마크다운 파일로 이미 작성해놓은 포스트를 단순히 요청에 의해 보여주기만 할 뿐이기 때문이다.

#### Dynamic Pages (동적 페이지) ####

Dynamic Pages는 위의 정적 페이지와는 정반대의 개념으로 동적인 컨텐츠를 말한다.

정적 페이지와 다르게 동적 페이지는 주어진 인자의 내용에 맞게 그에 알맞는 동적 컨텐츠를 유저에게 제공한다.

쉽게 말해 웹 서버에서 실행되는 프로그램을 통해서 (정확히는 WAS에서) 만들어진 동적 결과물을 반환한다는 말이다.

### Web Server와 WAS ###

위에서 정적 동적 페이지에 대해 알아보았다. 여기에서는 이제 웹 서버와 웹 어플리케이션 서버에 대해 알아보자!

#### Web Server (웹 서버) ####

웹 서버는 크게 하드웨어와 소프트웨어로 구분 할 수 있다.

- 하드웨어: 웹 서버가 실제로 설치되어 있는 컴퓨터
- 소프트웨어: 클라이언트로부터 요청을 받아 정적인 컨텐츠를 응답으로 제공해줄 수 있는 프로그램

웹 서버가 하는 일은 크게 나눠서 두가지 정도가 될 수 있다.

1. 웹 서버 자체는 정적 컨텐츠를 제공해주는 역할을 하기 때문에 정적 컨텐츠에 대한 요청이 들어왔다면 WAS를 거치지 않고 웹 서버가 직접 정적 컨텐츠를 제공해준다.
2. 클라이언트로부터 요청을 받았는데 그 요청이 동적 컨텐츠를 제공해달라는 요청이라면 해당 요청을 WAS에 보내주는 역할을 해주고 WAS가 처리한 결과를 다시 전달 받아 해당 결과를 클라이언트에게 응답으로 보내준다.

위의 내용을 정리해보면 웹 서버는 정적 컨텐츠에 대해 직접적인 응답을 해주고 동적 컨텐츠에 대한 요청이 들어왔을때는 직접 처리하지않고 WAS에게 해당 요청을 전달하고 처리된 결과를 다시 전달받아 클라이언트에게 전해주는 역할을 한다고 보면 되겠다.

웹 서버로는 Apache Server, Nginx 등이 있다.

#### WAS (웹 어플리케이션 서버) ####

웹 어플리케이션 서버는 동적 컨텐츠를 처리하는 서버이다. 어플리케이션 서버 혹은 앱 서버라고도 부른다.

WAS는 DB 조회나 다양한 로직 처리를 요구하는 동적인 컨텐츠를 제공하기 위해 만들어진 어플리케이션 서버이다.

HTTP 통신을 통해 컴퓨터나 장치에 어플리케이션을 수행해주는 **미들웨어**이다.

보통 DB서버와 통신하며 요청 받은 인자의 내용에 따라 동적으로 컨텐츠를 제공해주기 때문에 비즈니스 로직도 적용이 가능하다.

이렇게 동적으로 데이터를 처리한후 해당 내용을 클라이언트에게 보여주기 때문에 Server Side Rendering (SSR)을 하여 응답을 보내준다.

### 웹 서버와 WAS를 나눠놓은 이유 ###

사실 웹 서버나 WAS나 둘다 정적 페이지와 동적 페이지에 대한 응답을 하게끔 만들 수 있다.

하지만 다음과 같은 이유로 이 둘을 나누어 놓았다.

1. 기능을 분리함으로서 서버의 과부하를 방지한다.

2. 서버자체를 물리적으로 분리하였기 때문에 보안도 더 강화 할 수 있다.

3. 여러 대의 WAS를 연결함으로서 fail over (장애 극복)에 유리 하다.

정리를 해보면 **자원 이용의 효율성 및 장애 극복, 배포 및 유지보수의 편의성**을 위해 두개를 분리한다.

### 마치며 ###

사실 웹 서버와 WAS에 대해 찾아 보았지만 다 비슷한 말로 적혀있고 웹 서버는 알겠는데 WAS가 정확히 어떤식으로 동작하는지를

몰라서 답답했었다. 부트캠프에서 리뷰 세션에서 질문을 해봤고 간략하게 개념에 대해 알 수 있어서 내가 이해한대로 정리를 해보았다.

충분히 틀린 부분이 있을 수도 있다는것을 염두에 두면 좋겠다. 나중에 틀린 부분을 찾게된다면 수정 태그를 달아서 수정을 해야겠다.

그리고 참고를 많이 한 블로그가 하나 있어서 Reference로 아래에 남겨 놓겠다.

[Reference](https://gmlwjd9405.github.io/2018/10/27/webserver-vs-was.html)
