# 1부 3장 : HTTP 메시지-2

메시지의 각 부분들에 대해 자세히 알아보자.

이전 편을 못 봤다면!

[1부 3장 : HTTP 메시지](https://www.notion.so/1-3-HTTP-57dbe3d95f264c4e923fbe2a2f3c20bd)

# 1. 메시지의 구성

모든 HTTP 메시지는 무엇을 해야 하는지, 무슨 일이 일어났는지를 알려주는 시작줄로 시작한다.

## 1) 시작줄

### 요청줄 = 요청메시지의 시작줄

서버에게 리소스에 대해 무언가를 해달라고 요청한다.

(1) 서버에서 어떤 동작이 일어나야 하는지를 설명해주는 **‘메소드’**,

(2) 그 동작에 대한 대상을 지칭하는 **‘요청 URL’**, 

(3) 클라이언트가 어떤 HTTP 버전으로 말하고 있는지 알려주는 **‘HTTP 버전’**을 포함한다.

모든 필드는 공백으로 구분된다.

### 응답줄 = 응답 메시지의 시작줄

수행 결과에 대한 상태 정보와 결과 데이터를 클라이언트에게 돌려준다.

(1) 응답 메시지에서 쓰인 **HTTP 버전**

(2) 수행 상태를 알려주는 숫자로 된 **상태코드**

(3) 수행 상태에 대해 설명하는 텍스트인 **사유구절**

모든 필드는 공백으로 구분된다. 

### 메소드

요청의 시작줄은 메소드로 시작하며, 서버에게 ‘무엇을 해야하는지’를 알려준다.

모든 서버가 http 명세에 나와있는 메소드를 모두 구현 해놓지는 않는다.

한편, http는 쉽게 확장될 수 있도록 설계되어 커스텀한 확장 메소드를 사용할 수 있다.

  

| 메소드 | 설명 | 메시지 본문 유무 |
| --- | --- | --- |
| GET | 서버에서 어떤 문서를 가져온다. | 없음 |
| HEAD | 서버에서 어떤 문서에 대해 ‘헤더만 가져온다’ → GET과 비슷 | 없음 |
| POST | 서버가 처리해야 할 데이터를 보낸다.  | 있음 |
| PUT | 서버에 요청 메시지의 본문을 저장한다. | 있음 |
| TRACE | 메시지가 프락시를 거쳐 서버에 도달하는 과정을 추적한다. | 없음 |
| OPTIONS | 서버가 어떤 메서드를 수행할 수 있는지 확인한다. | 없음 |
| DELETE | 서버에서 문서를 제거한다. | 없음. |

### 상태코드

클라이언트에게 무슨 일이 발생했는지 알려준다 - 성공 / 실패 등

`HTTP/1.0 200 OK` 에서 200이 상태 코드. 세 자리 숫자로, 백 단위 기준으로 다음과 같이 범위가 나뉨.  

| 전체 범위 | 정의된 범위 | 분류 |
| --- | --- | --- |
| 100-199 | 100-101 | 정보 |
| 200-299 | 200-206 | 성공 |
| 300-399 | 300-305 | 리다이렉션 |
| 400-499 | 400-415 | 클라이언트 에러 |
| 500-599 | 500-505 | 서버 에러 |

범위로 보면 100개의 상태 코드를 정의할 수 있지만, 현재 그보다 적은 수의 상태 코드만이 정의되어있다. 프로토콜이 진화하면서 더 많은 상태 코드가 HTTP 명세에 공식적으로 정의될 것이다.
또한, 누군가 현재 프로토콜의 확장으로 새로운 상태 코드를 정의해서 사용할 수도 있다. 
→ 만약 HTTP 명세에 정의되지 않은 상태 코드를 받았다면(EX 515) 확장 상태코드이고, 범위에 따라 분류에 맞게 처리하면 된다. 500대 에러면 서버에러로 처리하면 된다.

### 사유 구절 (reason phrase)

사유 구절과 일대일로 대응되어, 상태코드에 대해 이해하기 쉽도록 넘겨주는 문구이다.
HTTP 명세에는 사유 구절에 대한 규칙이 제공되어 있지 않다. 

### 버전 번호

`HTTP/x.y` 형식으로 자신이 따르는 프로토콜의 버전을 상대에게 말해주기 위한 수단이다.
요청, 응답 메시지 모두에 기술된다.
애플리케이션들은 대화 상대의 능력과 메시지의 형식에 대한 단서를 버전 번호로 알 수 있다.

ex) http 1.1 애플리케이션과 대화하는 http 1.2버전 애플리케이션은 1.2 버전의 기능을 못 사용한다고 가정한다.
응답의 프로토콜 버전이 HTTP/x.y라는 것은 더 정확히 말하면, 해당 응답을 보낸 애플리케이션이 HTTP/x.y까지 이해할 수 있다는 것이다.

버전 번호의 점을 소수점으로 이해하면 안된다. ⇒ 2.22버전이 2.3보다 크다. 2, 22를 따로 비교해야한다.

<br/>
<br/>

## 2) 헤더

시작줄 다음에는 여러 개, 혹은 0개, 1개의 http헤더가 올 수 있다. 
`:`로 구분되어 이름/값 쌍의 목록으로 되어있고, 메시지에 대한 추가 정보를 알려준다.

애플리케이션은 자유롭게 자신만의 확장 헤더를 만들어낼 수 있다. 

이름, 쉼표, 공백(선택적), 필드 값, CRLF가 순서대로 온다.

### 헤더 분류

| 일반 헤더 | 요청과 응답 양쪽 모두에 나타날 수 있다. | ex) Date |
| --- | --- | --- |
| 요청 헤더 | 요청에 대한 부가 정보를 제공한다. | ex) Accept |
| 응답 헤더 | 응답에 대한 부가 정보를 제공한다. | ex) Age |
| entity 헤더 | 본문 크기, 콘텐츠, 리소스 자체를 서술한다. | ex) Content-length |
| 확장 헤더 | 명세에 정의되지 않은 새로운 헤더 |  |

### 흔히 쓰이는 헤더

| Date: Tue, 3, Oct 1997 16:03 GMT | 서버가 응답을 만들어 낸 시각 |
| --- | --- |
| Content-length: 15000 | 15000바이트의 데이터를 포함한 엔터티 본문을 싣고 있다. |
| Content-type: image/gif | 엔터티 본문은 gif이미지이다. |
| Accept: image/gif, image/jpeg, text/html | 클라이언트는 gif, jpeg이미지와 html을 받아들일 수 있다. |

### 헤더 여러 줄로 나누기

긴 헤더는 여러 줄로 쪼개어서 더 보기 좋게 만들 수도 있다. (추가 줄 앞에는 최소 하나의 스페이스나 탭 문자가 와야한다.)

ex)

```jsx
Server: Test Server
	Version 1.0
```

<br/>
<br/>
## 3) 엔터티 본문

엔터티 본문은 http의 화물이라고 할 수 있다. 
이미지, 비디오, html문서, 애플리케이션, 신용카드 트랜잭션, 전자우편 등 여러 종류의 디지털 데이터들을 실어 나를 수 있다.

## *0.9 버전의 메시지

0.9 버전은 http 프로토콜의 초기버전이기 때문에 훨씬 단순하다. 
요청과 응답으로 이루어지지만, 요청 메시지는 그저 메서드와 요청 url만을 갖고 있고, 응답은 오직 엔터티로만 이루어져있다. 

이런 단순함 때문에 다양한 상황에 대응할 수 없고, 이 책에서 설명하는 기능, 애플리케이션 또한 구현할 수 없다. 하지만 HTTP/0.9의 제약에 대해 아는 것도 좋을 것이다.

—여기서부터 HTTP메시지 구성요소에 대해서 더 자세히 알아봅니다—
<br/>
<br/>
<br/>

# 2. 메소드

---

## 1) 안전한 메소드(Safe Method)

HTTP는 안전한 메소드라 불리는 집합으로 GET, HEAD를 정의한다.
⇒ 해당 HTTP 요청의 결과로 서버에 어떤 작용도 없고, 변화가 나타나지 않는다. : **멱등성**

서버에 어떤 ‘작용’이 일어나는 ‘안전하지 않은 메소드’의 예) 회원가입

<br/>

## 2) GET

가장 흔히 쓰인다. 주로 서버에게 어떤 리소스를 달라고 요청하기 위해 사용한다.
HTTP/1.1 준수를 위해 서버는 이 메소드를 구현해야 한다.

<br/>

## 3) HEAD

정확히 GET처럼 행동하지만, 응답으로 ‘헤더’만 받고, 엔터티 본문은 받지 않는다.
클라이언트가 헤더만 조사하여 다음과 같이 일부 정보를 알아내고자 할 때 사용한다.

- 리소스를 가져오지 않고 해당 리소스에 대한 정보를 알고자 할 때
- 응답의 상태코드만 확인해서 개체가 존재하는지 확인하고자 할 때
- 리소스가 변경되었는지 검사하고자 할 때

서버 개발자는 HEAD 메소드로 반환되는 헤더가 GET 요청시 반환되는 헤더와 일치하도록 해야한다.

HTTP/1.1 준수를 위해서 HEAD 메소드 또한 반드시 구현되어야 한다.

### HEAD 메소드 동작 예시

```jsx
HEAD /result/382.html HTTP/1.1
Host: www.joes-hardware.com
Accept: *
```

```jsx
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 617
```

<br/>

## 3) PUT

서버가 요청의 본문을 가지고 요청URL 이름대로 새 문서를 만들거나, 이미 URL이 존재한다면 본문을 사용해서 교체한다. 즉, 수정하거나 새로 만든다.
콘텐츠를 변경할 수 있게 해주기 때문에, 많은 웹 서버는 PUT 수행 전 사용자 비밀번호를 요구한다. 

<br/>

## 4) POST

POST 메소드는 입력된 데이터를 서버에 전송하기 위해 설계되었다.

실제로, HTML form을 지원하기 위해 흔히 사용되며, 채워진 form에 담긴 데이터는 서버로 전송되어 필요로 하는 것에 보낸다.

<br/>

## 5)  TRACE

TRACE 요청을 받은 서버는 **자신이 받은 요청 메시지를 본문에 넣어 응답으로 돌려준다.** 

이처럼 클라이언트의 요청이 서버에 도달했을 때 어떻게 보이게 되는지 알려주어, 주로 진단을 할 때 사용된다. 
→ 어떤 진단?

클라이언트의 요청은 방화벽, 프락시, 게이트웨이 등의 애플리케이션을 통과될 수 있는데, 이들은 HTTP요청을 수정할 수 있는 기회가 있다.

TRACE 요청 요청이 의도한 요청/응답 연쇄를 거쳐가는지, 거쳐가는 프락시나 다른 애플리케이션들이 어떤 영향을 미치는지 진단할 때 사용된다.
하지만 HTTP 메서드에 따라 애플리케이션들이 다르게 동작하기 때문에 이 한계점도 알고 있어야 한다. 예를 들어,  프락시는 POST 요청을 바로 서버로 통과시키지만, GET요청은 웹 캐시같은 다른 애플리케이션으로 전송한다. 문제는, TRACE는 메소드를 구별하여 진단하는 매커니즘을 제공하진 않는다는 것.

### 예시

요청

```jsx
TRACE /product_list.txt HTTP/1.1
Accept: *
Host: www.joes-hardware.com
```

프락시를 거친 요청 = 서버에게 도착한 요청 ⇒ 응답 메시지 본문에 들어가는 내용

```jsx
TRACE /product_list.txt HTTP/1.1
Accept: *
Host: www.joes-hardware.com
Via: 1.1 proxy3.company.com
```

서버가 보낸 응답 메시지 ⇒ 프락시

```jsx
HTTP/1.1 200 OK
Content-type: text/plain
Content-length: 98

TRACE /product_list.txt HTTP/1.1
Accept: *
Host: www.joes-hardware.com
Via: 1.1 proxy3.company.com
```

프락시를 거친 응답메시지

```jsx
HTTP/1.1 200 OK
Content-type: text/plain
Content-length: 98
Via: 1.1 proxy3.company.com

TRACE /product_list.txt HTTP/1.1
Accept: *
Host: www.joes-hardware.com
Via: 1.1 proxy3.company.com
```

<br/>

## 6) OPTIONS

OPTIONS 메소드로 웹 서버에게 여러가지 종류의 지원 범위에 대해 물어볼 수 있다. 
특정 리소스에 대해 어떤 메소드가 지원되는지 알고자 할 때 사용할 수 있다.

### 예시

요청

```jsx
OPTIONS * HTTP/1.1
Host: www.growto.com
Accept: *
```

응답

```jsx
HTTP/1.1 200 OK
Allow: GET, POST, PUT, OPTIONS
Context-length: 0
```

<br/>

## 7) DELETE

서버에게 요청 URL로 지정한 리소스를 삭제하길 요청한다.
하지만 클라이언트는 삭제가 수행되는 것을 보장하지 못한다. - 서버가 클라에게 알리지 않고 요청을 무시할 수도 있기 때문.
<br/>
## 8) 확장 메소드

HTTP는 필요에 따라 확장 메소드를 만들어도 문제가 없도록 설계되었다. 

→ 리소스를 관리하는 서버의 능력을 확장할 수 있다.

### 대표적인 확장 메소드 몇 가지

| 메소드  | 설명 |
| --- | --- |
| LOCK | 사용자가 리소스를 잠글 수 있게 해준다. 
ex) 문서를 편집하는 동안 다르사람이 동시에 같은 문서를 편집하지 못하도록 잠글 수 있다. |
| MKCOL | 사용자가 문서를 생성할 수 있게 해준다. |
| COPY | 서버에 있는 리소스를 복사한다. |
| MOVE | 서버에 있는 리소스를 옮긴다. |

모든 확장 메소드는 형식을 갖춘 명세로 정의되어있진 않다.

만약 내가 어떤 확장 메소드를 정의하고 싶다면, 대부분의 http 애플리케이션은 이를 이해할 수 없다는 것을 알고 있어야하며, 반대로, 내 http 애플리케이션도 이해할 수 없는 확장 메소드를 사용하는 애플리케이션을 마주할 수 있다는 것을 알고 있어야 한다.

<br/>

<aside>
💡 이어서 상태코드와 사유구절, 헤더에 대해 자세히 다뤄보겠습니다..

</aside>