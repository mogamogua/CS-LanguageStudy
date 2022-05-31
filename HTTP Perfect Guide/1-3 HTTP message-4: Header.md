# 1부 3장: HTTP 메시지-4: 헤더

# 헤더(header)

---

헤더, 메소드는 클라이언트(이하 클라.)와 서버가 무엇을 하는지 결정하기 위해 함께 사용된다.

헤더는 크게 다섯 가지로 분류된다. 간단하게 설명하자면...

### 일반 헤더(General Headers)

클라, 서버, 메시지를 보내는 여러 애플리케이션을 위해 다양한 목적으로 사용됨.
ex) Date 헤더 : 메시지가 만들어진 일시 지칭하는 일반 목적 헤더.

`Date: Sum, 11 May 1997 09:48 GMT`

### 요청 헤더(Request Headers)

요청 메시지를 위한 헤더. 서버에게 클라이언트가 받고자 하는 데이터에 대한 부가 정보를 제공한다.
ex) Accept 헤더 : 클라이언트가 응답으로 받을 수 있는 미디어 타입

`Accept: * / *` (모두 받을 수 있음) `Accept: application/json` json객체만 받음

### 응답 헤더(Response Headers)

클라이언트에게 정보를 제공하기 위한 헤더.
ex) Server 헤더 : 어떤 종류의 서버와 대화하고 있는지 알려줌.

`Server: Tiki-Hut/1.0` - 클라이언트에게 Tiki-Hut 서버 1.0버전과 대화하고 있다고 알려줌

### 엔터티 헤더(Entity Headers)

말 그대로 엔터티 본문에 대한 헤더. 
ex) Content-Type 헤더 : 엔터티 본문에 있는 데이터의 타입이 무엇인지 알려줌

`Content-Type: text/html; chrset=iso-latin-1` : 애플리케이션에게 데이터가 iso-latin-1 문자집합으로 구성된 html문서임을 알려줌

### 확장 헤더(Extension Headers)

개발자들에 의해 만들어졌지만, HTTP 명세에는 추가되지 않은 비표준헤더.
HTTP 프로그램을 개발한다면, 확장 헤더들에 대해 모른다 하더라도 해당 헤더들을 용인하고 전달할 수 있도록 하는 것이 좋다.

<aside>
💡 여기서부터 각 종류의 헤더들을 깊게 알아보자!

</aside>

## 1. 일반 헤더

---

메시지 종류와 상관없이 메시지에 대한 아주 기본적인 정보 제공. 

- Connection  : 클라, 서버가 요청/응답 연결에 대한 옵션을 정할 수 있게 해준다.
`Connection: close`
현재 HTTP 메시지 직후에 TCP 접속을 끊는다는 것을 알린다.
`Connection: Keep-Alive`
현재 TCP 커넥션을 유지한다.
- Date : 메시지가 언제 만들어졌는지 날짜, 시간을 제공
- MIME-Version : 발송자가 사용한 MIME의 버전을 알려준다.
- Trailer chunked transfer : 인코딩으로 인코딩된 메시지의 끝 부분에 위치한 헤더들의 목록을 나열.
- Transfer-Encoding : 안전한 전송을 위해 메시지에 어떤 인코딩이 적용되었는지 말해줌
- Upgrade : 발송자가 ‘업그레이드’하길 원하는 새 버전이나 프로토콜을 알려줌
- Via : 이 메시지가 어떤 중개자(프락시, 게이트웨이)를 거쳐 왔는지 보여줌

### 일반 캐시 헤더

HTTP/1.0 부터 캐시할 수 있도록 해주는 최초의 헤더 도입. - 원 서버로부터 객체를 가져오는 대신 로컬 복사본으로 캐시 가능해짐. 

Cache-Control : 메시지와 함께 캐시 지시자를 전달하기 위해 사용.

Pragma : 메시지와 함께 지시자를 전달하는 또 다른 방법. 엄밀히 말하면 요청 헤더이다. Cache-Control헤더로 대체 사용되면서 사용되지 않을 헤더임.

## 요청 헤더

- Client-IP: 클라이언트가 실행된 컴퓨터의 IP를 제공
- From : 클라이언트 사용자의 메일 주소를 제공
- Host: 어디에 요청하는지, 요청의 대상이 되는 서버의 호스트 명과 포트를 알려준다. ex 도메인네임. Host : www.growto.kr
- Referer: 현재의 요청 URI가 들어있었던 문서의 URL을 제공
- UA-Color: 클라이언트 기기 디스플레이의 색상 능력에 대한 정보 제공
- UA-CPU: 클라이언트 CUP종류나 제조사 정보
- UA-Disp: 클라이언트 디스플레이(화면) 능력에 대한 정보
- UA-OS : 클라이언트 기기에서 동작중인 운영체제 이름과 버전
- UA-Pixels : 클라이언트 기기 디스플레이에 대한 픽셀 정보
- User-Agent: 요청을 보낸 애플리케이션의 이름을 서버에게 알려줌.

### Accept 관련 헤더

클라는 Accept관련 헤더로 자신의 선호, 능력을 알려줄 수 있음.(무엇을 원하고, 원하지 않는지)

⇒ 서버는 클라이언트가 받을 수 없는 것을 전송하는데 시간을 낭비할 일이 없어진다.

- Accept : 서버가 보내도 되는 종류에 대한 정보
- Accept-Charset: 서버가 보내도 되는 문자 집합 정보
- Accept-Encoding: 서버가 보내도 되는 인코딩
- Accept-Language: 서버가 보내도 되는 언어
- TE : 서버가 보내도 되는 확장 전송 코딩

### 조건부 요청 헤더

클라는 서버에게 요청에 응답하기 전, 먼저 조건이 참인지 확인하게 하고, 참일 때만 적절한 응답을 요구할 수 있다.

- Expect : 클라이언트가 요청에 필요한 서버의 행동을 열거
`Expect: 100-continue` :서버가 100 continue 응답을 보내주길 예상
- if-Match : 문서의 엔터티 태그가 주어진 엔터티 태그와 일치하는 경우에만 문서를 가져온다.
- if-None-Match : 문서의 엔터티 태그가 주어진 엔터티 태그와 일치하지 않는 경우에만 문서를 가져온다.
- if-Modified-Since : 주어진 날짜 이후 리소스가 변경되지 않았다면 요청을 제한함
- if-Unmodified-Since : 주어진 날짜 이후 리소스가 변경되었다면 요청을 제한함.
- if-Range: 문서 특정 범위에 대한 요청을 한다.
- Range : 서버가 범위 요청을 지원핟나면, 리소스에 대한 특정 범위를 요청

### 요청 보안 헤더

HTTP는 자체적으로 요청을 위한 간단한 인증요구/응답체계를 갖고 있다. 
리소스에 접근하기 전에 자신을 인증하게 하여 트랜잭션을 더 안전하게 한다.

- Authorization: 클라이언트가 서버에게 제공하는 인증에 대한 정보를 담음
- Cookie : 클라가 서버에게 토큰을 전달할 때 사용. 
진짜 보안헤더는 아니지만 보안에 영향을 줄 수 있다.
- Cookie2 : 요청자가 지원하는 쿠키의 버전을 알려줄 때 사용.

### 프락시 요청 헤더

인터넷에서 프락시가 흔해지면서 프락시 기능을 돕기 위한 헤더들이 정의되었다.

- Max-Forwards: 요청이 서버로 향하는 과정에서 , 다르 프락시나 게이트웨이로 전달될 수 있는 최대 횟수. TRAC E메소드와 함께 사용됨.
- Proxy-Authorization: Authorization과 같은데, 프락시 인증에서 쓰임
- Proxy-Connection: Connection과 같으며, 프락시에서 연결을 맺을 때 쓰임

## 2. 응답 헤더

---

- Age: 응답이 얼마나 오래되었는지
- Public : 서버가 특정 리소스에 대해 어떤 메소드를 지원하는지 알려줌(목록으로)
- Retry-After: 현재 리소스가 사용 불가능한 상태이며, 언제 가능해지는지 날짜나 시각을 알려줌
- Server : 서버 애플리케이션의 이름, 버전 정보
- Title : HTML문서에서 주어진 것과 같은 제목
- Warning: 사유 구절에 있는 것보다 더 자세한 경고 메시지를 보내줌.

### 협상 헤더

예를 들어, 한국어와 일본어, 영어로 번역된 HTML 문서가 있을 때 선택지가 3개가 존재한다. 
이 때 클라이언트와 서버가 협상을 통해 적절한 리소스를 보낼 수 있도록 협상 헤더를 사용할 수 있다.

- Accept-Range : 서버가 자원에 대해 받아들일 수 있는 범위의 형태
- Vary : 서버가 클라이언트에게 보 내줄 리소스의 가장 적절한 버전을 선택하기 위해 살펴보아야 하는 헤더들의 목록.

### 응답 보안 헤더

인증 요구 헤더들

- Proxy-Authenticate : 프락시에서 클라이언트로 보낸 인증 요구의 목록
- Set-Cookie: 클라이언트를 인증할 수 있도록 클라 측에 토큰을 설정하기 위해 사용함.(쿠키에 토큰을 저장해놓고 클라가 요청을 볼 때마다 쿠키에 있는 토큰을 같이 보내도록한다. 이럼으로써 어떤 사용자인지 인증, 구별할 수 있다.)
진짜 보안 헤더는 아니지만, 보안에 영향을 줄 수 있음.
- Set-Cookie2: Set-Cookie와 비슷하게 정의된 쿠키. 확장 헤더임.
- WWW-Authenticate: 서버 → 클라이언트로 보낸 인증 요구 목록.

## 3. 엔터티 헤더

요청, 응답 양쪽 모두 엔터티를 포함할 수 있기 때문에 양 타입 메시지 모두에 나타날 수 있다.

- Allow: 이 엔터티에 대해 수행될 수 있는 요청 메소드들을 나열.
- Location: 엔터티가 실제로 어디에 위치하고 있는지 알려줌. 수신자에게 리소스에 대한 위치(url)를 알려줄 때 사용.

### 콘텐츠 헤더

콘텐츠에 대한 구체적 정보 제공.(종류, 크기, 등)

- Content-Base : 본문에서 사용된 상대 URL을 계산하기 위한 기저 URL
- Content-Encoding: 본문에 적용된 인코딩
- Content-Language : 본문을 이해할 적절한 자연어
- Content-Length : 본문 길이나 크기
- Content-Location: 리소스의 실제 위치
- Content-MD5 : 본문의 MD5 체크섬(checksum)
- Content-Range : 전체 리소스 중 이 엔터티가 해당하는 범위를 바이트 단위로 표현
- Content-Type : 이 본문이 어떤 종류 객체인가 ⇒ 브라우저는 이 헤더를 보고 이 객체를 어떻게 보여줄지 결정할 수 있다.

### 엔터티 캐싱 헤더

언제 어떻게 캐시가 되어야 하는지에 대한 지시자 제공

캐시된 사본이 아직 유효핝, 더이상 유효하지 않게 되는 시점이 언제인지 추정할 수 있는 단서 제공

- ETag : 이 엔터티에 대한 엔터티 태그
- Expires : 이 엔터티가 더이상 유효하지 않아 원본을 다시 받아와야 하는 일시
- Last-Modified: 가장 최근 이 엔터티가 변경된 일시.