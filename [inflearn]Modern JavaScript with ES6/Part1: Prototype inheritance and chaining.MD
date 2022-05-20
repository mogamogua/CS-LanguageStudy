# Prototype 상속과 체이닝

### Q. 자바스크립트는 객체 지향 언어인가?

- Yes. but, 다르언어와 다르게 프로토타입 상속에 기반을 둔 OOP언어이다.

 <br/>
 <br/>
 <br/>

# 프로토타입 상속의 필요성 : “재사용”

우리 집에 망치와 드라이버가 없다. ⇒ 아빠집에 간다. 여기도 없으면 ⇒ 할아버지 댁으로 간다.

이미 부모 객체에 정의된 속성과 메소드를 그대로 물려받아서 재사용이 가능하다.

새로운 기능을 자식 객체에서 추가해서 기존 기능을 더 확장시킬 수도 있다.

<br/>

### 프로토타입 체인

`__proto__` : 상속을 해준 부모를 가리킴 ⇒ 자식 객체가 부모객체의 메소드, 속성을 사용가능.

⇒ 이 체인을 바꿀 수 있을까? : Yes.

```jsx
let obj1 = {name: "jisu", age: 25,
						sayHi: function() {console.log("Hi" + this.name);}
					};
let obj2 = {name: "diqiu"}
```

→ obj2의 `__proto__`가 가리키는 객체는 최상위Object이다. (생성자 함수로 만들어지지 않았기 때문)

→ obj2객체의 `__proto__`속성이 가리키는 것을 바꿀 수 있나?

```jsx
obj2.__proto__ = obj1; //부모바꾸기
console.log(obj2.name); //diqiu (obj2에 이미 이름이있기 때문)
console.log(obj2.age); //25 부모에서 참조.
```

이렇게 부모 변경은 가능하지만, 변경하는 일은 드물다.

<br/>

## 프로토타입 체이닝 정리

자바스크립트는 자신에게 없는 속성과 메소드를 `__proto__`가 가리키는 prototype(원형)에서 가져온다.  이 때, 가장 하위 자식 객체에서부터 순차적으로 연결되어있는 원형을 찾아 올라간다.
 **= 프로토타입 체인**

자바스크립트는 이 프로토타입 체인을 이용해 객체의 특성을 다른 객체로 공유, 전파한다.
 = 프로토타입 상속으로 구현하는 객체 지향 언어를 “ 프로토타입 기반 객체 지향 언어”라고한다

### 객체의 프로토타입 출력하기

```jsx
console.log(obj.__proto__); //일부 브라우저에서 지원하지 않는다.
console.log(Object.getPrototypeOf(obj));
```

<br/>
<br/>

# new 연산자의 내부 동작

내부적으로 빈 객체를 생성하고 같은 이름의 ‘프로토타입 객체’를 새로운 객체의 원형으로 설정한다.
 1. 빈 객체를 생성한다.

1. 프로토타입 객체를 `__proto__` 속성으로 참조하게 한다.

==

```jsx
//new 키워드 내부 동작
const cat = new Animal('nya');

const newObj = {}; // 빈 객체 생성
newObj.__proto__ = Animal.prototype; //프로토타입객체를 __proto__속성으로 참조시킴
Animal.apply(newObj, 'nya'); 빈객체의 this.name에 'nya'를 전달하여 바인딩한다.
```

![자바스크립트-함수와객체와의-관계- 수정003](https://user-images.githubusercontent.com/62272445/168965531-494f4c23-f748-4dd1-a004-07411151ec29.png)

### new 연산자 정리

생성자 함수와 new 연산자에 의해 새로운 객체를 생성하는 것은, 객체 생성과 새 객체의 prototype 지정, 파라미터로 받은 초기화 값을 생성 시에 편리하게 지정한다는 장점이 있다. -  코드 단 한 줄로.

<br/>
<br/>

# 생성자 함수의 내부 동작

객체들을 여~러 개 만들어야 할 때, 같은 구조의 객체를 만들어내는 생성자함수를 만들어놓고 사용한다.

= 비슷한 객체들을 만들어내는 ‘공장’으로 비유가능

<br/>

## 생성자 함수의 규칙

- **첫 글자는 주로 대문자**를 사용하여 작성한다. ex) Animal, Person, ...
- 새롭게 생성되는 객체 자신을 가리키도록 **this를 사용**한다.
- **new 연산자**를 사용해서 새로운 객체 생성한다.

```jsx
function Person(name, age) {
	//this = {}; //빈 객체 생성.
	this.name = name;
	this.age = age;
	return this; //새 객체 리턴.
}
```

<br/>
<br/>

# 인스턴스 : new 연산자로 생성된 객체

생성자 함수와 new객체(인스턴스) 간의 관계는 딱히 없지만, new 연산자가 생성자 함수를 이용해서 새로운 객체를 생성한다.

**하지만 이 인스턴스들의 부모는 생성자 함수가 아니라, 생성자함수와 같은 이름을 가진 prototype객체이다!**

→ 해당 프로토타입이 어떻게 연결되는지 잘 기억하는 것이 좋다.

### 인스턴스가 어떤 생성자 함수로 만들어졌는지 체크하는 방법

- prototype객체의 ‘constructor’ 참조를 이용한다.

```jsx
function Factory() {}
const natureRepublic = new Factory();
console.log(natureRepublic.constructor);// Factory()를 가리킨다.
console.log(Factory.prototype.__proto__); //Object(최상위)
```

생성자함수에서는 ‘prototype’속성으로 참조.

인스턴스에서는 ‘__proto__’속성으로 참조하는듯.

- 인스턴스가 A로 생성된 것인지 판별 : instanceOf() 메소드 사용하기

```jsx
console.log(natureRepublic instanceOf Factory);//true
//이항연산자라서 이렇게 사용함.
```

객체 리터럴로 생성된 경우, 최상위 오브젝트를 오른쪽에 두면 true가 리턴됨.
```jsx
Person instanceof Function // true
Person instanceof Object // true
Function instanceof Object // true
```

- instanceOf 연산자는 특정 객체가 프로토타입 체인에 연결되어 있는지를 체크하는 방법 중 하나이다.
- <br/>
```jsx
Person.isPrototypeOf(jisu) //false
Person.prototype.isPrototypeOf(jisu) //true
```
- 이외에도 `isPrototypeOf()` 메소드도 있다.

: 해당 객체가 파라미터로 들어가는 객체의 원형이 되는 객체인지 확인하는 메소드이다.

### prototype과 __proto__ 의 차이

자스에서 함수는 객체이다. 객체는 프로퍼티를 가진다.
생성자함수는 ‘prototype’이라는 속성으로 해당 함수의 프로토타입객체를 참조하고있고,

인스턴스는 new 연산자와 생성자 함수로 생겨나면서 `__proto__` 속성으로 프로토타입객체를 참조한다.

### 즉, 자식.__proto__ = constructor.prototype
```jsx
console.log(Person.prototype); //(1)
console.log(Person.__proto__); //(2)
```
(1) `prototype` 은 생성자함수가 선언되면서 같이 생겨나는 동일한 이름의 프로토타입 객체를 참조한다. 즉, 생성자함수만 갖고있는 프로퍼티이다.

(2) `__proto__`는 모든 객체가 가지고있고, 하나의링크와같다. 해당 객체의 원형이 참조된다.
![image](https://user-images.githubusercontent.com/62272445/166676736-63546035-3e0b-4567-a98f-0dcd1a944a1b.png)

→ 생성자함수가 prototype속성으로 참조하는 객체는 해당 생성자함수가 있게한 프로토타입 객체가 아니다. 

→ 해당 함수가 있게 한 프로토타입(부모, 원형)객체는 `__proto__`로 참조하고있다.

따라서 (Person.`__proto__`).isPrototypeOf(Person)이 true이다.

### 메소드 / 연산자 정리

- `A.isPrototypeOf(B)` : B 객체가 A객체를 원형으로 갖는지 확인
- `A instanceof B` : A가 B객체의 인스턴스인지 확인 - A B가 프로토타입 체인에 연결되어 있는지 확인.
- `Object.getPrototypeOf(A)` : A의 프로토타입 객체를 가져오는 메소드
- `A.hasOwnProperty(B)` : 해당 A객체에만 한해서 B프로퍼티를 가지고 있는지 체크한다. 프로토타입 체인에 연결된 객체를 탐색하여 찾지 않는다.
```jsx
jisu.sayHi = function() {console.log("hi, i'm Jisu")};
jisu.hasOwnProperty('sayHi'); //true;
```

### for ... in 반복문에서 객체 출력

: 프로토타입 체인이 연결되어 있는 원형의 속성, 메소드들이 모두 출력된다.

### 생성자 함수 내의 속성을 변경할 수 없도록 하기

1. 생성자 함수 내에서 const로 변수를 만든다.
2. 해당 값을 외부에서 읽을 수 있도록 전용 메소드 getProperty()를 만든다.
3. 기존 코드에서 해당 프로퍼티를 불러오는 부분은 모두 전용 메소드로 호출하도록 바꾼다.

```jsx
const Housing = function(_name, color, rooms) {
	//this.name = name;
	**const name = _name;
	this.getName = () => console.log(name);**
	this.color = color;
**}**
```

이렇게 작성하면 오직 getName으로 name 프로퍼티에 접근할 수 있다. this.name으로 객체에 프로퍼티 할당하지 않았기 때문이다.

### 자식 입양하기

__proto__ 속성을 직접 등록하여 object끼리 상속을 구현할 수 있다.

```jsx
const person = {name: "Kim"}
const jisu = {age: 20};
jisu.__proto__ = person; //자식 입양
console.log(jisu.name);// kim 부모의 name 속성을 물려받아 사용할 수 있다.thrtjddmf  anffuqkedk tkdydrksmd.
```
<br/>

## **JS에서 모든 function, object, array 등 은 최종 부모가 Object**

모든 array 자료형, 함수 자료형, object자료형의 조상은 Object()라는 constructor이다.

array나 함수자료형은 중간에 Array(), Function() 부모가 끼어있다.

때문에 자바스크립트를 가리켜 모든 것이 ‘object’로 구성되어있다고 하는 것이다.

### refer

[프로토타입: [[Prototype]], __proto__, prototype 프로퍼티](https://www.howdy-mj.me/javascript/prototype-and-proto/)

[Javascript __proto__ vs prototype 차이](https://velog.io/@h0ngwon/Javascript-proto-vs-prototype-%EC%B0%A8%EC%9D%B4)

[ES6 문법과 함께하는 모던 Javascript(자바스크립트) 고급 Part.1 - 인프런 | 강의](https://www.inflearn.com/course/%EB%AA%A8%EB%8D%98-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EA%B3%A0%EA%B8%89-1/dashboard)
