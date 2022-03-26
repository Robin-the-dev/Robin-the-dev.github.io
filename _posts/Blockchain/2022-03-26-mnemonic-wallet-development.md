---
layout: post
title: Mnemonic wallet development
subtitle: Mnemonic wallet development using eth-lightwallet module
categories: Blockchain
tags: [Blockchain, Wallet, Mnemonic, HD Wallet]
---

How to develop a wallet?
------------

13주차 부트캠프 회고에 말했다시피 드디어 Section 4를 들어와서 블록체인 이론이 아닌 직접적인 개발을 해보고 있다.

그 첫번째 프로젝트로 Mnemonic wallet 개발을 해보았다. 정확하게는 Mnemonic phrase를 사용하는 HD wallet 개발이 되겠다.

사용한 package는 eth-lightwallet이고 [공식문서](https://github.com/ConsenSys/eth-lightwallet#readme)는 이곳을 참조하자!

공식문서에는 A minimal ethereum javascript wallet이라고 되어있으며 보안에 대해서도 언급했는데

In official document, it said it has not been gone through security review which means it is still experimental software.

Also, it said this wallet is intended to store small amount of crypto currency.

라고 얘기 하고 있으니 감안하고 사용하면 되겠다.

코드를 보기전에 Mnemonic wallet development는 Node.js 환경에서 개발 되었고 code에서 명시된 end point로

get 혹은 post 요청을 보내(여기서는 post요청만 사용) Mnemonic phrase를 받거나 wallet을 만들거나 할 수 있게 되어있다.

여기서는 Node.js의 app.js 파일에 대해서는 설명하지 않고 실제로 eth-lightwallet 모듈을 사용한 코드 파일에 대해서만 정리를 해보려고 한다.

아래에서 코드를 보며 정리해보겠다.

### Code ###

#### Importing necessary package ####

일단 import 해오는 package부터 보자!

```nodejs
var express = require('express');
var router = express.Router();
const lightwallet = require("eth-lightwallet");
const fs = require('fs');
```

Node.js로 user가 wallet을 발급받을 수 있는 플랫폼을 만들었기 때문에 express를 import 해오고

router를 통해서 end-point를 나눠줄것이기 때문에 `express.Router()`를 통해 router를 가져온다.

그리고 이번 개발의 key package인 eth-lightwallet을 import 하고 fs module도 import 해온다.

fs module이 필요한 이유는 key store를 파일로 가져오려하기 때문에 필요하다.

fs module을 사용하지 않으려면 key store의 내용들을 response와 함께 보내주면 된다.

#### To get new mnemonic phrase ####

```nodejs
router.post('/newMnemonic', async(req,res) => {
  let mnemonic;

  try {
	mnemonic = lightwallet.keystore.generateRandomSeed();
	res.json({mnemonic});
  } catch(err) {
	console.log(err);
	res.json({message: err});
  }
});
```

유저가 Mnemonic phrase를 받을수있게 해주는 코드이다.

/newMnemonic 쪽으로 post 요청을 보내면 응답으로 json 형태로 Mnemonic phrase를 받을 수 있다.

여기서는 Mnemonic phrase를 받기 위해 eht-lightwallet module의 generateRandomSeed() 함수를 사용하였다.

generateRandomSeed() 함수는 이름대로 seed 값을 리턴해주는 함수인데 그 seed가 Mnemonic phrase로 되어있다.

여기서 알 수 있는 것은 seed를 사용한다는 것은 이것이 HD wallet이라는 것을 알 수 있고 

**seed == Mnemonic** 이라는 것도 알 수 있다.

일반적인 seed 값은 사람이 보기 불편한 값이기에 직접 적거나 할때 충분히 오타가 날 수 있기에 사람이 보기 편한

Mnemonic phrase로 변환을 해서 그 값을 유저들이 안전하게 어딘가에 적어서 보관하면 훨씬 관리하기 용이하다.

그래서 Mnemonic phrase를 사용한다고 생각하면 되겠다.

#### To create new wallet ####

```nodejs
router.post('/newWallet', async(req, res) => {
  const password = req.body.password;
  const mnemonic = req.body.mnemonic;

  try {
	lightwallet.keystore.createVault({
	  password: password, 
	  seedPhrase: mnemonic,
	  hdPathString: "m/0'/0'/0'"
	}, (err, ks) => {
	  ks.keyFromPassword(password, (err, pwDerivedKey) => {
		ks.generateNewAddress(pwDerivedKey);

		const addresses = ks.getAddresses();
		const keystore = ks.serialize();

		fs.writeFile('wallet.json', keystore, (err) => {
		  if(err) {
			res.json({message: 'Failed to create new wallet'});
		  } else {
			res.json({address: addresses[0], message: 'Successfully created new wallet'});
		  }
		});
	  });
	});
  } catch(err) {
	console.log(err);
	res.json({message: err});
  }
});
```

이 코드는 제공받은 Mnemonic phrase를 이용해서 새로운 지갑을 개설하는 코드이다.

/newWallet이라는 end-point로 post 요청을 보내면 local에 key store 파일을 저장하고 응답으로 address와 성공 message를 받을 수 있다.

일단 key store라는 새로운 단어를 보게되었으니 이것 부터 정리를 해보면 key store 라는 것은 **암호 화폐지갑을 사용하기 위한 private key를 암호화한 텍스트 혹은 파일** 이다.

[Reference for key store](http://wiki.hash.kr/index.php/%ED%82%A4%EC%8A%A4%ED%86%A0%EC%96%B4#:~:text=%ED%82%A4%EC%8A%A4%ED%86%A0%EC%96%B4(Key%20Store)%EB%9E%80,%ED%95%9C%20%ED%85%8D%EC%8A%A4%ED%8A%B8%20%EB%98%90%EB%8A%94%20%ED%8C%8C%EC%9D%BC%EC%9D%B4%EB%8B%A4.)

그리고 지갑의 원리를 알면 public key가 address인것을 알 수 있다.

다시 코드를 돌어와서 일단 요청은 body에 private key를 암호화 할 (key store로 만들) password와 제공받은 mnemonic을 함께 보내야한다.

그러면 그것을 받아와 각각 password와 mnemonic 변수를 만들어 할당해놓는다.

그러면 제일 먼저 해야할 일은 createVault() 함수를 이용해 key store를 만들어야 한다.

해당 함수는 첫번째 argument로 객체로 되어있는 option값을 주어야 한다. 공식문서를 가보면 어떤 값을 mandatory로 주어야 하는지 적혀있으니

확인해보고 option 값을 주면된다. option에 대해서는 한가지만 얘기 해보겠다.

option 중에서 hdPathString이라는 값을 주어야하는데 BIP-39의 default path는 m/0'/0'/0' 이기에 이 값을 그대로 주었다.

이 말은 BIP-39 (Mnemonic 관련 proposal)의 내용을 따라 HD wallet을 개발하는것이라고 보면 되겠다.

공식문서를 보면 BIP-44 (HD wallet 관련 proposal)는 default path는 m/44'/60'/0'/0 이고 이것 또한 사용이 가능하다고 한다.

그리고 이어서 두번째 argument는 callback function을 받는데 callback의 첫번째 argument는 error를 두번째 argument로는 createVault()가 만든 key store를 받을 수 있다.

그래서 callback function 내에서 전달받은 key store를 가지고 계속해서 작업을 이어나가면 된다. (key store를 ks라는 변수 이름으로 받았으니 앞으로 ks라고 하겠다.)

그 다음으로는 keyFromPassword() 함수를 이용해서 key 값을 가지고 와야한다. 이렇게 가져온 key 값을 이용해서

새로운 주소를 받을 수 있다.

keyFromPassword() 함수는 위의 ks를 가지고 호출 할 수 있기 때문에 ks.keyFromPassword()로 사용하면 된다.

해당 함수의 첫번째 argument는 password를 받는데 우리가 위에서 만든 ks를 암호화한 password이다.

그리고 두번째 argument로는 또 다시 callback function을 받는데 첫번째 argument는 error 그리고 두번째 argument는 keyFromPassword()가 만든 key 값이다.

그러면 또 callback function안에서 작업을 계속 해주면 된다. (callback hell 처럼 되는것이 맞다.)

generateNewAddress() 함수를 이용해서 새로운 주소를 받을 수 있다.

첫번째 argument로는 위에서 받은 key 값과 두번째 argument로 만들 갯수를 정수형으로 주면 된다.

ks.generateNewAddress()로 함수를 실행하기때문에 해당 주소는 우리가 위에서 만든 ks안에 들어있게 된다.

이제 wallet을 만들기 위한 모든 작업이 끝났다.

ks.getAddresses()를 이용해서 주소값을 받아 올 수 있다. 배열 형태로 만든 모든 주소값을 리턴해준다.

그리고 당연히 key store 파일을 json 형식으로 만들어서 fs module을 이용해 local에 저장할수있게끔 해줄것이기 때문에

ks.serialize() 함수를 이용해서 json 형식으로 key store를 직렬화해준다.

그 다음 부터는 주소값과 json 형식의 key store를 유저에게 적절한 방식으로 전해주기만 하면된다.

address는 응답으로 보내주고 직렬화된 key store는 fs.writeFile() 함수를 이용해서 local에 저장해주게끔 하면 된다.

그리고 아래는 전체 코드이다.

#### Entire code ####

```nodejs
var express = require('express');
var router = express.Router();
const lightwallet = require("eth-lightwallet");
const fs = require('fs');

router.post('/newMnemonic', async(req,res) => {
  let mnemonic;

  try {
	mnemonic = lightwallet.keystore.generateRandomSeed();
	res.json({mnemonic});
  } catch(err) {
	console.log(err);
	res.json({message: err});
  }
});


router.post('/newWallet', async(req, res) => {
  const password = req.body.password;
  const mnemonic = req.body.mnemonic;

  try {
	lightwallet.keystore.createVault({
	  password: password, 
	  seedPhrase: mnemonic,
	  hdPathString: "m/0'/0'/0'"
	}, (err, ks) => {
	  ks.keyFromPassword(password, (err, pwDerivedKey) => {
		ks.generateNewAddress(pwDerivedKey);

		const addresses = ks.getAddresses();
		const keystore = ks.serialize();

		fs.writeFile('wallet.json', keystore, (err) => {
		  if(err) {
			res.json({message: 'Failed to create new wallet'});
		  } else {
			res.json({address: addresses[0], message: 'Successfully created new wallet'});
		  }
		});
	  });
	});
  } catch(err) {
	console.log(err);
	res.json({message: err});
  }
});

module.exports = router;
```

### 마치며 ###

이렇게 eth-lightwallet module을 이용해서 HD wallet 이자 Mnemonic wallet인 지갑을 직접 개발 해보았다.

지갑 개발을 from the scratch에서 하는 것이 아닌 누군가 만들어 놓은 package를 이용해서 개발을 했기 때문에

어떤 함수가 어떤 동작을 하고 지갑 개발을 위해서는 정확히 어떤 함수를 사용하는지만 알면 크게 어렵지 않게 개발 할 수 있다.

여기서 중요한것은 역시 내가 항상 언급하는 공식문서를 잘 보는 것이고 사용한 함수들을 공식문서에서 확인하고

스스로 코딩을 해보니 크게 어렵지 않게 할 수 있었다.

앞으로도 항상 공식문서를 참고하면서 개발 능력을 키울수있게 하자!!!
