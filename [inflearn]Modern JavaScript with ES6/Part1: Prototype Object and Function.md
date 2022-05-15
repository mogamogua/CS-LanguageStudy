# Part1: Prototype객체와 생성자 함수

<aside>
💡 해당 글은 인프런의 ‘ES6문법과 함께하는 모던 자바스크립트 고급’ 무료강의를 들으며 정리한 내용입니다. 💡
</aside>

<br/>

# 프로토타입(Prototype)

“자바스크립트는 프로토타입 기반 언어이다.”라는 말을 들어본 사람이 있을 것이다. 
프로토타입이란 대체 무엇일까? 

prototype의 사전적 의미는 ‘원형’이다.  자바스크립트는 객체의 원형을 가지고 새로운 객체들(인스턴스)을 생성해가는 프로그래밍 방식으로 확장과 재사용성을 높였다.

생성된 객체들은 모두 자신에 대한 프로토타입 객체, 즉, 자신이 만들어지게 한 원형을 갖는다.
⇒ 해당 프로토타입(부모)을 통해 프로토타입이 갖고 있는 메소드들을 사용할 수 있다.

<br/>

## Prototype vs. Class

현재 자바스크립트는 Class문법을 지원하고 있지만, 원래는 프로토타입 기반 언어였기 때문에 원래 Class가 없었으며, 해당 기능 또한 prototype을 기반으로 구현되어있다.

prototype 객체는 *new 연산자에 의해 생성된 객체(**인스턴스**)*에 **공유 프로퍼티와 메소드등을 제공**하기 위해 사용된다.

그 예시,

```jsx
const fruit = {name: 'apple'}

fruit.expiration = '20220222', //속성 추가
//속성이 있는지 확인하는 메소드, hasOwnProperty() 사용해보기
console.log(fruit.hasOwnProperty('expiration')); //true
```

여기서 이상한 점! 우리는 hasOwnProperty라는 메소드를 fruit객체에 만든 적이 없는데 어떻게 사용할 수 있는 것일까?

```jsx
console.log(fruit);
//결과
{name: 'apple', expiration: '20220222'}
expiration: "20220222"
name: "apple"
[[Prototype]]: Object
constructor: ƒ Object()
hasOwnProperty: ƒ hasOwnProperty()
isPrototypeOf: ƒ isPrototypeOf()
propertyIsEnumerable: ƒ propertyIsEnumerable()
toLocaleString: ƒ toLocaleString()
toString: ƒ toString()
valueOf: ƒ valueOf()
__defineGetter__: ƒ __defineGetter__()
__defineSetter__: ƒ __defineSetter__()
__lookupGetter__: ƒ __lookupGetter__()
__lookupSetter__: ƒ __lookupSetter__()
__proto__: (...)
get __proto__: ƒ __proto__()
set __proto__: ƒ __proto__()
```

[[Prototype]]: Object를 보면 fruit객체가 Object로부터 만들어졌음을 알 수 있다.

즉, 연결된 프로토타입 객체를 확인할 수 있으며, 해당 프로토타입 객체가 갖고있는 메소드를 참조를 통해 사용가능하다.

<br/>
<br/>

## **proto**에 있는 메소드 덮어쓰기 가능?

hasOwnProperty()를  fruit에서 정의해버리면 어떻게 될까?

```jsx
const fruit2 = {
	name: 'apple', 
	expiration: '20220222', 
	hasOwnProperty: function(){console.log('안녕')},
}
fruit2.hasOwnProperty; //안녕
```

⇒ 이처럼 해당 객체가 갖고 있는 메소드를 먼저 출력하고, 객체에 해당 메소드가 없는 경우에 부모의 메소드를 사용한다.

<br/>
<br/>

# 자바스크립트 함수의 내부와 객체 간의 관계

함수가 만들어졌을 때,

```jsx
function Animal() {};
```
1) **내부에서 함수 자신과 같은 이름의 프로토타입 객체가 생성된다.**
![](https://velog.velcdn.com/images/mogamogua/post/c3ff5a27-3d78-425d-844f-fbe73e2b7bd1/image.png)

<br/>

**2) 함수 자신의 이름과 같은 프로토타입 객체를 참조하는 ‘prototype’속성이 함수에 추가되며, 프로토타입 객체에는 ‘constructor’가 생성되어 함수를 참조한다.** 
![](https://velog.velcdn.com/images/mogamogua/post/94025c80-08b4-45f2-bf29-77e93c46c1d7/image.png)

```jsx
console.dir(Animal);
//결과
ƒ Animal()
...
name: "Animal"
> **prototype: //constructor로 자신을 가리키는 프로토타입객체를 참조하고있다.
	> constructor: ƒ Animal() //위에서 만든 Animal 함수 참조
	> __proto__: Object //최상단 Object**
...
__proto__: ƒ () //함수도 객체이기 때문에 자신의 원형을 갖는다.
...
```

<br/>

3) **new 연산자와 생성자 함수로 만든 객체(=인스턴스)들은 모두 “__proto__” 속성이 생성**되며, 이 속성으로 자신을 만들어질 수 있게 한 **프로토타입 객체를 숨은 링크로 가리킨다.** 
![](https://velog.velcdn.com/images/mogamogua/post/a320b129-d56a-4424-818f-01598661f8d1/image.png)
 - __proto__로 가리키는 객체는 절대 생성자 함수 Animal이 아니라, Animal 프로토타입 객체이다. 헷갈리지 않기! 그림을 그려가며 생각하자.


```jsx
let cat = new Animal();
let tiger = new Animal();
console.log(cat);
//결과
Animal {}
> __proto__: Object //constructor로 Animal함수를 참조하는 프로토타입객체를 참조하는 속성.
	> constructor: ƒ Animal() 
	>__proto__: Object 
  //프로토타입객체도 객체이기 때문에 최상단 Object로부터 상속받는다.
  //(이를 통해 객체의 기본 메소드를 사용가능하다.)
```

→ 해당 인스턴스들은 프로토타입 객체에 있는 속성들과 메소드들을 모두 사용가능하다.(상속) 

<br/>
<br/>

## 공용 메소드, 속성 만들고 상속받기

예를 들어, 모든 동물들이 사용하는 메소드 run()이 있다고 할 때, 
**프로토타입 객체에 저장**하여 모든 **인스턴스들이 상속받아 사용할 수 있도록 한다.** 

### 1) 생성자 함수에 메소드 추가하는 경우

만약 공용되는 메소드를 생성자 함수에서 만들어서 아주 많은 인스턴스를 생성한다면 메모리 문제가 생길 수 있다
: 생성자로 만들어진 인스턴스 객체 모두에 공용 메소드가 추가되기 때문.
![](https://velog.velcdn.com/images/mogamogua/post/9541cba0-77f8-431f-b8f4-e008fa74729f/image.png)

```jsx
function Person() {
	this.run = function() {console.log('run')}
};
const jessie = new Person();
jessie.run(); //run 
//실행은 잘 되지만 인스턴스에 메소드가 추가되어있다. - new로 생성시 추가됨.

console.log(jessie);
//결과
- Person {run: ƒ}
	> **run: ƒ ()**
	> __proto__: Object //프로토타입 객체.
		> constructor: ƒ Person()
		> __proto__: Object
```

<br />


### 2) 프로토타입 객체에 메소드 추가하는 경우

위에서  보았듯, `instance.method()` 이렇게 객체 참조하여 메소드 사용할 때 해당 객체에 존재하는지 확인하고, 없으면 부모객체(원형객체)에서 찾아 사용한다.

⇒ 같은 생성자 함수로 만들어진 인스턴스들이 가리키는 원형 객체는 모두 동일하므로, 
프로토타입 객체에 추가하여 상속받게 하는 것이 좋다.
![](https://velog.velcdn.com/images/mogamogua/post/4c0dcbeb-7dfa-49b6-9468-f31f5d69b2e7/image.png)


```jsx
Animal.prototype.run= function() {
	console.log("뛴다");
}
//여기서 Animal.prototype은 Animal의 이름을 가진 프로토타입 객체(그림의 오른쪽)임.

cat.run(); //뛴다.
```

<br/>
<br/>
<br/>


## 🍈정리
![](https://velog.velcdn.com/images/mogamogua/post/f4ec25be-6d4f-41ee-ac6e-b201f2fdf73d/image.png)


1. 어떤 함수의 prototype 객체는 함수로 만들어진 객체(인스턴스)의 원형이 되는 객체다.
2. 모든 인스턴스는 “__proto__” 속성으로 함수의 prototype 객체를 참조하고 있으며, 이를 통해 prototype 객체의 속성, 메소드에 접근 및 사용이 가능하다.
3. 자식 객체에서 부모(원형)객체의 메소드를 재정의하면 해당 객체에 한해서 덮어 써진다.
4. 모든 인스턴스들이 사용할 속성과 메소드의 추가, 수정, 삭제는 함수의 prototype 속성을 통해 적용하는 것이 좋다.
5. 프로토타입의 멤버를 읽을 땐 객체명(`cat.run()` ) 또는 함수의 prototype속성을 통해(`Animal.prototype.run()` ) 접근할 수 있다. 

### 추가 참고사항

- arrow function으로 만들어진 함수는 prototype속성을 갖지 않는다.
- 일반 객체는 prototype속성을 갖지 않는다.
- 단방향 참조를 위해 [[prototype]]슬롯에 직접 접근할 수 없고, ‘__proto__’속성으로만 원형객체에 접근할 수 있다. 또는 `Object.getPrototypeOf()` 메소드를 사용할 수 있다.
- instance에서 바로 생성자함수에 접근하고 싶다면 `instance.constructor` 로 접근하면 된다.


### reference
[프로토타입: [[Prototype]], __proto__, prototype 프로퍼티](https://www.howdy-mj.me/javascript/prototype-and-proto/)

[Javascript __proto__ vs prototype 차이](https://velog.io/@h0ngwon/Javascript-proto-vs-prototype-%EC%B0%A8%EC%9D%B4)

[ES6 문법과 함께하는 모던 Javascript(자바스크립트) 고급 Part.1 - 인프런 | 강의](https://www.inflearn.com/course/%EB%AA%A8%EB%8D%98-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EA%B3%A0%EA%B8%89-1/dashboard)
