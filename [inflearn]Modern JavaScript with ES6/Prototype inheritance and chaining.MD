# Prototype 상속과 체이닝

## 프로토타입(prototype)의 기본 개념

“자스는 프로토타입 기반 언어다”
프로토타입 기반으로 확장성과 재사용성을 확장시킨 언어.
객체의 ‘원형’을 가지고 새로운 객체를 생성해가는 프로그래밍 방식.생성된 객체는 자신의 프로토타입을 갖는다. 즉, 자신이 만들어지게 된 원형을 알고 있고, 참조한다.
= 부모가 갖는 메소드, 속성들을 공유받아 사용할 수 있다.

### Prototype vs. Class

지금 Javascript는 class문법을 지원하고 있지만 원래는 프로토타입 기반 언어이며, 원래 class가 없었다. 객체의 원형(부모)인 프로토타입을 이용해 객체의 확장성, 재사용, 상속 등을 구현한 언어이다.

### Prototype과 new 연산자

프로토타입은 new연산자에 의해 생성된 객체(인스턴스)에 공유 프로퍼티, 메소드 등을 제공하기 위해 사용된다.

```jsx
const fruit = {name: 'apple'};
fruit.expiration = '20220309' //속성추가

//속성 있는지 체크하는 메소드 : hasOwnProperty();
console.log(fruit.hasOwnProperty('expiration')); // true;
```

⇒ 여기서 이상한 점, 우리는fruit 객체에 ‘hasOwnProperty()’라는 메소드를 만든 적이 없다.

어떻게 이 객체가 메소드를 사용할 수 있을까?

콘솔에 찍어보자
<br/>
![image](https://user-images.githubusercontent.com/62272445/166676295-f31280ba-bb13-4549-b099-e93388ed6a67.png)


<br/>
'__proto__ '를 통해서 연결된 프로토타입 객체를 알 수 있다.
비어있는 객체를 만들어 확인해봐도 해당 객체의 proto객체를 확인할 수 있다.

### proto에 있는 메소드를 덮어씌울 수 있다?

hasOwnProperty()를 fruit객체에서 정의해버리면 덮어 씌워질까?

```jsx
const fruit2 = {
name: 'apple', 
expiration: '20220309',
hasOwnProperty: function() {
  console.log('안녕');
  }
};

fruit2.hasOwnProperty(); //안녕
```

### javascript 함수의 내부와 객체 간의 관계

1. 함수가 만들어지고 실행될 때

내부에서 함수자신과 같은 이름의 프로토타입 객체가 생성된다.

(ex) Animal함수, Animal prototype객체

```jsx
function Animal(name) {
		this.name = name;
}

let tiger = new Animal('tiger');
let lion = new Anmail('lion');
```

⇒ new 연산자, 생성자 함수를 이용해 객체 생성시 각 객체에는 `__proto__` 속성이 자동 생성되며, 이 속성은 객체가 만들어질 수 있게 한 원형 객체(prototype)를 숨은 링크로 가리킨다.

- 이 객체가 참조하는 원형 객체 또한 prototype이 존재한다. = 최상단 Object.
![image](https://user-images.githubusercontent.com/62272445/166676353-d04e8619-0ff6-421f-b63c-efe6aa93f0a5.png)

1. 해당 함수 멤버로 prototype속성이 생성된다. 이 속성으로 함수와 같은 이름을 갖고 있는 **prototype 객체를 참조**하고 있다.
2. Animal함수에 의해 생성된 객체(**인스턴스)들이 모두 공유하는 메소드들은 prototype객체에 저장**하고, Animal 함수(생성자 함수)의 prototype속성으로 참조하여 사용한다.
ex) 모든 Animal들이 run()이라는 메소드를 갖고 있다면, Animal fucntion이아니라, prototype에 해당 메소드를 저장하고, 모든 인스턴스(동물들)이 참조하여 사용할 수 있도록한다.
    

이 때, Animal prototype 객체는 생성자 함수와 new연산자로 만들어진 모든 객체(인스턴스)의 원형이 되는 객체다.

<aside>
💡 중요 : 생성자 함수로 만들어진 인스턴스에서 prototype객체 메소드들을 모두 사용가능하다.
</aside>


### 프로토타입 객체에 멤버 추가하기

```jsx
Animal.prototype.run = function() {
	return "run run run";
}
```

많은 객체 생성시(예를 들어 1000개), 공용되는 메소드들을 모두 생성자함수에서 만들면, 메모리 부하가 생길 수 있다. 따라서 prototype 객체에 중복되는 메소드를 추가하는 것이좋다.

- lion.run() ⇒ lion에서 해당 메소드가 있는지 체크하고, 없으면 prototype객체에서 찾는다. 해당 객체에도 없다면 prototype의 prototype객체에 있는지 확인한다. 이렇게 계속해서 부모를 거슬러 올라가 참조한다.

<br/>
<br/>
## 프로토타입 정리

- 어떤 함수의 prototype 객체는 객체의 생성을 위한 부모격의 원형이 되는 객체다.
- 이 prototype 객체로 생성된 객체들은 모두 이 prototype 객체의 멤버에 접근이 가능하고, 사용할 수 있다. **= 상속**
- 새롭게 생성된 객체(자식)에서 원형(부모)의 메소드를 재정의하면 덮어써진다.
- 멤버 추가, 수정, 삭제는 prototype 속성을 통해 가능하다.
- 멤버를 읽는 것은 객체 명 또는 함수의 prototype속성을 통해 접근 가능
    
    lion.run() / lion.prototype.run();
    

### Q. 자바스크립트는 객체 지향 언어인가?

- Yes. but, 다르언어와 다르게 프로토타입 상속에 기반을 둔 OOP언어이다.

 <br/>
 <br/>
 <br/>

# 프로토타입 상속의 필요성 : “재사용”

우리 집에 망치와 드라이버가 없다. ⇒ 아빠집에 간다. 여기도 없으면 ⇒ 할아버지 댁으로 간다.

이미 부모 객체에 정의된 속성과 메소드를 그대로 물려받아서 재사용이 가능하다.

새로운 기능을 자식 객체에서 추가해서 기존 기능을 더 확장시킬 수도 있다.

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
![image](https://user-images.githubusercontent.com/62272445/166676437-8fcbe688-2902-4090-b808-8d236034f4e9.png)


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
<br/>
![image](https://user-images.githubusercontent.com/62272445/166676583-2b5670ec-e9c5-4fda-b7e3-0801b2927e63.png)


- instanceOf 연산자는 특정 객체가 프로토타입 체인에 연결되어 있는지를 체크하는 방법 중 하나이다.
- <br/>
![image](https://user-images.githubusercontent.com/62272445/166676656-db8f310b-5b7a-453b-b1c0-b235723228d0.png)

- 이외에도 `isPrototypeOf()` 메소드도 있다.

: 해당 객체가 파라미터로 들어가는 객체의 원형이 되는 객체인지 확인하는 메소드이다.

### prototype과 __proto__ 의 차이

자스에서 함수는 객체이다. 객체는 프로퍼티를 가진다.

생성자함수는 ‘prototype’이라는 속성으로 해당 함수의 프로토타입객체를 참조하고있고,

인스턴스는 new 연산자와 생성자 함수로 생겨나면서 `__proto__` 속성으로 프로토타입객체를 참조한다.
![image](https://user-images.githubusercontent.com/62272445/166676699-6ff41ac7-6b39-4a20-a5d6-f8242f75f24a.png)

### 즉, 자식.__proto__ = constructor.prototype

(1) `prototype` 은 생성자함수가 선언되면서 같이 생겨나는 동일한 이름의 프로토타입 객체를 참조한다. 즉, 생성자함수만 갖고있는 프로퍼티이다.

(2) `__proto__`는 모든 객체가 가지고있고, 하나의링크와같다. 해당 객체의 원형이 참조된다.
![image](https://user-images.githubusercontent.com/62272445/166676736-63546035-3e0b-4567-a98f-0dcd1a944a1b.png)

→ 생성자함수가 prototype속성으로 참조하는 객체는 해당 생성자함수가 있게한 프로토타입 객체가 아니다. 

→ 해당 함수가 있게 한 프로토타입(부모, 원형)객체는 __proto__로 참조하고있다.

따라서 (Person.__proto__).isPrototypeOf(Person)이 true이다.

### 메소드 / 연산자 정리

- `A.isPrototypeOf(B)` : B 객체가 A객체를 원형으로 갖는지 확인
- A instanceof B : A가 B객체의 인스턴스인지 확인 - A B가 프로토타입 체인에 연결되어 있는지 확인.
- `Object.getPrototypeOf(A)` : A의 프로토타입 객체를 가져오는 메소드
  ![image](https://user-images.githubusercontent.com/62272445/166676843-92cfbe47-f66e-4d94-ba86-c14487f4bfc2.png)

- `A.hasOwnProperty(B)` : 해당 A객체에만 한해서 B프로퍼티를 가지고 있는지 체크한다. 프로토타입 체인에 연결된 객체를 탐색하여 찾지 않는다.

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
