JS의 실행 컨텍스트는 실행할 코드에 제공할 환경 정보를 모아놓은 객체다

자바스크립트는 실행 컨텍스트가 활성화되는 시점에 다음 일을 한다

- 선언된 변수를 위로 끌어올린다 = 호이스팅
- 외부 환경 정보를 구성한다
- this 값을 설정한다

## 실행 컨텍스트
실행 컨텍스트란 실행할 코드에 제공할 환경 정보들을 모아놓은 객체다

동일 환경에 있는 코드를 실행할 때 필요한 환경 정보들을 모아 컨텍스트를 구성하고 이것을 콜스택에 쌓아 올린다

가장 위에 쌓여있는 컨텍스트와 관련된 코드를 실행하는 방법으로 코드의 환경 및 순서를 보장할 수 있다

### 컨텍스트 구성

구성 방법에 여러가지가 있지만 함수만 생각하면 된다
- 전역 공간
- eval() 함수
- 사용자 함수

다음 코드의 실행 컨텍스트 구성 순서는 다음과 같다

```Javascript
// ---- 1번
var a = 1;
function outer() {
	function inner() {
		console.log(a); //undefined
		var a = 3;
	}
	inner(); // ---- 2번
	console.log(a);
}
outer(); // ---- 3번
console.log(a);
```

코드실행 → 전역(in) → 전역(중단) + outer(in) → outer(중단) + inner(in) → inner(out) + outer(재개) → outer(out) + 전역(재개) → 전역(out) → 코드종료

![image](https://github.com/yanJuicy/blog/assets/43159295/5168d91e-26df-48a2-8605-8487585c099e)

특정 실행 컨텍스트가 생성되는 시점이 콜 스택의 맨 위에 쌓이는 순간이다


### 실행 컨텍스트 객체에 담기는 정보

#### 1. Variable Environment
- 현재 컨텍스트 내의 식별자 정보(record)를 가지고 있다
  - `var a = 3`에서 `var a`의 정보를 가진다 
- 외부 환경 정보를 가지고 있다
- 선언 시점 Lexical Environment의 스냅샷, 변경사항을 유지한다

#### 2. Lexical Environment
- Variable Environment와 동일하지만, 변경사항을 실시간으로 반영한다

#### 3. This Binding
- this 식별자가 바라봐야할 객체를 가진다
- this가 function 안에서 어떤 역할을 할지 결정한다
 

## VariableEnvironment, LexicalEnvironment

VE: 스냅샷을 유지한다

LE: 스냅샷을 유지하지 않는다. 실시간으로 변경사항을 계속해서 반영한다

> 실행 컨텍스트를 생성할 때 VE에 정보를 먼저 담은 다음 이를 그래도 복사해서 LE를 만든다
> 이후에는 주로 LE를 활용한다


### 구성 요소
- VE, LE 모두 동일하며 `environmentRecord`, `outerEnvironemntReference`로 구성한다
- environmentRecord (=record)
  - 현재 컨텍스트와 관련된 코드의 식별자 정보들이 저장된다
  - 함수에 지정된 매개변수, 식별자, 함수자체, var로 선언된 변수 식별자 등
- outerEnvironmentReference(=outer)


## LexicalEnvironment - environementRecord와 호이스팅

현재 컨텍스트와 관련된 코드의 식별자 정보들이 저장된다

함수에 지정된 매개변수 식별자, 함수 자체, var로 선언된 변수 식별자등을 수집한다

컨텍스트 내부를 처음부터 끝까지 순서대로 훑어가며 수집한다

순서대로 수집할 뿐 코드를 실행하지는 않는다


### 호이스팅

변수정보 수집을 마쳤어도 아직 실행 컨텍스트가 관여할 코드는 실행 전 상태다

JS 엔진은 코드 실행 전 모든 변수정보를 알고 있다

#### 호이스팅 규칙

1. 매개변수 및 변수는 선언부를 호이스팅 한다

```Javascript
function a () {
	var x = 1;
	console.log(x);
	var x;
	console.log(x);
	var x = 2;
	console.log(x);
}
a(1);
```

호이스팅 적용 후
```Javascript
function a () {
	var x;
	var x;
	var x;

	x = 1;
	console.log(x);
	console.log(x);
	x = 2;
	console.log(x);
}
a(1);
```

호이스팅 개념을 모르면 예측이 불가능한 어려운 결과다


2. 함수 선언은 전체를 호이스팅한다

```Javascript
function a () {
	console.log(b);
	var b = 'bbb';
	console.log(b);
	function b() { }
	console.log(b);
}
a();
```

호이스팅 적용 후
```Javascript
function a () {
	var b; // 변수 선언부 호이스팅
	function b() { } // 함수 선언은 전체를 호이스팅

	console.log(b);
	b = 'bbb'; // 변수의 할당부는 원래 자리에

	console.log(b);
	console.log(b);
}
a();
```

결과는 다음과 같다

<img width="553" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/e72eb8a6-9ada-41b7-acb4-c45c677202d3">


3. 함수 선언문, 함수 표현식

```Javascript
console.log(sum(1, 2));
console.log(multiply(3, 4));

function sum (a, b) { // 함수 선언문 sum
	return a + b;
}

var multiply = function (a, b) { // 함수 표현식 multiply
	return a + b;
}
```

호이스팅 적용 후
```Javascript
// 함수 선언문은 전체를 hoisting
function sum (a, b) { // 함수 선언문 sum
	return a + b;
}

// 변수는 선언부만 hoisting

var multiply; 

console.log(sum(1, 2));
console.log(multiply(3, 4));

multiply = function (a, b) { // 변수의 할당부는 원래 자리
	return a + b;
};
```

LexicalEnvironment는 record와 outer를 수집한다

record를 수집하는 과정에서 호이스팅이 일어나고 함수 선언문은 전체를 호이스팅 하고, 함수 표현식은 변수만 호이스팅한다



## LexicalEnvironment - 스코프, 스코프 체인, outerEnvironmentReference(=outer)

### 스코프
식별자에 대한 유효범위를 의미한다

### 스코프 체인
식별자의 유효범위를 안에서부터 바깥으로 차례로 검색하나가는 것이다

![image](https://github.com/yanJuicy/blog/assets/43159295/57ad35c5-d0c3-4e9a-957c-b50032ecbcec)

### outerEnvironmentReference(outer)

외부 환경의 참조정보다

스코프 체인이 가능하도록 하는것이라 할 수 있다


### 스코프 체인 예시

```Javascript
var a = 1;
var outer = function() {
	var inner = function() {
		console.log(a);
		var a = 3;
	};
	inner();
	console.log(a);
};
outer();
console.log(a); 
```

inner()를 호이스팅하면 다음과 같다
```Javascript
var inner = function() {
    var a;
    console.log(a);
    a = 3;
};
```

inner가 호출된 당시에는 위와 같으므로 이 때 LexicalEnvironment를 참조한다

따라서 `console.log(a);`는 undefined가 나온다


outer 함수의 `console.log(a)`가 실행될 때 콜스택에서 inner()는 사라진다

따라서 콜스택은 다음과 같이 된다

<img width="151" alt="image" src="https://github.com/yanJuicy/blog/assets/43159295/9eb666ba-c925-4978-890d-86ffdfab2660">

여기서 a를 참조해야 하는데 outer에는 없고 그 다음으로 가까운 요소인 전역 컨텍스트로 가서 a를 찾는다

전역 컨텍스트에서 `var a = 1;`를 참조하고 outer 함수의 `console.log(a);` 결과로 1이 출력된다



