# ES6 자료구조(2) - symbol

# Symbol 타입

ES6에서 새로 도입한 원시형 중 하나. 
고유(unique)한 값으로, 변경 불가능한 불변값을 갖는다.
중요한 속성을 덮어쓰지 않도록 방지할 때 사용한다.
객체의 속성으로 사용되고 있다.(ex. array.length)

**기존 타입**
- 원시형 : Number, String, Null, Undefined, Boolean, Symbol
- 참조형 : Object(object, function, array...)

### 왜 객체의 속성으로 symbol을 사용할까?

⇒ 충돌을 피하기 위한 쉬운 방법이기 때문.
예를 들어, 배열의 길이를 구할 수 있는 length 속성은 우리가 작성한 코드에 의해 덮어 써지면 안된다. 이 때, 배열의 길이를 알려주는 length는 그대로 유지하면서 같은 이름의 length 속성을 추가하고 싶을 때 심볼을 사용하여 문제를 해결할 수 있다.

**심볼 사용하지 않을 때**

```jsx
let arr = [1,2,3,4,5]
console.log(arr.length) //5
arr.length = 50; //이렇게하면 정말로 arr 의 length속성이 덮어써져버린다,,,
console.log(arr.length)//50 
```

**심볼 사용하여 속성추가하기**

```jsx
const length = Symbol('length');
arr[length] = 50;
console.log(arr.length); //5 
console.log(arr[length]); //50
```

이렇게 함으로써 내장된 속성은 그대로 유지하면서 같은 이름의 속성을 추가할 수 있다.

## Symbol 사용법

1. new 연산자를 사용하지 않는다. Symbol() 함수 사용한다.
    
    ```jsx
    let symbol = Symbol(); //parameter에 아무것도 안넣어도 됨
    ```
    
2. 괄호 안은 비워도 되고, 문자열을 넣어 생성해도 된다. 이 문자열은 큰 의미가 없다.
단순 디버깅용 혹은 설명값이다. 고유한 값을 가지는 것에는 아무 영향도 주지 않는다.
즉, description 인자는 심볼의 고유값을 구분하지 못한다.
⇒ Symbol(’jisu’)를 두 번 호출해서 만든 각각의 심볼은 다른 심볼이다.
심볼은 심볼 함수 호출 시 새로운 심볼 값을 생성해내기 때문.
    
    ```jsx
    let jisu1 = Symbol("jisu");
    let jisu2 = Symbol("jisu");
    console.log(jisu1 === jisu2); //false.
    //동명이인을 생각하자. 이름이 같더라도 우리는 서로 다른 사람이다. 누군가 나를 다른사람과 같은 설명으로 소개한다해도..
    ```
    

### 심볼의 출력형태
심볼 값은 문자열 형태로 변환할 수가 없어서, alert를 사용할 수 없다. ⇒ console.log로 출력

```jsx
const jisu = Symbol("jisu");
undefined
console.log(jisu)
//Symbol(jisu)
```

### 심볼은 for...in구문으로 반복시 출력되지 않는다.

배열 객체 속성을 그냥 추가하면 for...in 반복문에서 출력되지만

```jsx
arr.someProperty = 10;
for (let i in arr) {
	console.log(i); //0 1 2 3 4 someProperty <=얘 왜나오니? 낄낄빠바못하는중.
}
```

⇒ 심볼 사용하여 속성 추가하면 for..in 구문에서 출력되지 않는다

⇒ 은닉성을 갖고 있다.

<aside>
💡 정리 : 배열 객체에 어떤 속성을 넣고자하고, 반복문에서 속성이 나타나지 않게 하고자 한다면? 속성을 Symbol을 이용해 추가하자.
</aside>
