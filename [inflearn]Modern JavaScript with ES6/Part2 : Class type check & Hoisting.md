# Part2 : Class의 type check, Hoisting

# 1. Class를 타입체크 해보면?

: **Function(함수)으로 나온다.**

### 함수는 객체다.

함수는 객체처럼 사용가능하다.

```jsx
function myMultiply(a, b) {
	return a*b;
}
//이 함수에 ten 속성 추가

myMultiply.ten = myMultiply(10, 10);
console.log(myMultiply.ten); //100
//마치 객체의 속성처럼 사용가능하다.
```

## 어떻게 Class 타입 체크시 function이 나올 수 있나?

예시 코드

```jsx
class Person {
	constructor(name, age) {
		this.name = name;
		this.age = age;
	}
	eat() {console.log('와구와구')}
	run() {console.log('달리자')}
	rest() {console.log('zzz')}
}

console.log(typeof Person); //function
```

### 클래스 선언문은 아래와 같은 내부 동작을 수행하기 때문.

(위 Person class 만드는 코드를 예시로)

1. Person 이름의 함수 생성
    
    ```jsx
    function Person() {}
    ```
    
2. 함수 본문에 constructor의 코드를 그대로 가져온다. constructor가 없으면 빈 함수를 생성한다.
 [this.name](http://this.name) = name; this.age=age 부분
    
    ```jsx
    function Person(name, age) {
    	this.name = name;
    	this.age = age;
    }
    ```
    
3. 메소드는 클래스와 같은 이름의 Person.prototype(프로토타입 객체)에 추가된다. 
    
    ```jsx
    Person.prototype.eat = function() {console.log('와구와구')};
    Person.prototype.run = function() {console.log('달리자')};
    Person.prototype.rest= function() {console.log('zzz')};
    ```
    

결국 프로토타입 객체에 메소드들을 추가해놓는 것이기 때문에, 
해당 클래스의 인스턴스가 생성되면, 추가된 메소드를 자유롭게 이용가능하다.
⇒ 이 부분은 프로토타입과 상속에 대한 이해가 필요하다.

# 2. 호이스팅(Hoisting)

## 함수 레벨 스코프 vs. 블록 레벨 스코프

```jsx
var a = 111;
console.log(a); // 111
{var a = 333;}
console.log(a); //?
```

⇒ 두 번째 찍히는 a의 값은 333이다.

### var 키워드의 다음 특징 때문.

1. 변수를 함수 레벨 스코프로 선언한다. 
2. 중복 선언이 가능하다. 

⇒ 함수가 아닌 블록 내에서 정의된 a는 전역변수가 된다.
만약 세번째 줄 블락을 function블락으로 바꾸면 두 번째 출력은 111이 된다.

## 호이스팅이란? : 끌어올리기

자바스크립트는 기본적으로 모든 선언문(var, let, const function, **그리고 class**)를 호이스팅한다.

→ 스코프 안 어디에서든 변수 선언은 최상위에서 선언한 것과 동일해진다.

var키워드와 let/const 키워드로 선언한 변수에 차이가 있지만 기본적으로 둘다 호이스팅된다.

```jsx
var testA;
console.log(testA); //undefined

let testB;
console.log(testB); //undefined

console.log(testC); //undefined
var testC;

console.log(testD); //Reference Error
let testD;
```

var 키워드로 변수 선언시 호이스팅과 변수 초기화가 되는 반면,

let & const 키워드로 선언시 호이스팅만 되고 변수 초기화 선언이 되지 않는다
 ⇒ 해당 변수에 접근하려고 할 때 오류가 발생.

## Class 호이스팅

```jsx
console.log(Person); //Reference Error : cannot access 'Person' before initialization
class Person {}
```

⇒ 초기화 전 접근할 수 없는 오류 : let & const 키워드와 같은 맥락.

### var vs. let, const, class 호이스팅 비교

```jsx
var str1 = 'Hello World"
const testFun = function() {
	console.log(str1);
	var str1 = 'Hello Jisu'; //해당 라인에 왔을 때 할당된다.
}
testFun(); //?
```

testFun 함수 실행결과는? : `undefined` .

### Why?

testFun내부에서 str1이 또 선언되어있기 때문에, 전역변수 str1은 참조하지 않는다. 해당 변수는 var로 선언되어서 호이스팅이되지만, 초기화만 된다. 
즉, 할당은 되지않는다. 값 할당은 해당 선언문에 왔을 때에서야 된다. 
선언된 str1을 출력하는 함수다. 따라서 undefined.

### 만약 let, const로 변수 선언했다면?

‘초기화 전 참조할 수 없음’ Referenc Error가 뜬다.

## 클래스 상속

```jsx
class Parent {}
class Child extends Parent {}; //이럴 경우 문제없다.

class Child2 extends Parent2 {}; // Reference Error
class Parent2 {}
```

class 키워드는 호이스팅 되어도 초기화 과정까진 안되기 떄문에 오류.
