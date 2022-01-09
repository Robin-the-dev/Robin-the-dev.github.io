---
layout: post
title: 일급객체(First-class citizen)와 고차함수(Higher order function)
subtitle: JavaScript Reference
categories: JavaScript
tags: [JavaScript, HoF, First-classCitizen, mapReduceModel, Closure, CurringFunction, FunctionComposition]
---

JavaScript의 일급객체와 고차함수
------------

2주차 회고에서도 말했다시피 2주차 마지막에 JavaScript의 일급객체와 고차함수라는 것에 대해 배웠다.

본론에 들어가기 앞서 간단히 일급객체와 고차함수에 대해 설명을 하자면 

`일급객체란 다른 객체들에 일반적으로 적용 가능한 연산을 모두 지원하는 객체를 가르킨다. [위키백과]`

`고차함수란 함수를 인자로 받거나(Callback function) 함수를 출력으로 반환하는 함수를 가르킨다.`

이렇다.

더 자세한 내용은 본문에서 정리 해보겠다.

### First-class citizen ###

**First-class citizen** 흔히들 일급객체라고 부른다.

위의 서론에 언급한것 처럼 일급객체는 다른 객체들에 적용 가능한 연산을 모두 지원하는 객체를 가르킨다.

그렇기 때문에 아래의 세가지 조건을 충족한다면 일급객체라고 부른다.

1. 변수나 데이터에 할당이 가능해야 한다.
2. 객체를 인자로 줄 수 있다.
3. 객체를 리턴 할 수 있다.

이것만 보면 알 수 있다시피 JavaScript에서는 Function은 일급객체이다.

아래의 코드를 살펴보자!

```javascript
const func = (arg) => console.log(arg);

const newFunc = (funcArg) => funcArg;

newFunc(func)('Hello World!'); // 'Hello World!'
```

코드에서 보다시피 함수를 변수에 할당 했고 함수를 다른 함수의 인자로 주었으며 그 함수는 함수를 리턴한다.

이처럼 JavaScript에서 함수는 일급객체이기 때문에 바로 다음에 설명할 고차함수라는 것을 만들 수 있다.

### Higher order function ###

고차함수란 말그대로 차항이 높은 함수를 뜻한다.

수학에서 일차함수 이차함수 이런 식으로 항이 높아짐에 따라 고차함수가 되는것이랑 같은 개념이다.

`f(x) = x + 1 => f(x) = x^2 + 1`

위와 같이 차항이 높아지는 것을 고차함수라고 부르고 이 개념을 그대로 JS 함수에 적용 한 것이다.

그렇기 때문에 JS에서 고차함수라는 것은 함수를 인자로 받거나 출력으로 반환하는 함수이다.

위의 일급객체에서 언급한대로 함수가 일급객체이기 때문에 가능한 일로 JS 또는 함수형 언어에서 가능한 부분이라고 한다.

코드 예제는 이미 일급객체에서 썼기 때문에 또 쓰지 않겠다.

### MapReduce Model ###

MapReduce 모델이란 프로그래밍 모델의 일종으로 굉장히 크기가 큰 빅데이터를 병렬로 처리하는 것을 말한다.

Word count algorithm이 MapReduce 모델을 가장 쉽게 설명 할 수 있기 때문에 예를 들어 보겠다.

Word count algorithm은 input, splitting, mapping, shuffling, reducing이라는 과정을 차례대로 거쳐 결과를 보여준다.

중간에 보이는 저 mapping을 통해 각 요소를 매핑해 횟수를 적어주고 reducing을 통해 중복되는 단어를 응축해 중복된 단어들의 횟수를 적어준다.

병렬처리 하기 위해서 프로세스가 간단해야하는데 그런 상황에서 쓰기 좋은 간단한 프로세스가 MapReduc 모델이다.

Reference: 
[Wiki](https://en.wikipedia.org/wiki/MapReduce), 
[Blog](https://velog.io/@juho00ng/MapReduce-Model).

### Closure와 Currying function ###

#### Closure ####

보통 Closure VS Currying function 이라고 되어있어서 상반되는 개념인가 했는데 전혀 아니었다.

일단 Closure는 자신의 함수 스코프 바깥에 있는 변수를 사용하는 것을 말한다.

```javascript
const func = (x) => {
	return (y) => {
		return x + y;
	}
}

const sumWithThree = func(3);

console.log(sumWithThree(5)); // 8
console.log(sumWithThree(4)); // 7
```

위의 코드를 `const func = x => y => x + y;`로 적을 수 있지만 가독성을 위해 일부러 풀어썼다.

func라는 함수에 인자를 3을 줘서 sumWithThree라는 변수에 다시 할당 해주었다.

이렇게 되면 func가 또다시 함수를 반환하기 때문에 sumWithThree라는 변수는 함수로 사용 할 수있고

인자를 3으로 주면서 할당했기 때문에 sumWithThree는 항상 x를 3으로 사용 할 수 있다.

그리고 위에서 언급한대로 x는 sumWithThree의 스코프 바깥이지만 사용할 수 있게 된다.

이것이 Closure이다.

#### Currying function ####

Currying function은 Closure와 상반되는 개념이 전혀아니고 오히려 Closure 개념을 사용하는 technique 중 하나이다.

Currying 이라는 것은 **여러 인자를 받는 함수를 하나의 인자만 받는 함수들의 배열로 변환**하는 것이다.

Currying 이라는 이름답게 함수형 언어 하스켈의 창시자인 Haskell Curry가 만든 것이고 Javascript를 함수형으로 사용할때 Closure와 더불어 Currying을 사용한다.

위의 Closure의 예제가 사실상 Currying function으로 작성한 것이고 일반적으로 위와 같은 sum 함수를 작성 할때는

```javascript
const func = (x, y) => x + y; // It is not currying function!!!
```

이런 식으로 함수 하나가 인자를 두개를 받아 바로 그 결과값을 리턴하게끔 한다.

#### Why Closure and Currying? ####

함수형 언어를 사용해보지 않았다면 (필자 포함) 사실 조금 어색한 개념이라고 생각할 수 있다.

왜냐하면 대부분 사람들이 사용하는 언어는 객체 지향 언어로서 class를 이용한 module pattern을 사용하고 있기때문이다.

Closure와 Currying을 이용하면 class를 이용한 것 처럼 모듈화 할 수 있고 내가 느끼기로는 그 생김새와 사용방법 까지 닮아 있다고 생각한다.

Closure와 Currying을 사용하는 이유는 더 손쉬운 관리와 더불어 namespace 오염을 줄일 수 있고 재사용 가능하게 할 수 있기 때문이라고 한다.

Reference: 
[Wiki](https://en.wikipedia.org/wiki/Currying), 
[Ref](https://www.digitalocean.com/community/tutorials/an-introduction-to-closures-and-currying-in-javascript).

### Function Composition ###

위의 내용과 다 관련이 있는 함수의 조합에 대한 내용도 한번 정리를 해본다.

함수 합성(Function Composition) 이란 **함수들을 조합하여 새로운 함수를 만드는 것**이다.

예를 들어 어떤 수를 받아 5를 곱하고 10을 빼는 함수를 만든다고 가정하면

```javascript
const multiplyFiveMinusTen = num => (num * 5) - 10;

const reulst = multiplyFiveMinusTen(10);

console.log(result); // 40
```

보통 이렇게 쓸 것이다.

이렇게 한번에 쓰게되면 **유지 보수가 힘들다**라는 단점이 생겨버린다.

예를 들어 위의 코드도 필요하고 또 다른 기능이 필요한데 이번에는 어떤 수를 받아 5를 곱하고 10을 나눈후에 10을 빼주는 기능의 함수가 필요하다고 가정하자!

그러면 

```javascript
const multiplyFiveMinusTen = num => (num * 5) - 10;

const result = multiplyFiveMinusTen(10);

console.log(result); // 40

const multiplyFiveDivideByTenMinusTen = num => ((num * 5) / 10) - 10;

const result2 = multiplyFiveDivideByTenMinusTen(10);

console.log(result); // -5
```

이렇게 함수를 또 만들어야한다. 함수의 리턴 부분만 봐도 많은 것들이 중복하고 있음을 알 수 있다.

그렇기 때문에 유지보수 측면에서도 그렇고 각각의 부분을 작은 함수로 나누어 그 함수들을 조합하는 편이

훨씬 간결하고 확장성도 뛰어나다. (마치 레고처럼 계속 붙일 수 있다.)

### compose와 pipe ###

함수의 조합 혹은 함성을 할때 가독성을 더 높여주기 위해 사용하는 함수를 만들어 줄 필요가 있다.

그것이 바로 compose()와 pipe()이다. 이 둘은 JS의 built-in function이 아니라 프로그래머가 함수를 조합할때 사용하는

직접 만드는 함수이다. 두 함수의 네이밍에 집중 할 필요가 있는데 compose는 여러 함수를 인자로 받았을때

맨 마지막 인자부터 첫인자까지 차례대로 적용을 시켜주는 것이고

pipe는 첫인자부터 차례대로 마지막인자까지 적용을 시켜주는 것이다.

코드와 함께 보면 이해가 더 편할 것이다.

```javascript
const compose = ...funcArgs => initialVal => ...funcArgs.reduceRight((val, func) => func(val), initialVal);

const result = compose(add2, multiply5, minus10)(5);
const result2 = add2(multiply5(minus10(5)));

console.log(result); // -23
console.log(result2); // -23
```

위의 코드에서 함수조합을 사용하는 compose 함수를 사용한 예와 함수를 각각 사용한 예를 다뤄봤다.

pipe 함수도 compose 함수와 같지만 적용을 첫번째 인자의 함수부터 마지막까지 차례대로 시켜준다.

```javascript
const pipe = ...funcArgs => initialVal => ...funcArgs.reduce((val, func) => func(val), initialVal);

const result = pipe(add2, multiply5, minus10)(5);

console.log(result); // 25
```

Reference: 
[Blog](https://velog.io/@nittre/JavaScript-%ED%95%A8%EC%88%98-%ED%95%A9%EC%84%B1Function-Composition).

### 마치며 ###

인터넷 여기저기를 찾아보며 최대한 내것으로 만들기 위해 한번 정리를 해보았다.

해석을 잘못했거나 분명히 틀린 부분이 있을 거라고 생각한다.

계속 해서 관련된 부분을 찾아보고 포스트 내에서 실수한 부분이 있으면 수정 tag를 달고 수정을 해야겠다.
