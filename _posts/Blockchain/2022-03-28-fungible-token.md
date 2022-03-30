---
layout: post
title: Fungible Token (ERC-20)
subtitle: Fungible Token(FT) Development with ERC-20
categories: Blockchain
tags: [Blockchain, FT, ERC-20]
---

What is Fungible Token(FT)?
------------

Fungible Token이란 무엇일까? NFT라는 것은 일반적으로 많이 들어봤을테니 이것의 반대되는 개념이라고는 유추할 수 있을것이다.

일단 Fungible의 사전적인 의미부터 먼저 보자면 **대체 가능한**이라는 뜻을 가지고 있다.

그래서 FT는 **대체 가능한 토큰**일 것이고 NFT는 **대체 불가능한 토큰**일 것이다.

NFT는 다음 포스트에서 얘기를 해보고 일단 FT 얘기부터 하자면

**대체 가능하다**라는 것은 무엇을 의미 하는 걸까?

일반적인 화폐라고 생각하면 편하겠다. 예를 들어서 원화 1만원은 내가 가지고 있는 1만원이나 A라는 사람이 가지고 있는 1만원이나

가치가 동일하다. 그렇기 때문에 서로 가지고 있는 1만원권을 서로 교환하더라도 전혀 문제가 없다.

FT는 이 부분의 개념과 동일하다. 내가 이더리움 기반으로 토큰을 만들어서 A라는 사람이 100토큰 B라는 사람이 100토큰을 가지고 있다면

이 두사람이 가지고 있는 토큰의 양이 동일하기에 가치가 동일하다.

그래서 정리를 하자면 FT를 어렵게 생각하지말고 그냥 일반적인 돈의 개념과 비슷하다고 생각하면 될 것 같다.

이번 포스트가 FT와 관련된 내용이니까 코인과 토큰에 대해서도 얘기를 해보겠다.

코인과 토큰의 차이는 mainnet의 유무라고 할수있겠다.

이더리움과 같은 경우 이더리움이라는 플랫폼에서 사용하는 토큰은 ether라고 하고 이더리움은 직접 mainnet을 운용하고 있기 때문에

ether는 코인이라고 할 수 있고 Governance Token이라고 할 수 있다.

반면에 우리가 ERC-20 표준을 사용해서 이더리움 플랫폼 위에서 FT를 발행한다면 이더리움 mainnet에 의존하고 있기 때문에

코인이 아니라 토큰이라고 부른다.

이더리움과 같이 mainnet을 보유한 코인이 돌아가는 생태계를 **Crypto Economy**라고 하고 

직접 mainnet을 운용하지 않고 다른 mainnet에 의존적인 FT가 돌아가는 생태계를 **Token Economy**라고 한다.

FT에 관한 포스팅이여서 내가 아는 FT에 대한 정보들을 코인과 비교해서 한번 정리해보았다.

아래에서는 코드를 살펴보며 ERC-20과 KIP-7를 이용해 발행한 FT에 대해 정리를 해보겠다.

### ERC-20 ###

ERC-20이란 EIP-20에서 제안된 이더리움에서 FT를 만들기위한 표준이다.

그래서 이더리움 위에서 돌아가게 하는 FT를 발행하고 싶으면 ERC-20 표준을 지켜서 만들면 된다.

#### Entire Code ####

[전체코드](https://github.com/Robin-the-dev/Practice-ERC20)는 여기서 볼 수 있다.

각 함수들이 어떻게 동작하는지 보기 위해 openZeppelin을 사용하지 않고 구현했다.

밑에서 코드를 나눠서 해당 코드가 무엇을 하는지 살펴보자!

#### SafeMath ####

먼저 contract 부분을 보기 전에 SafeMath가 무엇인지 얘기를 해보겠다.

SafeMath는 contract 내에서 연산을 했을때 생길 수 있는 **overflow**와 **underflow**를 예방해주는 library이다.

overflow 하나만 알면 자연스럽게 underflow도 알 수 있어서 overflow만 설명해보겠다.

Solidity에서는 uint 옆에 8의 배수의 숫자를 명시해서 표현할 수 있는 범위를 설정할 수 있는데

예를 들어 uint8이라고 하면 2^8-1 만큼 표현할 수 있다. 여기서 overflow가 발생한다는 것은

2^8-1 보다 큰 수가 들어가서 에러가 발생함을 말한다. (underflow는 반대)

```solidity
library SafeMath {
  	function mul(uint256 a, uint256 b) internal pure returns (uint256) {
		uint256 c = a * b;
		assert(a == 0 || c / a == b);
		return c;
  	}

  	function div(uint256 a, uint256 b) internal pure returns (uint256) {
	    uint256 c = a / b;
		return c;
  	}

  	function sub(uint256 a, uint256 b) internal pure returns (uint256) {
		assert(b <= a);
		return a - b;
  	}

  	function add(uint256 a, uint256 b) internal pure returns (uint256) {
		uint256 c = a + b;
		assert(c >= a);
		return c;
	}
}

contract Foo {
  using SafeMath for uint256;

  // Contract contents

  function bar(uint256 amount) public returns(uint256) {
	// Let's assume that _amount is data type of uint256
	return _amount.sub(amount);
  }
}
```

위의 코드와 같이 `library`를 이용해서 SafeMath의 내용을 정의 할 수 있고 contract 내에서

`using SafeMath for uint256;`을 해서 데이터 타입 uint256으로 정의된 변수가 SafeMath 함수를 사용할수있게

명시해준다.

직접적으로 사용하는 방법은 `bar()`함수 안에 있으니 확인해보자!

#### ERC20 Interface ####

Solidity에도 JAVA와 같이 interface와 abstract라는 것이 있는데 JAVA의 그것과 동일하다고 생각하면 되겠다.

```solidity
interface ERC20Interface {
    function totalSupply() external view returns (uint256);
    function balanceOf(address account) external view returns (uint256);
    function transfer(address recipient, uint256 amount) external returns (bool);
    function approve(address spender, uint256 amount) external returns (bool);
    function allowance(address owner, address spender) external view returns (uint256);
    function transferFrom(address spender, address recipient, uint256 amount) external returns (bool);
    
    event Transfer(address indexed from, address indexed to, uint256 amount);
    event Transfer(address indexed spender, address indexed from, address indexed to, uint256 amount);
    event Approval(address indexed owner, address indexed spender, uint256 amount);
}
```

그래서 위의 코드는 ERC20의 표준을 따르는 interface를 저렇게 정의를 해놓은 것이고 실제로 구현할 contract가 해당 interface를

상속하면서 interface에 정의된 function들을 override 해주면 된다.

이 [링크](https://medium.com/upstate-interactive/solidity-override-vs-virtual-functions-c0a5dfb83aaf)에서 solidity에서 interface와 override 그리고 virtual이 어떻게 쓰이는지 확인해보자!

기본적으로 다른 프로그래밍 언어와 동작하는 방식이 동일하고 위의 코드에서 interface안에서 정의해준 function들은 virtual이라는 키워드가

명시되어있지 않는데 명시되지않았을 경우 interface에서 정의 function은 virtual로 간주된다. (링크 확인)

#### abstract contract ####

전체 코드를 보면 abstract contract도 사용 했는데 OwnerHelper라는 abstract contract를 만들어서 내가 구현한 contract가 그것을 상속하는 식으로 쓰였다.

interface 부분에서 interface와 abstract 둘 다 JAVA의 그것과 동일하다고 생각하면 된다고 했는데 interface와 abstract 그 자체에 대한 설명이 부족한 것 같아 여기서 설명 해보겠다.

interface는 function에 대한 정의만 할 수 있다. interface 안에서 function의 구현을 하면 안된다.

반면에 abstract는 interface와 동일하게 function에 대한 정의만 해놓고 다른 contract가 abstract contract를 상속해서 해당 function 을 override 할 수 있지만 완전하게 구현된 function도 abstract contract 안에 넣어 놓을 수 있다.

코드를 보면 이해가 더 쉬울것이다.

```solidity
abstract contract OwnerHelper {
  	address private _owner;

  	event OwnershipTransferred(address indexed preOwner, address indexed nextOwner);

  	modifier onlyOwner {
		require(msg.sender == _owner, "OwnerHelper: caller is not owner");
		_;
  	}

  	constructor() {
        _owner = msg.sender;
  	}

    function owner() public view virtual returns (address) {
		return _owner;
    }

  	function transferOwnership(address newOwner) onlyOwner public {
        require(newOwner != _owner);
        require(newOwner != address(0x0));
        address preOwner = _owner;
    	_owner = newOwner;
    	emit OwnershipTransferred(preOwner, newOwner);
  	}
}
```

코드에서의 abstract contract는 오히려 반대로 정의만 해놓은 function이 없다!

그리고 abstract contract라고 꼭 코드에서 처럼 abstract라고 명시 하지 않아도 된다.

왜냐하면 contract는 적어도 하나의 function이 구현되어 있지않다면 abstract를 적던 적지 않던 그것은 abstract contract로 간주되기 때문이다.

이 [링크](https://medium.com/upstate-interactive/solidity-how-to-know-when-to-use-abstract-contracts-vs-interfaces-874cab860c56#:~:text=Abstract%20contracts%20are%20base%20contracts,cannot%20include%20any%20implemented%20functions.)에서 자세한 내용을 살펴보자!

#### main contract ####

main contract라고 해놨는데 별다른 뜻이 있는게 아니라 이 smart contract에서의 주 목적이 담긴 contract를 말하는 것이다.

이 코드에서는 **MyLittlePreciousToken** contract가 되겠다.

사실 코드자체가 어려운 내용은 전혀 없고 단순히 ERC-20의 표준에 맞게 코드를 작성해주면 되는것 같다. (예를 들어 소수점은 18자리 등 등)

그리고 솔직한 생각으로 ERC-20을 이용해서 모든 코드를 직접 구현하는 것은 별로 현명하지 않다라는 생각이 든다.

왜냐하면 smart contract라는 것은 본질적으로 돈의 이동과 관련되어 있는데 내가 직접 구현해서 조그만 취약점이나 실수가 발견되면 블록체인 특성상

수정이 쉽지 않고 나의 서비스를 사용하는 사용자들이 큰 손해를 볼 수 있기 때문이다.

그래서 이미 잘 쓰여진 ERC-20 표준을 따르는 코드를 들고와서 내가 운영하려는 서비스나 목적에 맞게 필요한 function들만 따로 작성을 해주는게

현명하지 않을까라는 생각이 든다.

나의 이런 생각이 틀린 것은 아닌지 openzeppelin이라는 이더리움 전용 라이브러리가 존재한다.

해당 라이브러리를 사용하면 오늘 작성해본 포스팅과 관련된 ERC-20 그리고 나중에 또 작성할 ERC-721 등을 정말 간단하게 구현을 할 수 있다.

main contract의 내용은 길어서 위의 entire code에서 확인을 해보자!

#### OpenZeppelin ####

위에서 OpenZeppelin을 언급했으니까 이 부분도 얘기를 안 할 수가 없겠다.

일단 OpenZeppelin은 말한대로 이더리움 표준을 사용하기위해 적어야 하는 코드가 많은데 그 부분을 라이브러리로 만들어놓고 단순히

import해와서 쓸수있도록 해주는 라이브러리이다.

여기서는 truffle을 활용해 OpenZeppelin을 사용할 수 있는 방법에 대해서 얘기 해보겠다.

그전에 remix에서의 사용법을 먼저 간단하게 얘기하고 넘어가겠다. remix에서는 OpenZeppelin 사용법은 훨씬 더 간단하다.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity 0.8.10;

// 이렇게 openzeppelin을 import한다고 명시해주면 자동으로 remix가 해당 라이브러리를 다운 받아준다.
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract MyLittlePreciousToken is ERC20 {
    constructor() ERC20("MyLittlePreciousToken", "MLPT") {
          _mint(msg.sender, 100000000e18);
    }

}
```

이런식으로 import만 해주면 자동으로 remix가 해당 라이브러리를 받아오기때문에 훨씬 간단하다.

그럼 바로 truffle을 활용해서 OpenZeppelin을 이용하는 법을 보자!

먼저 작업할 폴더를 하나 만들어주고 해당 폴더에 들어가서 `truffle init`과 `npm init`을 해서 초기화를 시켜준다.

그 이후에 Solidity 사용을 위해 openzeppelin solidity 라이브러리를 받아준다. `npm install -E openzeppelin-solidity`

`-E`옵션은 `--save-exact`와 동일하고 인스톨받은 패키지의 정확한 버전을 package.json에 저장할 수 있게 해준다.

그리고 `truffle-config.js`파일의 compiler 부분의 버전을 openzeppelin이 사용하는 컴파일러 버전과 동일하게 바꿔준다.

ganache를 통해 local RPC에 배포해서 해당 contract를 확인을 할 것이라면 `truffle-confg.js`파일의 network 부분을 바꿔주면 된다.

contracts 폴더안에 위의 코드를 들고와서 저장을 해주고 코드에서 import 한 경로를 추가해주기 위해 `npm install @openzeppelin/contracts`을 해준다.

그러면 마지막으로 `truffle deploy` 혹은 `truffle migrate` 명령어를 입력해 배포를 해주면 된다!

똑같은 ERC-20 코드인데 OpenZeppelin을 사용하니 엄청 짧아졌다. 이더리움 기반으로 dApp을 개발한다고 하면 OpenZeppelin을 사용하면 되겠다!

### 마치며 ###

Fungible Token 포스팅에 ERC-20과 KIP-7 둘 다 넣을려고 했는데 글을 쓰다보니 생각보다 ERC-20의 내용이 너무 많아서 KIP-7의 내용은 

따로 또 포스팅을 작성하려고 한다.

KIP-7 자체는 ERC-20에서 거의 따왔다고 해도될만큼 크게 다른 내용은 없어서 해당 포스트는 내용이 좀 짧아질 수 도 있지만

어쨌든 몇가지 다른 부분이 있기는 하기 때문에 그 부분에 대해서는 해당 포스트에서 얘기를 해보겠다.

아무튼 이렇게 ERC-20을 testnet 환경 혹은 로컬 환경에서 배포를 해보았는데 내 지갑에 내가 만든 토큰이 찍혀있는것을 보니

재밌기도 하고 뭔가 감개무량했다. ㅋㅋㅋ 블록체인과 관련해서 배우기 시작하면서 학습하는 내용의 양이 방대해서 블로그에 

포스팅하는 것도 사실 쉽지는 않지만 최대한 배운 것들을 간략하게나마 쓸수 있도록 노력 해봐야겠다!!!
