![HTTP완벽가이드-정리본-001](https://user-images.githubusercontent.com/62272445/169688737-65762636-0a4e-486d-92fc-91f87a7b5951.png)

# 1부 4장 : 커넥션 관리-1 TCP 커넥션과 그 과정
<br/>

> 🌰
이 장에서 배우는 것
- **HTTP는 어떻게 TCP 커넥션을 사용하지?(오늘 다룰 부분)**
- TCP 커넥션의 지연, 병목, 막힘에 대해서🧐
- 병렬 커넥션, Keep-Alive 커넥션, 커넥션 파이프라인을 활용한 HTTP의 최적화🔗
- 커넥션 관리를 위해 따라야 할 규칙들📚

<br/>

# 1. TCP 커넥션

전 세계 모오오오든 HTTP 통신은 **TCP/IP**를 통해 이루어진다.
세계 어디어세나 클라이언트 애플리케이션은 서버 애플리케이션으로 TCP/IP 커넥션을 맺을 수 있다.
커넥션이 맺어지면, 주고받는 메시지는 손상되거나 순서가 바뀌는 일 없이 **안전성을 보장**받는다.
  
⇒ 인터넷에서 데이터들이 안전하게, 제대로 보내질 수 있도록 사용한다고 생각하면 될 듯!

> ***TCP/IP :** 네트워크의 핵심 프로토콜(규약). 
인터넷에서 전송되는 정보, 파일들을 일정한 크기의 패킷으로 나누어 분산적으로 전송하고, 
도착한 패킷들이 재조립되어 원래의 정보, 파일로 전달되게 끔 하는 기능을 갖고 있다. 
****출처 : [네이버 지식백과] [TCP/IP](https://terms.naver.com/entry.naver?docId=2274898) (망중립성, 2014. 4. 15., 배진한)
 
<br/>

HTTP 개관을 정리했을 때, TCP에 대해 간략하게 정리한 내용을 다시 읽고 오자.
[5. TCP 커넥션](https://www.notion.so/5-TCP-f3b61a606cfb430c8724d2f900ecdd27) 
 
<br/>
<br/>


## 1) TCP 커넥션 과정 예시

`http://www.growto.kr:80/request.html`

**이 url을 입력받은 브라우저는… 🖥 (이거 진짜 아래 과정을 브라우저가 다 함. 부지런해라)**

(1) 호스트명을 추출한다 : `www.growto.kr` 

(2) 이 호스트명에 대한 IP 주소를 찾는다 : `202.43.78.3`

(3) 포트 번호를 얻는다 : `80`

(4) 얻은 IP주소의 포트번호로 TCP 커넥션을 생성한다.

(5) 서버로 HTTP GET 요청 메시지를 보낸다.

(6) 서버에서 온 HTTP 응답 메시지를 읽는다.

(7) 커넥션을 끊는다.
 
<br/>

### 신뢰할 수 있는 데이터 전송 통로인 TCP

사실, HTTP 커넥션은 몇몇 사용 규칙을 지켜야 한다는 점만 제외하면 거의 TCP 커넥션이나 같다.

TCP는 HTTP에 신뢰할 만한 통신 방식을 제공하며,
반대쪽으로 보내지는 바이트들은 모두 정확히 순서에 맞게 전달된다. 
TCP 커넥션은 인터넷을 안정적으로 연결해주고, 신속 정확하게 데이터를 보내주기 때문에 
TCP에 대해서 잘 알고 있어야 좋은 HTTP 애플리케이션을 개발할 수 있을 것이다😃

<br/>

## 2) 세그먼트로 나뉘고 IP패킷을 통해 전송되다📮

TCP는 **IP 패킷**(=IP 데이터그램)이라 불리는 작은 조각을 통해 데이터를 전송한다.

(IP 패킷은 공식 규정된 우편 봉투?라고 생각하면 편할 듯!)

### 작은 조각으로 전송되는 과정


> 🌰 이 모든 과정은 TCP/IP 소프트웨어에 의해 처리되며, 개발자에겐 보이지 않는다.

(1) HTTP는 메시지를 보내려고 할 때, 현재 연결된 TCP 커넥션을 통해 메시지 데이터 내용을 순서대로 보낸다.

(2) TCP는 **세그먼트**라는 단위로 ***데이터 스트림**을 잘게 나눈다.

(3) **세그먼트를** **IP 패킷**이라고 불리는 봉투에 담아 인터넷을 통해 데이터를 전달한다.

---

***데이터 스트림** : 정해진 포맷을 사용하여 문자 또는 바이트 형식으로 송수신되는 [데이터 항목](https://terms.naver.com/entry.nhn?docId=843028&ref=y)
의 연속적인 흐름

### IP 패킷에 담긴 것들 📦

- **IP 패킷 헤더**(보통 20byte) : 발신지, 목적지 IP 주소, 크기, 기타 플래그들
- **TCP 세그먼트 헤더**(보통 20byte) : TCP 포트 번호, TCP 제어 플래그, 데이터 순서와 무결성을 검사하기 위해 사용되는 숫자 값
- **TCP 데이터 조각** (0 이상의 byte)
    

![HTTP완벽가이드-정리본-002](https://user-images.githubusercontent.com/62272445/169688352-66a35af7-a4c0-4c84-b708-e8e958a574a1.png)
위 그림은 너무나도 자세한 부분이라, , 패킷과 세그먼트를 구분해서 이해하면 되지않을까,,

 
<br/> 
<br/>

## 3) TCP 커넥션 유지하기🔗

컴퓨터는 항상 TCP 커넥션을 여러 개 갖고 있고, TCP는 포트 번호로 여러 개의 커넥션을 유지한다. 

### 포트 번호

포트번호는 회사의 내선 전화를 생각하면 이해가 쉽다.

회사 대표 전화번호로 전화하면 회사를 대표할 수 있는 안내데스크, 상담/문의로 연결되고,
내선 전화로 전화하면(포트번호까지 연결하면) 해당 번호에 맞는 직원에게 연결된다.

IP 주소는 : 해당하는 컴퓨터에 연결되고, 
PORT 번호는 : 해당하는 애플리케이션으로 연결된다.

### TCP 커넥션 주소 구성 요소

`<발신지 IP 주소, 발신지 포트, 수신지 IP주소, 수신지 포트>` 

 → 이 네가지 값으로 **유일한 커넥션**을 생성한다.

어떤 커넥션들은 같은 목적지 포트 번호를 가리킬 수도 있고, 
같은 발신지 IP 주소를 가리킬 수도 있다. 
하지만 **네 가지 주소 구성 요소의 값이 모두 같을 수 없다**.(일부는 같을 수 있다)

<br/> 
<br/>

## 4) TCP 소켓 프로그래밍

운영체제는 TCP 커넥션의 생성과 관련된 여러 기능을 제공한다. 

### 소켓 API

- 소켓 API는 처음에 유닉스용으로 개발되었고, 현재는 대부분의 운영체제, 언어에서 사용 가능하다.
- 소켓 API는 개발자에게 TCP, IP의 세부 사항들을 숨긴다.
- 소켓 API를 사용하면 , TCP endpoint(종단) 데이터 구조를 생성하고, 원격 서버 TCP 종단에 그 종단 데이터 구조를 연결하여 데이터 스트림을 읽고 쓸 수 있다.
 
<br/>

### 클라이언트 - 서버가 TCP 소켓 인터페이스로 HTTP 트랜잭션하는 과정

여기서 트랜잭션이란 서로 메시지를 주고받아 원하는 데이터를 주거나 받는 것

> 👩🏻‍💻 `code` 로 표시한 부분은 TCP 커넥션을 위해 쓰이는 소켓 API 함수입니당.
트랜잭션하는 과정의 흐름을 중심으로 알아보아요.


**(1) 웹 서버**

1. 새 소켓을 만든다 : `s = socket(<parameter>)`
2. 80포트로 소켓을 묶는다. : `bind(s, <local IP:port>)`
3. 소켓 커넥션을 허가한다 : `listen(s, ...)`
4. 커넥션을 기다린다. : `s2 = accept(s)`

**(2) 클라이언트** 

1. URL에서 IP, PORT번호를 알아낸다.
2. 새로운 소켓을 생성한다 : `s = socket(<parameter>)`
3. 서버의 IP:포트로 연결하여 TCP 커넥션을 생성한다. : `connect(s, <remote IP:port>)` 
- 이 때 커넥션 생성 시간은 서버와의 거리, 서버의 부하, 인터넷 혼잡도에 따라서 시간이 달라진다.

**(3) 서버**

- 애플리케이션 커넥션 통지

**(4) 클라이언트**

1. 커넥션 성공적으로 연결됨
2. 커넥션이 되면, HTTP 요청을 보낸다.

**(5) 서버**

1. HTTP 요청을 읽는다 : `n = read(s, buffer, n)`
2. 요청을 분석하여, 수행한 후 데이터를 클라이언트에게 보낸다. `n = write(s, buffer, n)`

**(6) 클라이언트**

1. 응답을 기다린다.
2. HTTP 응답 메시지를 받으면, 응답 데이터를 처리한다.
3. 커넥션을 닫는다. : `close(s)`

**(7) 서버** 

- 커넥션을 닫는다 : `close(s)`

 
<br/>

> 🌰 다음엔 TCP 성능에 대해 알아보겠습니당