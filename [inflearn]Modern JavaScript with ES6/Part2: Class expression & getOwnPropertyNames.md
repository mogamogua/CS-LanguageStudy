# Part2: Class 표현식 및 생성자 함수 비교

# 1. 표현식 비교

<br/>

## 1) 무명 표현식

```jsx
const Person = class {
	constructor(name, age) {
		this.name = name;
		this.age = age;
	}
};
console.log(Person.name); //Person
```

- 무명 표현식으로 작성하면, 클래스명이 없다.

⇒ 클래스의 name 속성 값에 변수명이 오게 된다.

<br/>
<br/>


## 2) 유명 표현식named Person

```jsx
const Person = class namedPerson {
	constructor (name, age) {
		this.name = name;
		this.age = age;
		}
}
console.log(Person.name); //namedPerson
```

- 클래스명이 있기 때문에, 클래스의 name  속성 값은 클래스명인 namedPerson이 된다.


> 👩🏻‍💻 유명표현식 사용 시, 클래스명이 아닌, **변수명으로 인스턴스를 만들어야 한다!**


```jsx
const jisu = new Person('지구', '26');
```

<br/>
<br/>

# 2. 클래스와 생성자 함수 비교

## 1) 생성자 함수 이용

```jsx
function Person1(name, age) {
	this.name = name;
	this.age = age;
}

Person1.prototype.say = function(word) {console.log(word)} //메소드 추가
```

<br/>
<br/>

## 2) class 이용

```jsx
class Person2 {
	constructor (name, age) {
		this.name = name;
		this.age = age;
	}
	say(word) {console.log(word)}
}
```

- class는 기본적으로 내부에서 use strict가 자동 적용된다.
- class의 메소드는 열거 대상이 아니다. 
= 만든 class와 같은 이름의 프로토타입 객체에 추가되어있는 메소드들은 열거대상이 아니다.

```jsx
const p1 = new Person1('jisu', 26)
for (let i in p1) {
	console.log(i); //name, age, say <== 3개가 다 나온다.
}
```

생성자함수로 만들어진 Person1의 인스턴스 p1은 메소드도 열거한다.

 

```jsx
const p2 = new Person2('jigu', 206);
for (let i in p2) {
	console.log(i); //name, age
}
```

클래스로 만든 인스턴스는 속성만 열거된다.


> ❓ 그렇다면 객체가 어떻게 만들어졌는지와 관계없이 모든 특성을 보고 싶다면 ?


<br/>
<br/>

# getOwnPropertyNames() 메소드

해당 메소드는 정적 메소드이기 때문에, `Object.getOwnPropertyNames()` 이렇게 사용한다.

참고로, Object정적 메소드로 `Object.getPrototypeOf()` 도 알고 있자. 인자로 받는 객체의 프로토타입 객체를 알려준다.

위에서 보았듯이, 클래스로 만든 객체는 안의 메소드를 출력하지 않고 속성만 출력한다.

<br/>

### 다시 확인해보자. 여러 방법으로

(1) 

```jsx
for (let property in p2) {
	console.log(property); //name, age : 속성만 출력
}
```

(2) 

```jsx
Object.keys(p2); //['name', 'age'] : 속성만 출력
```

(3) 

```jsx
Object.keys(Object.getPrototypeOf(p1)); //[]
// 해당 인스턴스의 프로토타입 객체의 key를 구해도 ....안됨
```

⇒ class객체와 new 키워드로 만들어진 인스턴스는 메소드를 출력하지 않는다.

### 그럼 어떻게 할까?

```jsx
Object.getOwnPropertyNames(Object.getPrototypeOf(p2)); ['constructor', 'eat', 'run', 'rest']
```

이렇게 해당 인스턴스의 프로토타입 객체를 인자로 넣어 메소드를 구한다.

```jsx
Object.getOwnPropertyNames(cat1); //['name', 'age']
```

인스턴스를 넣으면 속성만 나온다.

<br/>

## 복습 퀴즈

### (1) 클래스 타입체크

```jsx
console.log(typeof Animal); //function
```

### (2) 속성과 메소드 추가

```jsx
Animal.prototype.age = 4;
Animal.prototype.run = function() {
	console.log(this.name + '가 뛴다')
};
cosnt lion = new Animal('liony');
lion.age = 4; //부모객체로부터 상속
```

### (3) 클래스 이름?

```jsx
console.log(Animal.name); //Animal
console.log(Animal === Animal.prototype.constructor); //true 
```

### (4) Animal 프로토타입 객체의 멤버를 모두 보고싶다면
```jsx
Object.getOwnPropertyNames(Animal.prototype)
```

