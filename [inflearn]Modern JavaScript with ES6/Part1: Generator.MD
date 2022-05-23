# PART1: Generator

## 제너레이터란 무엇인가?

조금은 이상하게(?) 동작하는 함수다.

일반 함수는 하나의 값(혹은 0개의 값)만을 반환한다. 
하지만 제너레이터는 여러 개의 값을 하나씩 반환(yield)한다.  
함수 실행 도중 특정 키워드 `yield`에서 멈췄다가, 외부에서 넘긴 값을 반환한고, 재개한다.

**외적 특징**

`*`를 붙인다. `function*` 키워드 사용.

**`function* f(…)`, `function *f(…)` 둘 다 사용 가능하지만, function에 *을 붙인 것이 선호된다.**

# 1. 동작 방식

```jsx
function* testGen() {
  yield 1;
  yield 2;
	yield 3;
  return 4;
}

const iter = testGen();
alert(iter); //[object Generator]
```

### 1) 제너레이터 객체 반환

genrator 함수를 실행하면 바로 코드가 실행되지 않고, 실행을 처리하는 특별한 객체, ‘제너레이터 객체’가 반환된다. 
제너레이터 객체는 interator 객체이며 주요 메소드는 `next()`이다.

![image](https://user-images.githubusercontent.com/62272445/169801038-05727a70-89d0-4f79-9739-d38be787adaa.png)

### 2) next() 호출 시 yield문까지 실행

`next()` 를 호출하면,  가장 가까운 `yield <value>` 문까지 실행하고 멈춘다.

멈추고 나서 산출하고자 하는 값인 `value` 가 밖으로 반환된다. 

(yield = 넘겨주다, 양도하다는 뜻이 있다)=제어권을 호출자에게 양도(=yield)한다)

value가 없으면 undefined를 반환한다.

### 3) next()가 반환하는 객체

`next()`는 항상 value, done 2개의 속성을 가진 객체를 반환한다.

- `value`: 산출 값
- `done`: 함수 코드 실행이 끝났으면 `true`, 아니라면 `false`

```jsx
console.log(iter.next()); //{value: 1, done: false}
console.log(iter.next()); //{value: 2, done: false}
console.log(iter.next()); //{value: 3, done: false}
console.log(iter.next()); //{value: undefined, done: true}
```

`next()` 메소드 호출 → `yield` 키워드까지 실행하고(stop), → 제어권 함수 외부로 양도(yield) → yield 뒤의 값이 반환.

# 2. 제너레이터와 이터러블

## 1) for … of 반복문 사용

next() 메소드를 가진 제너레이터는 이터러블 객체

⇒ `for ... of` 반복문을 사용하여 값을 얻을 수 있다.

```jsx
for (let value of generator) {
	alert(value); //1, 2, 3이 순차적으로 출력됨
}
```

이렇게 반복문을 사용하면 `.next().value`을 호출하는 것 보다 나아 보인다.
하지만, for문을 사용하면 1, 2, 3은 출력되지만,  4는 출력되지 않는다.

이유는 `for..of` 이터레이션이 `done: true`일 때 마지막 `value`를 무시하기 때문.

 `for..of`를 사용했을 때 모든 값이 출력 되길 원한다면 `yield`로 값을 반환해야 함

```jsx
function* testGen2() {
  yield 1;
  yield 2;
	yield 3;
  yield 4;
}

let iter2 = testGen2();

for (let value of iter2) {
	alert(value); //1, 2, 3, 4 차례로 출력
}
```

## 2) 전개(spread) 문법 사용

이터러블 객체이기 때문에, 전개문법 사용가능

```jsx
function* testGen3() {
  yield 1;
  yield 2;
	yield 3;
  return 4;
}

//let iter3 = testGen2();
let iter3 = [0, ...iter3()];

alert(iter3); //0, 1, 2, 3
```

spread문법을 사용하면, 제너레이터 객체를 배열 요소로 바꿔준다.


> ♾️ 제너레이터는 무한한 값을 만들 수도 있다.

값을 끊임없이 생성하는 제너레이터를 만드는 것도 가능하다.
끊임없는 값을 생성하는 제너레이터라면 당연히 `for..of` 안에 `break`나 `return`이 있어야  무한 반복문이 되어서 스크립트가 멈추는 일이 발생하지 않는다.


# 22. 'yield’를 사용해 안·밖으로 정보 교환하기

제너레이터는 값을 생성해주는 특수문법을 가진 이터러블 객체와 유사하지만, 
사실 `yield`가 양방향 통로와 같은 역할을 하며 더 강력한 기능을 제공한다.

`**yield`는 값을 제너레이터 안으로 전달하기도 한다.!**

값을 안, 밖으로 전달하려면 `generator.next(argument)`를 호출해야 한다.

이때 인수 `argument`는 `yield`의 결과가 된다.

```jsx
function* gen() {
  *// 질문을 제너레이터 밖 코드에 던지고 답을 기다립니다.
  let result = yield "2 + 2 = ?"; // (*)*alert(result);
}

let generator = gen();

let question = generator.next().value; // <-- yield는 value를 반환합니다.

generator.next(4); // --> 결과를 제너레이터 안으로 전달합니다.
```

### 조건

1. `generator.next()`를 처음 호출할 땐 인수가 없어야 한다. 인수가 넘어오더라도 무시돼야 한다. 
2. `generator.next()`를 호출 ⇒ 실행시작, 첫 번째 `yield "2+2=?"`의 결과 반환.
이 시점에는 제너레이터가 `(*)`로 표시한 줄에서 실행을 잠시 멈춥니다.
3. `yield`의 결과(”2+2=?”)가 제너레이터를 호출하는 외부 코드에 있는 변수, `question`에 할당됩니다.
4. 마지막 줄, `generator.next(4)`에서 제너레이터가 다시 시작되고 `4`는 `result`에 할당됩니다(`let result = 4`).

외부 코드에선 `next(4)`를 즉시 호출하지 않고 있다.
 제너레이터가 기다려주기 때문에 호출을 나중에 해도 문제가 되지 않는다.

## 제너레이터의 쓰임

모던 자스에서는 잘 사용하지 않지만, 실행 중에도 호출 코드와 데이터를 교환할 수 있으며, 이터러블 객체를 쉽게 만들 수 있다는 점에서 종종 유용하게 쓰이기도 한다.
비동기 제너레이터는 페이지네이션을 사용해 전송되는 비동기 데이터 스트림을 다룰 때 사용된다. 웹 프로그래밍에선 데이터 스트림을 다뤄야 하는 경우가 많은데, 이 경우에 유용하다.
