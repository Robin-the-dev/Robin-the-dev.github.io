---
layout: post
title: Typescript Decorator
subtitle: Decorator
categories: TypeScript
tags: [Typescript, Decorator]
---

Typescript Decorator 정리
------------

Nest.js를 사용하면서 Decorator를 정리할 필요가 있어 정리해본다.

### Class Decorator ###

Class decorator applies to class constructor to read or modify class definition.

Below example is to show adding new property to class.

```typescript
function reportableClassDecorator<T extends { new (...args: any[]): {} }>(constructor: T) {
  return class extends constructor {
    reportingURL = "http://www.example.com";
  };
}

@reportableClassDecorator
class BugReport {
  type = "report";
  title: string;

  constructor(t: string) {
    this.title = t;
  }
}

const bug = new BugReport("Needs dark mode");
console.log(bug);

// {type: 'report', title: 'Needs dark mode', reportingURL: 'http://www.example.com'}
```

### Method Decorator ###

Method decorator applies to method's property descriptor to read or modify method definition.

```typescript
function HandleError() {
  return function(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    console.log(target)
    console.log(propertyKey)
    console.log(descriptor)

    const method = descriptor.value;

    descriptor.value = function() {
      try {
        method();
      } catch (e) {
        // 에러 핸들링 로직 구현
        console.log(e);
      }
    }
  };
}

class Greeter {
  @HandleError()
  hello() {
    throw new Error('테스트 에러');
  }
}

const t = new Greeter();
t.hello();

// {constructor: ƒ, greet: ƒ}
// hello
// {value: ƒ, writable: true, enumerable: false, configurable: true}
// Error: 테스트 에러
```

### Property Decorator ###

```typescript
function format(formatString: string) {
  return function (target: any, propertyKey: string): any {
    let value = target[propertyKey];

    function getter() {
      return `${formatString} ${value}`;
    }

    function setter(newVal: string) {
      value = newVal;
    }

    return {
      get: getter,
      set: setter,
      enumerable: true,
      configurable: true,
    }
  }
}

class Greeter {
  @format('Hello')
  greeting: string;
}

const t = new Greeter();
t.greeting = 'World';
console.log(t.greeting);

// Hello World
```

### Parameter Decorator ###

```typescript
import { BadRequestException } from '@nestjs/common';

// parameter decorator
function MinLength(min: number) {
  return function (target: any, propertyKey: string, parameterIndex: number) {
    target.validators = {
      minLength: function (args: string[]) {
        return args[parameterIndex].length >= min;
      }
    }
  }
}

// function decorator
function Validate(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
  const method = descriptor.value;

  descriptor.value = function(...args) {
    Object.keys(target.validators).forEach(key => {
      if (!target.validators[key](args)) {
        throw new BadRequestException();
      }
    })
    method.apply(this, args);
  }
}

class User {
  private name: string;

  @Validate
  setName(@MinLength(3) name: string) {
    this.name = name;
  }
}

const t = new User();
t.setName('Dexter');
console.log('----------')
t.setName('De'); // 에러 발생
```

## Reference ##

[Blog](https://wikidocs.net/158481).

## P.S ##

There is one more decorator called 'Accessor Decorator' and it will be added to this post later.
