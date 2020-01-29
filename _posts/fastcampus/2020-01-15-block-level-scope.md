---
layout: fs-post
title: <strong>let, const와 블록 레벨 스코프</strong>
categories: fastcampus
section: fastcampus
seq: 15
permalink: /:categories/:title
description:
---

* TOC
{:toc}

# 1. var 키워드로 선언한 변수의 문제점

ES5까지 변수를 선언할 수 있는 유일한 방법은 var 키워드를 사용하는 것이었다. var 키워드로 선언된 변수는 아래와 같은 특징이 있다. 이는 다른 언어와는 구별되는 독특한 특징으로 주의를 기울이지 않으면 심각한 문제를 일으킨다.

## 1.1.	변수 중복 선언 허용

var 키워드로 선언한 변수는 중복 선언이 가능하다. 아래 예제를 살펴보자.

```javascript
var x = 1;
// var 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용한다.
// 아래 변수 선언문은 자바스크립트 엔진에 의해 var 키워드가 없는 것처럼 동작한다.
var x = 100;
console.log(x); // 100
```

위 예제의 변수 x는 중복 선언되었다. 이처럼 같은 스코프 내에서 변수를 중복 선언하면 나중에 작성된 변수 선언문은 자바스크립트 엔진에 의해 var 키워드가 없는 것처럼 동작한다. 이때 에러는 발생하지 않는다.

위 예제와 같이 만약 동일한 변수 이름이 이미 선언되어 있는 것을 모르고 변수를 중복 선언하면서 값까지 할당했다면 의도치 않게 먼저 선언된 변수값이 변경되는 부작용이 발생한다. 따라서 변수의 중복 선언은 문법적으로 허용되지만 사용하지 않는 것이 좋다.

## 1.2.	함수 레벨 스코프

var 키워드로 선언한 변수는 오로지 함수의 코드 블록 만을 지역 스코프로 인정한다. 따라서 함수 외부에서 선언한 변수는 모두 전역 변수이다.

```javascript
var x = 1;

if (true) {
  // var 키워드로 선언된 변수는 함수의 코드 블록 만을 지역 스코프로 인정한다.
  // 함수 밖에서 선언된 변수는 코드 블록 내에서 선언되었다 할 지라도 모두 전역 변수이다.
  // 따라서 x는 전역 변수이다. 이미 선언된 전역 변수 x가 있으므로 변수 x는 중복 선언된다.
  // 이는 의도치 않게 변수값이 변경되는 부작용을 발생시킨다.
  var x = 10;
}

console.log(x); // 10
```

for 문의 변수 선언문에서 var 키워드로 선언한 변수도 전역 변수가 된다.

```javascript
var i = 10;

// for문에서 선언한 i는 전역 변수이다. 이미 선언된 전역 변수 i가 있으므로 중복 선언된다.
for (var i = 0; i < 5; i++) {
  console.log(i); // 0 1 2 3 4
}

// 의도치 않게 변수의 값이 변경되었다.
console.log(i); // 5
```

함수 레벨 스코프는 전역 변수를 남발할 가능성을 높인다. 이로 인해 의도치 않게 전역 변수가 중복 선언되는 경우가 발생한다.

## 1.3.	변수 호이스팅

var 키워드로 변수를 선언하면 변수 호이스팅에 의해 변수 선언문이 스코프의 선두로 끌어 올려진 것처럼 동작한다. 즉, 변수 호이스팅에 의해 var 키워드로 선언한 변수는 변수 선언문 이전에 참조할 수 있다. 단, 할당문 이전에 변수를 참조하면 언제나 undefined를 반환한다.

```javascript
// 이 시점에는 변수 호이스팅에 의해 이미 변수 foo가 선언되었다. (1. 선언 단계)
// 변수 foo는 undefined로 초기화된다. (2. 초기화 단계)
console.log(foo); // undefined

// 변수에 값을 할당 (3. 할당 단계)
foo = 123;

console.log(foo); // 123

// 변수 선언은 런타임 이전에 자바스크립트 엔진에 의해 암묵적으로 실행된다.
var foo;
```

변수 선언문 이전에 변수를 참조하는 것은 변수 호이스팅에 의해 에러를 발생시키지는 않지만 프로그램의 흐름 상 맞지 않을 뿐더러 가독성을 떨어뜨리고 오류를 발생시킬 여지를 남긴다.

# 2.	let 키워드

이러한 var 키워드의 단점을 보완하기 위해 ES6에서는 let과 const 키워드를 도입하였다. let 키워드는 var 키워드와 동일하게 변수를 선언할 때 사용한다. var 키워드와의 차이점을 중심으로 let 키워드에 대해 살펴보자.

## 2.1.	변수 중복 선언 금지

var 키워드로 동일한 이름을 갖는 변수를 중복 선언하면 아무런 에러가 발생하지 않는다. 이때 변수를 중복 선언하면서 값까지 할당했다면 의도치 않게 먼저 선언된 변수값이 재할당되어 변경되는 부작용이 발생한다.

하지만 let 키워드로 동일한 이름을 갖는 변수를 중복 선언하면 문법 에러(SyntaxError)가 발생한다.

```javascript
var foo = 123;
// var 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용한다.
// 아래 변수 선언문은 자바스크립트 엔진에 의해 var 키워드가 없는 것처럼 동작한다.
var foo = 456;

let bar = 123;
// let이나 const 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용하지 않는다.
let bar = 456; // SyntaxError: Identifier 'bar' has already been declared
```

## 2.2.	블록 레벨 스코프

var 키워드로 선언한 변수는 오로지 함수의 코드 블록 만을 지역 스코프로 인정하는 함수 레벨 스코프를 따른다. 하지만 let 키워드로 선언한 변수는 모든 코드 블록(함수, if 문, for 문, while 문, try/catch 문 등) 을 지역 스코프로 인정하는 블록 레벨 스코프(Block-level scope)를 따른다. 아래 예제를 살펴보자.

```javascript
let foo = 123; // 전역 변수

{
  let foo = 456; // 지역 변수
  let bar = 456; // 지역 변수
}

console.log(foo); // 123
console.log(bar); // ReferenceError: bar is not defined
```

let 키워드로 선언된 변수는 블록 레벨 스코프를 따른다. 따라서 위 예제에서 코드 블록 내에 선언된 변수 foo와 지역 변수이다. 전역에서 선언된 변수 foo와는 다른 별개의 변수이다. 또한 변수 bar도 블록 레벨 스코프를 갖는 지역 변수이다. 따라서 전역에서는 변수 bar를 참조할 수 없다.

함수도 코드 블록이므로 스코프를 만든다. 이때 함수 내의 코드 블록은 함수 레벨 스코프에 중첩된다.

![](/assets/fs-images/15-1.png)
{: .w-450 }
블록 레벨 스코프의 중첩
{: .desc-img}

## 2.3.	변수 호이스팅

var 키워드로 선언한 변수와는 달리 let 키워드로 선언한 변수는 변수 호이스팅이 발생하지 않는 것처럼 동작한다. 아래 예제를 살펴보자.

```javascript
console.log(foo); // ReferenceError: foo is not defined
let foo;
```

이처럼 let 키워드로 선언한 변수를 변수 선언문 이전에 참조하면 참조 에러(ReferenceError)가 발생한다.

"4.2 변수 선언"에서 살펴본 바와 같이 var 키워드로 선언한 변수는 런타임 이전에 자바스크립트 엔진에 의해 암묵적으로 "선언 단계"와 "초기화 단계"가 한번에 진행된다.

즉, 선언 단계에서 스코프(실행 컨텍스트의 렉시컬 환경(Lexical Environment))에 변수 식별자를 등록하여 자바스크립트 엔진에 변수의 존재를 알린다. 그리고 즉시 초기화 단계에서 undefined로 변수를 초기화한다. 따라서 변수 선언문 이전에 변수에 접근하여도 스코프에 변수가 존재하기 때문에 에러가 발생하지 않는다. 다만 undefined를 반환한다. 이후 변수 할당문에 도달하면 비로소 값이 할당된다.

```javascript
// var 키워드로 선언한 변수는 런타임 이전에 선언 단계와 초기화 단계가 실행된다.
// 따라서 변수 선언문 이전에 변수를 참조할 수 있다.
console.log(foo); // undefined

var foo;
console.log(foo); // undefined

foo = 1; // 할당문에서 할당 단계가 실행된다.
console.log(foo); // 1
```

![](/assets/fs-images/15-2.png)
{: .w-450 }
var 키워드로 선언한 변수의 생명 주기
{: .desc-img}

**let 키워드로 선언한 변수는 "선언 단계"와 "초기화 단계"가 분리되어 진행된다.** 즉, 런타임 이전에 자바스크립트 엔진에 의해 암묵적으로 선언 단계가 먼저 실행되지만 초기화 단계는 변수 선언문에 도달했을 때 실행된다.

만약 초기화 단계가 실행되기 이전에 변수에 접근하려고 하면 참조 에러(ReferenceError)가 발생한다. 따라서 스코프의 시작 지점부터 초기화 단계 시작 지점(변수 선언문)까지는 변수를 참조할 수 없다.

스코프의 시작 지점부터 초기화 시작 지점까지의 구간을 **일시적 사각지대(Temporal Dead Zone; TDZ)**라고 부른다.

```javascript
// 런타임 이전에 선언 단계가 실행된다.
// 아직 변수가 초기화되지 않았다. 따라서 변수 선언문 이전에 변수를 참조할 수 없다.
console.log(foo); // ReferenceError: foo is not defined

let foo; // 변수 선언문에서 초기화 단계가 실행된다.
console.log(foo); // undefined

foo = 1; // 할당문에서 할당 단계가 실행된다.
console.log(foo); // 1
```

![](/assets/fs-images/15-3.png)
{: .w-450 }
let 키워드로 선언한 변수의 생명 주기
{: .desc-img}

결국 let 키워드로 선언한 변수는 변수 호이스팅이 발생하지 않는 것처럼 보인다. 하지만 그렇지 않다. 아래 예제를 살펴보자.

```javascript
let foo = 1; // 전역 변수

{
  console.log(foo); // ReferenceError: foo is not defined
  let foo = 2; // 지역 변수
}
```

let 키워드로 선언한 변수의 경우, 변수 호이스팅 발생하지 않는다면 위 예제는 전역 변수 foo의 값을 출력해야 한다. 하지만 let 키워드로 선언한 변수도 여전히 호이스팅이 발생하기 때문에 참조 에러(ReferenceError)가 발생한다.

자바스크립트는 ES6에서 도입된 let, const를 포함하여 모든 선언(var, let, const, function, function*, class 등)을 호이스팅한다.

## 2.4.	전역 객체와 let

전역 객체(Global Object, "21.4. 전역 객체" 참고)는 코드가 실행되기 이전 단계에 자바스크립트 엔진에 의해 어떤 객체보다도 먼저 생성되는 특수한 객체이며 어떤 객체에도 속하지 않은 최상위 객체이다. 전역 객체는 클라이언트 사이드 환경(브라우저)에서는 window, 서버 사이드 환경(Node.js)에서는 global 객체를 가리킨다.

var 키워드로 선언한 전역 변수와 선언하지 않은 변수에 값을 할당한 암묵적 전역("21.4.3. 암묵적 전역" 참고) 그리고 전역 함수는 전역 객체의 프로퍼티가 된다. 전역 객체의 프로퍼티를 참조할 때 window를 생략할 수 있다.

```javascript
// 이 예제는 브라우저 환경에서 실행시켜야 한다.

// 전역 변수
var x = 1;
// 암묵적 전역
y = 2;
function foo() {}

// var 키워드로 선언한 전역 변수는 전역 객체의 프로퍼티이다.
console.log(window.x); // 1
// 전역 객체의 프로퍼티는 전역 변수처럼 사용할 수 있다.
console.log(x); // 1

// 암묵적 전역은 전역 객체의 프로퍼티이다.
console.log(window.y); // 2
console.log(y); // 2

// 함수 선언문으로 정의한 전역 함수는 전역 객체의 프로퍼티이다.
console.log(window.foo); // ƒ foo() {}
// 전역 객체의 프로퍼티는 전역 변수처럼 사용할 수 있다.
console.log(foo); // ƒ foo() {}
```

let 키워드로 선언한 전역 변수는 전역 객체 window의 프로퍼티가 아니다. 즉, window.foo와 같이 접근할 수 없다. let 전역 변수는 보이지 않는 개념적인 블록(전역 렉시컬 환경의 선언적 환경 레코드. 이에 대해서는 "23. 실행 컨텍스트"에서 자세히 살펴볼 것이다.) 내에 존재하게 된다.

```javascript
// 이 예제는 브라우저 환경에서 실행시켜야 한다.
let x = 1;

// let, const 키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 아니다.
console.log(window.x); // undefined
console.log(x); // 1
```

# 3.	const 키워드

const 키워드는 상수(constant 변하지 않는 고정된 값)를 선언하기 위해 사용한다. 하지만 반드시 상수만을 위해 사용하지는 않는다. 이에 대해서는 후반부에 설명한다. const의 특징은 let과 대부분 동일하므로 let과 다른 점만 살펴보도록 하자.

## 3.1.	선언과 초기화

let 키워드로 선언한 변수는 재할당이 자유로우나 **const 키워드로 선언한 변수는 재할당이 금지된다.**

```javascript
// 0.1은 변해서는 않되는 상수로서 사용될 값이다.
// 변수 이름을 대문자로 선언하여 상수를 저장하고 있음을 명확히 나타낸다.
const TAX_RATE = 0.1;

// const 키워드로 선언한 변수는 재할당이 금지된다.
// 상수는 재할당이 금지된 변수이다.
TAX_RATE = 0.2; // TypeError: Assignment to constant variable.
```

const 키워드로 선언한 변수는 값을 변경, 즉 재할당할 수 없으므로 처음 할당한 값을 그대로 유지한다. 따라서 변하지 않는 고정된 값, 즉 상수를 변수에 할당하기 위해 사용한다.

**const 키워드로 선언한 변수는 반드시 선언과 동시에 할당이 이루어져야 한다.** 그렇지 않으면 아래와 같이 문법 에러(SyntaxError)가 발생한다.

```javascript
const FOO; // SyntaxError: Missing initializer in const declarationㅜ
```

const 키워드로 선언한 변수는 let 키워드로 선언한 변수와 마찬가지로 블록 레벨 스코프를 갖는다.

```javascript
{
  const TAX_RATE = 0.1;
  console.log(TAX_RATE); // 0.1
}
console.log(TAX_RATE); // ReferenceError: TAX_RATE is not defined
```

## 3.2.	상수

상수는 가독성과 유지보수의 편의를 위해 적극적으로 사용해야 한다. 아래 예제를 살펴보자.

```javascript
// 세전 가격
let preTaxPrice = 100;

// 세후 가격
// 0.1의 의미를 명확히 알기 어렵기 때문에 가독성이 좋지 않다.
let afterTaxPrice = preTaxPrice + (preTaxPrice * 0.1);

console.log(afterTaxPrice); // 110
```

코드 내에서 사용한 0.1은 어떤 의미로 사용하였는지 명확히 알기 어렵기 때문에 가독성이 좋지 않다. 상수를 사용하여 값의 의미를 명확히 할 수 있다. 상수를 값으로 갖는 변수는 일반적으로 변수 이름을 대문자로 선언하여 상수를 저장하고 있음을 명확히 나타낸다. 여러 단어로 이루어진 경우에는 언더스코어(_)로 구분하는 것이 일반적이다.

```javascript
// 세율
const TAX_RATE = 0.1

// 세전 가격
let preTaxPrice = 100;

// 세후 가격
let afterTaxPrice = preTaxPrice + (preTaxPrice * TAX_RATE);

console.log(afterTaxPrice); // 110
```

세율은 쉽게 바뀌지 않는 값이며 프로그램 전체에서 고정된 값을 사용해야 한다. 상수로 세율을 정의하면 값의 의미를 쉽게 파악할 수 있다. 또한 상수는 프로그램 전체에서 공통 사용하므로 나중에 세율이 변경되면 상수만을 변경하면 되기 때문에 유지보수성이 대폭 향상된다.

## 3.3.	const 키워드와 객체

const 키워드로 선언된 변수는 재할당이 금지된다. const 키워드로 선언된 변수에 원시 값을 할당한 경우, 원시 값은 변경할 수 없는 값(immutable value)이고 const 키워드에 의해 재할당이 금지되므로 할당된 값을 변경할 수 있는 방법은 없다.

const 키워드로 선언한 변수에 객체를 할당한 경우, 원시 값을 할당한 경우와 마찬가지로 재할당이 금지된다. 그러나 객체는 변경 가능한 값(mutable value)이다. 따라서 const 키워드로 선언된 변수에 할당된 객체는 변경이 가능하다.

```javascript
const person = {
  name: 'Lee'
};

// 객체는 변경 가능한 값이다.
person.name = 'Kim';

console.log(person); // {name: "Kim"}
```

const 키워드는 재할당을 금지할 뿐 "불변(immutable)"을 의미하지는 않는다. 다시 말해, 새로운 객체를 재할당하는 것은 불가능하지만 객체의 내용(프로퍼티의 추가, 삭제, 프로퍼티 값의 변경)을 변경하는 것은 가능하다. 객체의 내용이 변경되더라도 변수에 할당된 주소값은 변경되지 않는다.

# 4. var vs. let vs. const

변수 선언에는 기본적으로 const를 사용하고 let은 재할당이 필요한 경우에 한정해 사용하는 것이 좋다. 원시 값의 경우, 가급적 상수를 사용하는 편이 좋다. 그리고 객체를 재할당하는 경우는 생각보다 흔하지 않다. const 키워드를 사용하면 의도치 않은 재할당을 방지해 주기 때문에 보다 안전하다.

var와 let, 그리고 const 키워드는 다음처럼 사용하는 것을 추천한다.

-	ES6를 사용한다면 var 키워드는 사용하지 않는다.
-	재할당이 필요한 경우에 한정해 let 키워드를 사용한다. 이때 변수의 스코프는 최대한 좁게 만든다.
-	변경이 발생하지 않는(재할당이 필요 없는 상수) 원시 값과 객체에는 const 키워드를 사용한다. const 키워드는 재할당을 금지하므로 var, let 보다 안전하다.

변수를 선언하는 시점에는 재할당이 필요할지 잘 모르는 경우가 많다. 그리고 객체는 의외로 재할당을 하는 경우가 드물다. 따라서 변수를 선언할 때에는 일단 const 키워드를 사용하도록 하자. 반드시 재할당이 필요하다면(반드시 재할당이 필요한지 한번 생각해 볼 일이다.) 그때 const를 let 키워드로 변경해도 결코 늦지 않는다.