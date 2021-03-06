<h2>그림으로 배우는 HTTP&Network Basic(우에노 센 저, 이병억 번, 영진닷컴 발행) 책 정리</h2>

## TCP/IP의 계층

* 전송시에는 위에서부터 아래로, 수신시에는 아래부터 위로

애플리케이션 - 통신의 움직임 결정(FTP, DNS, HTTP 등). GET, POST, PUT, DELETE, TRACE 등

트랜스포트 - 애플리케이션 계층에 접속되있는 두 컴퓨터 사이의 데이터 흐름을 제공(TCP, UDP).
위 계층에서 내려온 HTTP 데이터를 조각내어 번호와 포트를 붙임

네트워크(인터넷) - 패킷의 이동을 다룸. 어떤 경로(절차)를 거쳐 상대의 컴퓨터까지 패킷을 보낼지 결정
수신지 MAC 주소를 추가하여 전송준비 완료

링크(데이터링크, 네트워크 인터페이스) - 하드웨어를 다룸. 케이블이나 device, NIC 모두를 포함

* 각 계층을 거칠 때는 헤더로 불리는 정보를 추가. 수신시에는 반대로 계층마다 사용한 헤더를 하나씩 삭제
* 캡슐화라고 부름

## IP/TCP/DNS

IP(Internet Protocol) - 네트워크층. 개개의 패킷을 상대방에게 전달
ARP - IP 주소로 MAC 주소를 추출
TCP - 트랜스포트층. 용량이 큰 데이터를 TCP segment라 불리는 단위 패킷으로 분해하여 신뢰성 있는 서비스를 제공.
### TCP는 3handshaking을 사용. SYN을 전송, SYN/ACK로 SYN을 받았다는 메시지와 ACK를 받을 준비가 되었다는 사실을 알리면 ACK를 보낸다
DNS - ip주소로 사람이 사용하기 쉬운 언어로 바꿔주거나 반대되는 역할을 수행

## URI/URL
URI는 리소스 식별자
URL은 네트워크상의 위치. URI의 서브셋

### URL 포맷
http://user:pass@www.example.kr:80/dir/index.htm?udi=1#ch1
* 스키마 http://
* 자격정보 user:pass(옵션)
* 서버주소 www.example.kr(IP주소로 대체가능)
* 서버포트 :80(생략하면 디폴트값)
* 계층적 파일 path /dir/index.htm
* 쿼리문자열 ?uid=1(요청에 포함시켜 서버에 임의의 파라미터를 넘겨줌)
* 프래그먼트식별자 #ch1(취득한 리소스에서 서브 리소스를 가리키기 위해 사용)


## HTTP의 특징
* 상태를 유지하지 않는다(stateless)
* 리퀘스트 URI로 리소스를 식별
* 서버에 메서드로 임무를 부여한다(GET, POST, PUT, DELETE, TRACE, HEAD, OPTIONS, CONNECT)
* 매 요청마다 TCP를 종료하지 않고 지속적으로 연결을 유지해둔다
* 파이프라인화되어 response가 완료되지 않아도 request를 보낼 수 있게 해준다
* state를 쿠키를 활용하여 관리한다

### HTTP의 상태코드
* 상태코드로 http는 리퀘스트 결과를 전달한다

1xx : 리퀘스트를 받아들여 처리<br>
2xx : 정상처리<br>
200 완료<br>
204 돌려줄 리소스가 없음<br>
206 range에 의해 범위가 지정된 리퀘스트에 의해 서버가 부분 리퀘스트를 받았음<br>
3xx : 완료를 위해서는 추가동작이 필요<br>
301 다른 URI를 사용<br>
302 일시적 이동된 URI를 안내<br>
303 리소스가 다른 URI에 있으므로 GET을 다시 해야함. 302와 비슷하지만 302는 옮겨진 것을 돌려주지만 303은 다시 GET을 해야함을 명시<br>
304 리소스에 대한 access는 허락하지만 조건이 충족되지 않음. responsebody에 아무것도 없어야함<br>
307 302와 같지만 URI로 바꿔주진 않음<br>
4xx : 리퀘스트 이해 불능<br>
400 리퀘스트 구문이 잘못되었음. 브라우저는 200과 같이 취급<br>
401 인증이 필요함. 인증을 한 경우엔 인증에 실패. challenge를 포함한 www-authenticate 헤더 필드가 필요<br>
403 액세스 금지. 권한 문제나 퍼미션이 아예 없는 경우<br>
404 해당 리소스는 서버에 없음<br>
5xx : 서버에서 리퀘스트 처리 실패<br>
500 리퀘스트를 처리중 에러가 발생<br>
503 서버 과부하로 현재 리퀘스트 처리 불가. Retry-After 헤더 필드로 클라이언트에 response하는 것이 바람직<br>

### 멀티 도메인을 지원하는 가상호스트

물리적으로는 1대지만 여러대의 서버를 돌리는거 같은 효과
req를 보내는 쪽에서 호스트명과 도메인 명을 완전히 포함된 URI를 작성하거나, Host 헤더 필드에 지정이 필요

## 프록시, 게이트웨이, 터널
* 통신(req, res)을 중계

프록시 : 서버와 클라이언트 역할을 하는 중계프로그램. 여러대의 프록시를 경유하는 경우도 가능. 중계시 Via 헤더 필드에 경유정보를 추가해야 함
캐시를 사용해 네트워크 대역을 효율적으로 사용하거나 액세스 제한 등을 지키려는 목적으로 사용
캐시 사용유무, 메시지 변경유무로 구분

캐싱 프록시 - 같은 req가 들어오면 캐시를 곧장 res. 캐시에는 유효기간이 존재한다
투명 프록시 - 메시지 변경을 하지 않는 프록시

게이트웨이 : 다른 서버를 중계하는 서버. req를 서버인 것처럼 수신하여 중계
프록시와 동작이 유사. 클라이언트와 게이트웨이 사이를 암호화하여 통신의 안정성을 높이는 역할 등을 한다

터널 : 떨어져있는 두 대의 클라이언트와 서버 사이를 중계해서 접속을 주선하는 프로그램
다른 서버와의 통신 경로를 확립. 클라이언트는 SSL 등 암호화 통신을 통해 서버와 안전하게 통신하기 위해 사용
req를 그냥 그대로 중계


## HTTP 메시지 헤더

HTTP res와 req에 처리하기 위한 정보가 담긴 헤더는 반드시 포함된다
부가적으로 중요한 정보를 전달하는 역할을 담당. 메시지 바디의 크기나 사용하는 언어, 인증 정보 등

### 헤더 필더의 구조
필드명 : 필드값의 구조를 가지며 필드값은 여러개를 가질 수 있다(ex, Date:01 Jul 2019 09:03:17 GMT)

* HTTP 헤더필드는 4종류로 구분하며 47개의 1.1버전 헤더필드가 있음
* 쿠키를 위한 헤더필드는 http/1.1 사양은 아니지만 웹사이트에서 많이 사용되고 있음
* 쿠키 헤더필드는 넷스케이프사, RFC2109, RFC2965, RFC6265의 4종류(2013. 5월 기준)


일반적 헤더필드(req, res 모두 사용)
req 헤더필드(req 메시지에 사용, 여러가지 정보를 담음)
res 헤더필드(res 정보와 서버정보, 클라이언트의 추가 정보 요구 등)
엔티티 헤더필드(req, res에 포함된 엔티티에 사용하는 헤더. 갱신 시간 등 엔티티에 관한 정보를 부가)

### req의 헤더
method, URI, http ver, http header field 등
### res의 헤더
http 메시지, http ver, 상태코드(와 설명), http header field 등



## HTTPS : SSL을 조합한 HTTP. 애플리케이션(HTTP), SSL, TCP, IP 계층을 가짐

HTTP는 평문통신(도청)이며 통신 상대를 확인하지 않고(위장) 완전성을 증명할 수 없어(변조)서 보안에 문제가 있다

### 통신 암호화(SSL, TLS 등의 다른 프로토콜 결합)
### 콘텐츠 암호화(클라이언트에서 복호화 처리가 필요. 주로 웹 서비스에서 이용)
### SSL로 증명서를 발급해 상대를 확인
### MD5나 SHA-1을 통한 해시 값을 확인하는 방법과 디지털 서명으로 변조를 막음

* SSL은 공개키암호화방식. HTTPS는 공개키 암호화로 키를 교환하고 그 후 통신에서는 교환한 키로 비밀키 암호화를 사용하는 하이브리드 암호화를 사용
* SSL은 많은 리소스를 사용하므로 처리가 느리다. SSL 엑셀레이터를 활용하여 문제를 해결중
* CA(인증기관)를 통해 공개키를 교환


## 인증

### 패스워드, 원타임토큰(OTP 등), 전자증명서(본인만이 가지고 있는 정보), 생체인증(바이오 매트릭스), IC 카드
### HTTP는 BASIC, DIGEST, SSL 클라이언트, 폼 베이스 인증을 사용

### BASIC 인증
http/1.0에서 도입. req에 401res를 보내고, 클라이언트는 id와 패스워드를 송신. 콜론을 사용해 연결해서 Base64로 인코딩한 문자열을 res
base64는 암호화가 아니라서 누구나 복호화가 가능하며 브라우저에서 로그아웃이 불가능. 현재는 별로 사용되지 않음

### DIGEST 인증
챌린지 리스폰스 방식. 챌린지 코드(realm과 nonce)를 사용해 req하고 받아서 코드로 계산하여 res
도청은 방지하지만 위장방지는 제공하지 못함

### SSL 클라이언트 인증
HTTPS 인증서를 이용한 인증. 클라이언트 증명서를 요구하고 선택하여 다시 보내 인증을 받는다. 인증이 유효하다면 공개키를 주고 HTTPS 암호화를 수행
* form base인증과 함께 2-factor 인증에서 사용
* SSL 인증은 비용이 필요

### 폼 베이스 인증
대부분 사전에 등록한 ID-PASSWORD를 사용
* 일반적으로 세션관리와 쿠키에 의해 구현된다


## HTTP에 기능을 추가한 프로토콜들

### SPDY
HTTP의 병목 현상을 해소. 단시간에 대량의 갱신정보가 발생하는데 HTTP는 이 처리를 제대로 할 수 없다

<h4>HTTP가 병목현상을 일으키는 사양</h4>
* 1개의 커넥션에선 1개의 req
* req는 클라이언트만 가능
* req/res 헤더를 압축하지 않으므로 지연이 생김. 장황한 헤더 또한 문제
* 압축이 강제가 아님

Ajax를 통해 웹페이지의 일부분만을 고치는 비동기 통신방법을 활용. 다만 대량의 req가 발생하는 것은 동일
Comet은 서버측 콘텐츠에 갱신이 있었을 경우, req를 기다리지 않고 클라이언트에게 보내는 방법 중 하나. res를 보류상태로 두고, 콘텐츠가 갱신되면 res
res를 보류하기 위해 커넥션을 유지하는 시간이 길어져 리소스를 낭비

※ SPDY는 HTTP와 SSL(TCP)계층 사이에 SPDY로 세션계층을 넣어 데이터 흐름만을 제어

<h4>SPDY의 사양</h4>
* req 우선순위 부여
* HTTP 헤더 압축
* 서버 푸시(req를 기다리지 않고 데이터를 전송)
* 서버 힌트(서버가 반대로 클라이언트에게 req할 리소스를 제안)

### WebSocket
* Ajax나 Comet에서 사용하는 XMLHttpRequest의 결점을 해결하기 위한 기술로서 개발중
* 한번 접속을 확립하면 서버나 클라이언트 어느쪽에서도 송신이 가능
웹 서버와 클라이언트가 한번 접속을 확립하면 그 뒤의 통신을 모두 전용 프로토콜로 하는 방식. JSON, XML, HTML, 이미지 등 임의 형식의 데이터로 전송


<h4>웹소켓 프로토콜의 주요기능</h4>
* 서버 푸시
* 통신량 삭감(헤더가 작고 오버헤드가 적어짐)
* 핸드쉐이크/req
* 핸드쉐이크/res
* WebSocket API(js에선 w3c에서 사양이 책정되어 제공되는 WebSocket Interface를 사용)

## HTTP/2.0
* 웹서버상 파일을 관리하는 WebDAV(보안, 편의성 문제로 쓰지 않는 PUT, DELETE의 대체.
  컬렉션, 리소스, 프로퍼티, 잠금의 업데이트와 메서드들의 추가/상태 코드 추가)
  
## WEB에서 쓰이는 기술
### HTML
### 디자인 적용 CSS
### 다이나믹 HTML(웹 페이지를 동적으로)
### HTML을 조작하기 쉽게 해주는 DOM(Document Object Model) : HTML과 XML을 위한 API. JS등의 스크립트를 사용해 HTML을 쉽게 조작할 수 있어짐
### 웹 애플리케이션(동적 콘텐츠)
### 웹 서버와 프로그램을 연계하는 CGI(Common Gateway Interface) : 웹 서버가 클라이언트에서 받은 req를 프로그램에 전달하는 구조
    요구사항에 맞게 동적으로 콘텐츠를 생성할 수 있음. Perl, PHP, Ruby, C 등의 프로그래밍 언어 사용
### Java에서 보급된 Servlet : HTML 등의 동적 콘텐츠를 생성하기 위한 프로그램. Java EE의 일부로서 제공. CGI보다 부하가 적음
### XML(eXtensible Markup Language) : 목적에 맞게 확장 가능한 마크업 언어. 데이터 기술에 특화. 태그를 사용한 트리구조 사용
### 갱신 정보 송신 RSS/Atom : 뉴스나 블로그의 기사 등의 갱신정보를 송신하기 위한 문서 포맷의 총칭. XML을 이용
### JavaScript에서 쓰기 쉬운 JSON : JavaScript Object Notation. 경량 데이터 기술 언어. JS 오브젝트 표기법을 바탕
    false/null/true/오브젝트/배열/수치/문자열의 7종류를 사용. 여러 프로그래밍 언어에서도 사용하기 쉬운 라이브러리도 많다
    
## 웹 공격
### 능동적 공격과 수동적 공격으로 구분

### 능동적 공격
서버를 노리는 능동적 공격. 공격자가 리소스에 직접 접근해야할 필요가 있다
SQL Injection, OS command Injection 등의 공격

### 수동적 공격
함정을 이용해 유저가 공격코드를 실행시키게 하여 쿠키나 권한 등을 탈취하여 악용
XSS, CSRF 등의 공격



<h4>웹 애플리케이션의 보안 대책을 실시하는 장소</h4>
* 클라이언트(대부분 js를 사용하지만 변조되거나 무효화되었을 수 있으므로 근본적인 해결책으로는 적합하지 않다)
* 웹 애플리케이션(서버)
- 입력값 체크
- 출력값 체크(보안대책으로 중요함. 이스케이프가 미비하면 공격자의 공격코드가 작동)

<h5>출력 값의 escape 미비로 인한 취약성</h5>

### 크로스 사이트 스크립팅(XSS, Cross-Site Scripting)
취약성이 있는 웹사이트를 방문한 사용자의 브라우저에서 부정한 html, js 등을 동작. 동적 HTML을 생성하는 곳에서 이뤄짐

- 가짜 입력폼에 정보를 탈취
- 쿠키값이 도둑맞거나 의도치않은 req 송신
- 가짜 문장이나 이미지 표기

### SQL Injection
SQL 문장의 구문을 파괴(주석 등을 사용)하여 부정한 SQL을 실행시킨다

- DB 부정열람이나 변조
- 인증 회피
- DB를 경유한 프로그램 설치 등의 피해

### OS Command Injection
운영체제의 쉘에 잘못된 OS 명령어를 실행시킨다

### HTTP HEADER Injection
res header field에 개행 문자 등을 넣어 임의의 res header field나 바디를 추가(바디를 추가하는 공격을 http response splitting attack이라 함)

- 임의의 쿠키가 세팅
- 임의의 URL에 리다이렉트
- 임의의 바디 표시(http 리스폰스 분할 공격시). XSS로 이어질 수 있다

### Mail HEADER Injection
헤더에 개행문자를 통한 공격. HTTP HEADER Injection과 유사

### 디렉토리 접근 공격
Directory Traversal이란 비공개 디렉토리의 파일에 디렉토리 패스를 가로질러 액세스하는 공격
../ 등의 상대 경로나 /etc/password 등의 절대경로를 지정하여 액세스해 변조시키거나 삭제하는 공격이다
임의의 파일명을 지정할 수 없게 해야한다

### 리모트 파일 인클루션(Remote File Inclusion)
다른 파일을 읽어올 때 지정한 외부 서버의 URL을 읽게 해 임의의 스크립트를 동작시킨다. 주로 PHP에서 동작. 임의의 파일명을 지정할 수 없게 해야한다



<h5>웹 서버의 설정이나 설계상의 미비로 인한 취약성</h5>

### 강제브라우징
공개 의도가 없는 파일이 열람됨. 인증 후에만 표시되게 해야한다

### 부적절한 에러 메시지 처리
에러 메시지에 공격자가 좋아할 정보가 포함되는 취약성을 노린 공격
* 주로 스크립트 에러, DB나 미들웨어의 에러, 웹 서버 에러 코드에서 이러한 현상이 나타남

### 오픈 리다이렉트
지정한 임의의 URL로 리다이렉트

<h5>세션 관리 미비</h5>

### 세션 하이잭(Session Hijack)
유저의 세션을 입수해 위장

### 세션 픽세이션(Session Fixation)
공격자가 지정한 세션 ID를 강제로 주입

### CSRF(Cross-Site Request Fogeries)
의도치 않은 상태로 개인정보나 설정 정보 등이 변경되는 것

<h5>그 외</h5>

### 패스워드 크랙킹(Password Cracking)
### 클릭 재킹(Clickjacking) : 투명한 레이어로 화면을 덮어 낚는 행위. 탈퇴버튼이 있는 자리를 투명하게 덮어두고 PLAY 버튼 이미지로 덮는 등의 낚시
### DoS(Denial of Service attack) : 액세스를 집중시켜 리소스를 소비시켜 멈추거나 취약점을 공격하여 멈추게 하는 공격
### 백도어(Backdoor)
