---
layout: post
title: Solidity fundamentals
subtitle: Solidity syntax reference
categories: Blockchain
tags: [Blockchain, Solidity]
---

What is Solidity?
------------

Solidity는 프로그래밍 언어의 한 종류로 Ethereum Virtual Machine (EVM)에서 실행되는 smart contract 개발을 위해 설계된 정적 타입 (static-typed)의 중괄호 (curly-braces) 프로그래밍 언어이다.

BitCoin의 OP code와 다르게 turing-complete한 언어 이다. 그래서 반복문 사용이 가능하다.

(EVM이 Solidity 코드를 읽어들여 OP code로 변환을 해주기에 동일선상에서 비교를 해보았다.)

반복문 사용이 가능하게 만들 수 있었던것은 Ethereum이 gas라는 개념의 수수료를 사용하기 있기 때문이다.

Solidity로 작성된 코드를 Ethereum chain 위에서 돌아가게 하려면 연산을 할때마다 그에 걸맞는 gas가 필요하고 gas가 부족하면

모든 state variable을 원래 상태로 만든다.

Solidity는 이러한 컨셉으로 돌아간다고 알면 되겠다.

아래에서 Solidity의 기본 문법에 대해 하나하나 설명해보겠다.

### Basic syntax ###

#### SPDX License Identifier ####

SPDX License Identifier는 저작권 혹은 라이센스 문제를 해소하기 위해 맨 첫줄에 주석으로 라이센스를 명시해주어야하는 것이다.

```solidity
// SPDX-License-Identifier: MIT
```

[SPDX-License list](https://spdx.org/licenses/)

#### Pragma ####

Pragma는 Solidity compiler 버전을 명시해주는 것이다.

```solidity
pragma solidity 0.8.7; // 0.8.7 버전 사용

// 혹은

pragma solidity ^0.8.7;	// 0.8.7 이상의 버전 사용
```

버전 규칙은 npm과 동일 하기에 [npm 버전 규칙](https://docs.npmjs.com/cli/v6/using-npm/semver)을 참고하자!

#### import ####

JS에서 사용한 방식과 비슷하기에 별다른 설명은 하지 않겠다.

```solidity
import "파일이름";

// as를 사용해서 alias 가능
import * as symbolName from "파일이름";  
import "파일이름" as symbolName;

// 중괄호를 사용해서 일부만 import 가능
import {symbol1 as alias, symbol2} from "파일이름"; 
```

### Contract architecture ###

Contract는 State Variable, Function, Function Modifier, Error, Inheritance, Enum Types 등으로 구성된다.

아래에서 Contract의 구성 요소를 하나씩 살펴보겠다.

#### State Variable ####

State Variable은 변수의 값이 Contract의 Storage에 영구적으로 저장되는 변수를 의미한다.

아래의 코드와 같이 선언 혹은 선언 및 초기화를 할 수 있고 JS와 다르게 데이터 타입을 명시해주어야 한다.

아래의 uint 자료형은 uint256과 동일하고 2^256-1 만큼의 양수를 표현 할 수 있다. (uint의 u는 unsigned)

```solidity
pragma solidity 0.8.7;

// contract 키워드를 사용해서 SimpleStorage라는 contract 생성
contract SimpleStorage {
    uint storedData; // State Variable 선언
    uint storedData = 20; // State Variable 선언 및 초기화
}
```

State Variable은 Value type (값형)과 Reference type (참조형)을 가진다.

##### Value type #####

- Bool

boolean 값을 가진다.

```solidity
bool isGood = true;
bool isBad = false;
```

- int, uint

위쪽에서 간단하게 설명했기에 예제만 쓰겠다.

```solidity
int8 signedInteger = -5;
uint16 unsignedInteger = 19;
```

참고로 int와 uint 뒤에 붙는 숫자는 8의 배수로 올 수 있다.

- fixed-size byte array (고정 바이트 배열)

bytes1에서 bytes32까지의 고정된 크기의 배열을 선언한다.

```solidity
bytes3 alphabets = 'abc';

alphabets[0]; // 'a'
alphabets[1]; // 'b'
alphabets[2]; // 'c'
```

- address

주소 객체는 0x로 시작하고 최대 40자리의 16진수로 구성된 크기가 20byte인 문자열을 가진다. 

주로 계정의 잔액을 반환하는 balance() 함수와 ether를 account로 전송하는 transfer() 함수에서 사용한다.

```solidity
address myAddress = 0x10abb5efEcdc01234f7b2384912398798E089Ab2;
```

0.8 버전부터는 address 형식은 transfer가 불가능한 주소값이기때문에 **address payable** 형식을 사용해야 한다.

address payable 형식에는 ether의 transfer를 위한 transfer(), send() 함수가 내장되어 있다.

address payable 형식 데이터는 address 형식 데이터를 **payable()** 함수에 인자로 담아 만들 수 있다.

```solidity
address myAddress = 0x10abb5efEcdc01234f7b2384912398798E089Ab2;
address payable myPayableAddress = payable(myAddress);
```

smart contract를 작성하기 위해서 구글링을 하거나 리서치를 하다보면 `address(0) 혹은 address(0x0)`을 많이 볼 수 있을텐데

보통 어떤 함수에 전해진 주소 값이 존재하는지 하지않는지 확인할때 위의 코드와 비교를 하면서 확인한다.

Solidity 에서 `address(0)`이 의미하는 것이 새로운 contract가 배포되었을때를 말한다고 하는데 사실 아직 이것이

의미하는 것이 정확히 무엇인지는 모르겠고 [링크](https://stackoverflow.com/questions/48219716/what-is-address0-in-solidity)에서 확인 해보자!

일단 `address(0)`는 전해받은 address가 존재하는지 하지않는지 확인을 위해서 사용한다고 생각하자!

왜냐하면 인자로 address 형식을 받는 함수에 address를 보내주지 않으면 `address(0)`의 값과 동일하기 때문이다.

##### Reference type #####

참조형 데이터 타입은 이름만 들어도 알수있다시피 배열과 같이 첫번째 메모리의 주소를 값으로 가지는 데이터 타입이다.

Solidity에서는 데이터 영역에 두가지 종류가 있는데

memory: 프로그램이 작동할때만 값을 가지고 있다가 종료되면 값을 잃는 데이터 영역

storage: 블록체인에 영구적으로 기록되어 값이 유지되는 데이터 영역

이다.

```solidity
function foo() {
  uint8[3] memory bar;
  bar[0] = 3;
}
```

위의 코드처럼 bar는 길이가 3이고 요소의 데이터 타입이 uint8인 배열이다. 데이터 영역은 memory로 위와 같이 작성을 하여 명시해준다.

이러한 feature를 가진 것이 reference type 이고 아래에서 각각의 참조형 타입을 알아보자!!!

- Array (배열)

데이터 형식에 []를 붙여 선언한다. 배열에는 정적배열과 동적배열이 존재하는데 `uint8[4]` 와 같이 하는 것이 정적배열이고 `uint8[]` 와 같이 하는것이 동적배열이다.

길이를 지정하느냐 하지않느냐의 차이이다.

- String (문자열)

문자열은 bytes와 동일하지만, 길이와 push() 함수가 없다.

```solidity
string foo = "bar";
```

- Struct (구조체)

C 언어의 구조체와 쓰임새가 비슷하다. 서로 다른 유형의 항목을 포함하는 집합으로 **사용자 정의 형식**이다.

```solidity
contract foo {
  struct Bar {
	address foofoo;
	string barbar1;
	string barbar2;
  }

  function f(address newFooFoo, string newBarBar1, string newBarBar2) {
	Bar memory newBar = Bar({
	  foofoo: newFooFoo, barbar1: newBarBar1, barbar2: newBarBar2
	});
  }
}
```

위의 코드와 같이 구조체를 정의하고 자료형으로서 사용하면 된다.

- Mapping (매핑)

매핑은 Storage 데이터 영역에서 key-value 쌍으로 데이터를 저장할때 사용하는 Reference type 자료형이다.

```solidity
mapping(address => int) public userAddress;
```

위와 같이 선언하고 JS의 객체와 비슷하다고 생각하면 되겠다.

#### Global variable ####

글로벌 변수라고 해서 헷갈릴수있는데 Solidity가 가지고 있는 변수라고 생각하면 되겠다.

종류로는

- block: 블록에 대한 정보를 가지고 있다.
  - blockhash(uint blocknumber): 주어진 블록의 hash를 bytes32 형태로 반환한다.
  - coinbase: 블록의 채굴자 주소를 address 형식으로 받는다.
  - gaslimit: 블록의 가스 한도를 uint 형식으로 받는다.
  - number: 블록의 번호를 uint 형식으로 받는다.
  - timestamp: 블록의 타임스탬프를 uint 형식으로 받는다.

- msg: 컨트랙트를 시작한 트랜잭션 콜이나 메시지 콜에 대한 정보를 가지고 있다.
  - gasleft(): 남아있는 가스의 양을 uint 형태로 반환한다.
  - data: 전체 콜데이터 본문을 bytes 형식으로 받는다.
  - sender: 현재 호출을 수행하고 있는 메시지 발신자를 address 형식으로 받는다.
  - gas: 남은 가스양의 uint 형식으로 받는다.
  - value: 메시지와 함께 보낸 ether를 uint 형식으로 받는다.

- tx: 트랜잭션 데이터를 가지고 있다.
  - gasprice: 트랜잭션 가스 비용을 uint 형식으로 받는다.
  - origin: 트랜잭션 발신자를 address 형식으로 받는다.

- This: 현재 Contract를 참조한다. 현재 Contract 주소로 암시적으로 변환된다.

#### 상태 변수 접근 수준 ####

Contract 내의 State variable의 접근 수준을 지정할 수 있는 키워드들이 있다.

- public: public으로 State variable을 생성할 시에 compiler가 자동으로 getter 함수를 생성해준다. public state variable은 contract 내에서 직접 상태 변쑤를 사용할 수 도 있고 외부 contract나 클라이언트에서도 getter 함수를 이용해서 접근 할 수 있다.

- private: private으로 생성되면 같은 contract 내에서만 접근 할 수 있다.

- internal: State variable을 선언할때 접근 수준을 명시해주지 않으면 default 값으로 internal이 들어가게 된다. internal로 생성이 되면 현재 contract와 해당 contract를 상속받은 conract 만 접근 할 수 있다.

#### constant ####

상태 변수를 상수로 선언하는 것이다.

```solidity
contract foo {
  uint public constant constantBar = 123;
}
```

Solidity에서는 상수를 이렇게 선언한다.

#### function ####

이미 위의 코드에서 함수를 많이 선언해서 간단하게 설명을 해보자면

```solidity
contract foo {
  function bar(uint fooBar, uint8 barFoo) returns(uint8) {
	// function contents
	uint8 something = 12;
	return something;
  }
}
```

이런식으로 하면되고 반환해줄값이 있을경우 위의 코드와 같이 자료형만 적어주고 함수 내용에서 해당 값을 return이라는 키워드를 이용해서 직접 반환 할 수 도 있고

`returns(uint8 something)`이라고 적으면 uint8의 자료형을 가진 something이라는 변수가 자동적으로 반환된다. (즉 return 이라는 키워드를 명시하지 않아도 something이 반환된다.)

##### 함수 접근 수준 #####

상태 변수의 접근 수준과 같이 함수에도 접근 수준을 명시할 수 있는데 default 값은 public 이고 상태 변수의 접근 수준보다 한가지가 더있다.

- external: 상태 변수의 접근 수준에는 없는 키워드이다. 외부 contract나 클라이언트 쪽에서는 호출이 가능하나 내부에서는 불가능하다.

- public: 위에서 언급한대로 default 값이고 contract 내부, 외부, 클라이언트 모든 곳에서 호출이 가능하다.

- internal: contract 내부와 해당 contract를 상속한 contract에서만 호출이 가능하다.

- private: 내부에서만 호출 할 수 있다.

```solidity
contract fooContract {
  function foo(address userAddress, string userName) internal {
	// function contents
  }
  function bar(uint amount) private returns(bool) {
	// function contents
	bool isTrue = true;
	return isTrue;
  }
}
```

위의 코드와 같이 매개변수를 작성해준 괄호 다음에 붙여서 쓰면 된다.

#### view와 pure ####

- view: view 키워드를 사용한다면 해당 함수는 일반적으로 getter 함수라고 생각하면 편하겠다. 상태를 변경하지 않는 읽기 전용 함수를 명시한다.

- pure: 항상 view와 pure가 같이 언급이 되면서 뭔가 view의 반대되는 속성이라고 생각할 수 도 있는데 그게 아니라 JS를 배울때 나왔던 순수함수 (pure function) 같이 state variable을 읽지도 쓰지도 않는 함수이다.

```solidity
contract fooContract {
  uint public constant maximum = 1000;
  mapping(address => bool) public frozenAccount;

  function checkGas(uint amount) private pure returns(bool) {
	if(amount < maximum) return true;
	return false;
  }

  function validateAccount(address account) internal view returns(bool) {
	return frozenAccount[account];
  }
}
```

위의 코드에서 pure 함수와 view 함수가 어떻게 다른지 비교해보자!!!

#### payable ####

payable을 붙여서 함수를 선언하면 ether를 함께 보내며 함수를 실행시킬 수 있다.

smart contract를 배포하고 etherscan 혹은 remix에서 배포된 contract를 살펴보면 payable로 선언된 함수는

ether를 보낼 수 있게 빈칸이 나와있다.

#### constructor ####

OOP에서 많이 나오는 그 생성자가 맞고 contract가 처음에 실행될때 constructor 함수가 같이 실행되어 state variable을 초기화하거나 해줄수 있다.

```solidity
contract fooContract {
  address public add;

  constructor(address _add) internal {
	add = _add;
  }
}
```

#### slefdestruct() ####

`selfdestruct()` 함수를 이용하여 contract를 소멸 할 수 있다.

```solidity
selfdestruct(contract constructor address);
```

#### modifier ####

변경자라고 부르고 함수를 선언할때 미리 만들어둔 modifier를 추가하여 함수에 변경자를 적용할 수 있다.

변경자 코드는 `_;` 부분을 기준으로 실행시점이 달라지는데 여기서 어설프게 설명하는 것 보다 ERC-20을 만들어봤는데

해당 코드도 나중에 포스팅으로 올릴것이니 그 코드를 참고하며 어떻게 modifier가 작동하는지 확인 해보면 좋을것이다.

간단하게만 설명을 해보자면 `_;`를 기준으로 위에 있는 코드는 modifier가 적용된 함수가 실행하기 전에 먼저 동작하고

아래에 있는 코드는 modifier가 적용된 함수가 실행한 후에 동작하는것이다.

```solidity
modifier onlyOwner {
  require(isOwner, "Caller is not an owner");
  _;
}
```

위의 코드같이 쓰면 된다.

#### inheritance ####

Solidity는 다중 상속도 지원하며 `is` 를 이용해서 상속하면 된다.

```solidity
contract foo is bar1, bar2, bar3 {
  // contract contents
}
```

#### Errror handling ####

Solidity에서 error를 처리 할 때 `assert`, `require`, `revert` 함수를 사용한다.

- revert: revert는 코드에서 revert를 만났을때 바로 함수를 종료하고 error를 반환한다.

- assert와 require: revert와 다르게 require와 assert는 개발자가 설정한 조건이 참인지 확인하고 거짓일때만 error를 반환한다.

그러면 assert와 require가 헷갈릴 수 있는데 두가지는 차이가 있다.

일단 assert는 사용하지 않은 가스를 호출한 사람에게 반환하지 않는 반면 require는 남은 가스를 반환한다.

그리고 require는 함수를 실행하기전에 결과를 미리 알 수 있지만 assert는 함수를 실행해봐야지만 결과를 알 수 있다.

미리 안다는것이 무슨 말이냐 하면 remix로 contract를 배포하고 배포된 contract에서 특정 함수를 호출 할때 실행하기 전에 미리

메시지를 띄워서 함수가 제대로 실행되지 않을 수 도 있는데 실행을 할건지 말건지 묻는다.

```solidity
contract foo {
  function bar(uint amount) public payable {
	if(amount > msg.value) revert("Not enough ether");
  }
}
```

revert는 위의 코드와 같이 쓰면 되고

```solidity
contract foo {
  function bar(uint amount) public payable {
	require(amount > msg.value, "Not enough ether");
  }
}
```

require나 assert는 위의 코드처럼 쓰면 된다.

#### Enumerator ####

열거형은 특정 값들로 집합을 만들고, 그 집합안에 있는 값들로만 데이터를 가지는 것이다.

학부때 Unity를 사용해서 게임 개발을 했는데 거기에 있던 enum과 동일하다고 생각하면 되겠다.

```solidity
enum Characteristics {Angry, Good, Bad};

Characteristics rk = Characteristics.Good;

uint8 characteristicsIndex = uint8(rk); // enum 값 1을 정수형으로 반환
```

Unity라고 생각하고 쓰면 될거같다. 위의 코드처럼 쓰면 된다.

#### event ####

이벤트는 emit이라는 키워드를 이용해서 event를 일으키면 클라이언트 혹은 다른 contract에게 그 정보들을 전달해준다.

```solidity
contract foo {
  event Bar(address from, address to, uint amount);

  function bar(address to, uint amount) {
	// function contents
	emit Bar(msg.sender, to, amount);
  }
}
```

말로 설명하는 것보다 코드로 보는게 이해가 쉬울것 같다. 위와 같이 event를 정의하고 emit을 이용해서 event를 일으킬수있고

저렇게 실행된 event는 클라이언트에서 로그를 받아 볼 수 있다.

### 마치며 ###

Solidity의 기본 문법을 배우면서 이렇게 한번 정리를 해보았는데 다양한 언어를 접해봤고 툴들을 사용해본 경험이 있어서 그런지

크게 어렵지 않게 사용할 수 있을것 같다.

crypto zombie 같은 플랫폼을 이용해서 시간이 될때 solidity를 더 익숙해지게끔 노력을 할 필요가 있겠다.
