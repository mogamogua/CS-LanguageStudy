ES6자료구조 (1) set

# ES6에서 새로 도입한 데이터 자료구조, Map과 Set

### map의 특징
map은 key와 value를 한 상으로 묶는다는 점에서 객체(Object)와 유사.
map의 key는 object와 달리, number를 제외한 string, function, object, function이 올 수 있다.
map은 key를 통해 value를 set, get할 수 있다.

- map과 set에 대해 잘 정리된 글 참고: [[JS #5] ES6 Map(), Set()](https://medium.com/@hongkevin/js-5-es6-map-set-2a9ebf40f96b)

### Set 특징 : 집합, key와 value
- set은 중복을 허용하지 않는다는 점을 제외하면 배열과 유사.(js의 set은 python과 똑같음)
- key와 value 쌍을 가진다.
- array는 같은 값을 여러 개 가질 수 있지만, set(집합)은 같은 값을 중복해서 가질 수 없다.
→ 그래서 중복값 제외하는 알고리즘에 사용되기도.
- 배열과 달리 인덱스를 사용할 수 없다.

<br/>
<br/>

## Set 사용법
- 생성 : new,   추가 : add,   삭제 : delete

**생성하기**

```jsx
let mySet = new Set(); //비어있는 set 하나 생성.
console.log(mySet); // objec set 객체 반환
```
![image](https://user-images.githubusercontent.com/62272445/166868182-1eb0b5b8-a28d-4878-9980-be59b2b2aebc.png)


**추가하기**

```jsx
mySet.add("A");
mySet.add("B");
mySet.add("C");
console.log(mySet);// A, B, C
```
![image](https://user-images.githubusercontent.com/62272445/166868211-0d62652c-f7cb-471d-88c1-58ffe418b307.png)


```jsx
console.log(mySet[0]); //undefined. index사용 불가.
console.log(mySet.size);// 3. length가 아닌 size속성을 사용한다.
mySet.add("A"); //이걸 몇번 실행해도 여전히 size는 3이다.
```

**삭제하기**

```jsx
mySet.delete("C");
console.log(mySet); //A, B
```

**한꺼번에 삭제하기**

```jsx
mySet.clear();
```

**생성 시 값을 추가하는 방법**

```jsx
let family = new Set().add('오빠').add('언니').add('아빠');
console.log(family); // 오빠, 언니, 아빠
```

**출력하기 : spread 연산자 사용하면 편리.**

spread 연산자를 사용하면 이터러블 객체의 요소를 하나씩 분리해 전개한다(뿌린다.)

![image](https://user-images.githubusercontent.com/62272445/166868248-8206df4d-10b5-4ed9-a603-438daebf927a.png)

```jsx
let test = 'gummy'
console.log(test); // 'g', 'u', 'm', 'm', 'y'
console.log(test); //['g','u','m','m','y']
//출력할 때 바로 사용도 가능
console.log(...'love'); //'l','o','v','e'
```

<br/>
<br/>

## Set 자료구조의 반복문

1) 전통적 for 반복문 : index를 하나씩 증가해서 순회하는 방법

2) forEach 메소드

3) set의 다양한 메소드.: keys(), values()

<br/>

### for in / for of 반복문
일단, set구조는 인덱스로 접근이 불가능하다.
**⇒ ‘for ... in’ 반복문 사용이 불가하다.**

```jsx
for (let i in family) {
	console.log(i);
}
//undefined
```

대신, ‘for ... of’ 반복문을 사용한다.

```jsx
for (let member of family) {
	console.log(memeber);
}
//오빠 언니 아빠
```

### keys() 메소드 : iterator 객체를 반환한다.
= next()메소드 사용가능

```jsx
const key_itr = family.keys();
console.log(key_itr); // >SetIterator {'오빠', '언니', '아빠'}

console.log(key_itr.next().value); //오빠
console.log(key_itr.next()); // >{value: '언니', done: false}
console.log(key_itr.next()); // >{value: '아빠', done: false}
console.log(key_itr.next()); // >**{value: undefined, done: true}
//참고로, 여기서 value는 iterator의 속성이기 때문에 메소드처럼 사용하지 않기를 주의(values() X)
//iterator가 모두 순회되면 done 속성이 true로 바뀜.**
```

### values() 메소드 : iterator 객체를 반환한다.
= next() 메소드 사용가능

```jsx
const val_itr = family.values();
console.log(val_itr.next().value); //오빠
console.log(val_itr.next().value); //언니
console.log(val_itr.next().value); //아빠
console.log(val_itr.next().value); //undefined
```

### entires() 메소드

: 각각의 요소를 쌍으로 만들어 객체로 반환한다.

```jsx
cosnt entries = family.entries();
for (let element of entries) {
	console.log(element);
} 
// ['오빠', '오빠']
// ['언니', '언니']
// ['아빠', '아빠']
```

삽입순으로 Set 요소 각각에 대해 [value, value] 배열형식으로 객체 반환.

만약 Set.set()으로 key value 모두 주었다면 [key, value] iterator 배열형식으로 반환.

<br/>
<br/>

## 추가 : Set의 집합연산

![image](https://user-images.githubusercontent.com/62272445/166868293-02c2bccd-f907-4006-93f4-7e47aae02c1f.png)

출처 : [https://www.learnbyexample.org/python-set/](https://www.learnbyexample.org/python-set/)

### union (합집합) : A union B

```jsx
let setA = new Set(['고양이', '강아지', '여우']);
let setB = new Set(['여우', '호랑이', '늑대']);

let unionSet = new Set([...setA, ...setB])
for (let value of unionSet) {
  console.log(value);
}
//'고양이', '강아지', '여우', '호랑이', '늑대'
```

### intersection (교집합) : A intersection B

```jsx
let intersectionSet = new Set(
  [...setA].filter(a => setB.has(a)) //setB에서 a요소를 갖고있는 것만 새로운 set으로 정의
);
for (let value of intersectionSet) {
  console.log(value);
}
// '여우'
```

### difference (차집합) : A - B, A subtracting B

```jsx
let differenceSet = new Set(
  [...setA].filter(a => !setB.has(a))
 //A 요소 중 B가 갖고 있지 않은 요소만 filtering해서 새로운 Set으로 정의
);

for (let value of differenceSet) {
  console.log(value);
}
```

### symmetricDifference(배타적 차집합) A XOR B
```jsx
var symmetricDifferenceSet = new Set(
[...[...set1].filter(x => !set2.has(x)), ...[...set2].filter(x => !set1.has(x))]
)
//서로 교집합에 속하지 않은 요소만 새로운 Set에 넣어 정의한다. 
for (let value of symmetricDifferenceSet) {
  console.log(value);
}
//'고양이', '강아지', '호랑이', '늑대'
```
