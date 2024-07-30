# 웹 통신의 큰 흐름: https://www.google.com/을 접속할 때 일어나는 일

**[용어 정리]**

### DNS 클라이언트 (DNS Client)

- OS의 일부로, 클라이언트 - DNS 서버 중간에서 DNS 요청 생성 / 재귀 DNS 리졸버와 통신 / IP 주소 응답 반환 등의 역할을 한다.
- 이 때, ‘hosts’파일에 접근하거나 네트워크 통신을 하기 위해 시스템 콜을 사용한다.
- **DNS Sutb Resolver** 라고도 함

### 재귀 DNS Resolver (Recursive DNS Resolver)

- **DNS 클라이언트**의 요청을 받아 필요한 경우 여러 DNS 서버를 순차적으로 조회하여 최종 IP 주소를 찾아서 반환한다.
- 일반적으로 KT, SKB, LGU+ 등 인터넷 서비스 제공자(ISP)가 기기에서 자동으로 사용하는 기본 DNS 리졸버를 제공하므로, 직접 설정할 필요는 없음.
- **로컬 DNS 서버** 또는 **DNS Recursor** 라고도 함.

---

## 1. 사용자가 URL을 웹 브라우저에 입력

## 2. 웹 브라우저는 URL을 바탕으로 IP 주소 탐색

브라우저는 운영 체제의 DNS 클라이언트에 도메인 이름을 IP 주소로 변환해 달라는 요청을 보냄

### 2-1. 로컬 정보 확인(캐시, ‘hosts’ 파일)

1) **DNS 클라이언트**는 ‘**hosts**’파일을 확인한다. 이 때 조회한 도메인에 대한 IP주소가 설정되어 있으면, 해당 IP 주소를 반환한다. 이 때 **브라우저 캐시**나 **운영체제의 캐시**는 무시되고, **가장 우선적으로 적용**된다.

2) **운영체제의** DNS 캐시, **브라우저의** DNS 캐시를 확인한다.

### 2-2. 계층적 DNS 조회

1) 로컬 정보를 확인했음에도 찾지 못한 경우, **DNS 클라이언트**는 **DNS 리졸버**에 DNS 쿼리를 보낸다.

2) DNS 리졸버는 **자신의 캐시**를 확인한다. 만약 정보가 없다면, **계층적 DNS 조회**를 시작한다.

|DNS 클라이언트가 포함된 형태|DNS 클라이언트가 포함되지 않은 형태|
|:---:|:---:|
|![Untitled (2)](https://github.com/user-attachments/assets/f1075be7-458c-438d-8817-a20b51ce1064)|![Untitled (3)](https://github.com/user-attachments/assets/fa82f9a6-953e-44c7-a24d-d75ae0f5db2e)|
|NsLookup.io|CloudDNS|

- **루트 DNS 서버** 질의: 로컬 DNS 서버는 루트 DNS 서버에 ‘www.google.com’에 대한 정보를 요청. 루트 DNS 서버는 도메인에 대한 정보를 갖고 있지 않지만, **최상위 도메인(Top-Level Domain) 서버의 주소 제공** ⇒ ‘.com’ TLD DNS 서버 IP 응답
- **TLD DNS 서버** 질의: 로컬 DNS 서버는 루트 DNS 서버가 제공한 TLD 서버(’.com’)에 다시 ‘www.google.com’에 대한 정보를 요청. TLD 서버는 **‘google.com’ 도메인에 대해 권한을 갖고 있는 DNS 서버의 주소 제공** ⇒ ‘google.com’ 도메인의 권한을 가진 DNS 서버 IP 응답
- **‘google.com’ 도메인의 권한을 가진 서버** 질의: 로컬 DNS 서버는 권한을 가진 DNS 서버에 ‘www.google.com’에 대한 정보 요청. 권한을 가진 서버는 최종적으로 **‘www.google.com’의 실제 IP주소를 응답** ⇒ ‘142.250.206.238’

3) DNS 리졸버는 최종 IP 주소를 DNS 클라이언트에 반환하며, 이 정보를 자신의 캐시에 저장한다.

4) DNS 클라이언트는 IP 주소를 운영체제의 DNS 캐시에 저장한 후 애플리케이션으로 반환한다.

5) 브라우저는 받은 IP 주소를 자신의 캐시에 저장한다.

## 3. 웹 서버와 TCP/IP 연결 진행

1) IP주소를 사용하여 3-way 핸드셰이크를 통해 구글의 웹 서버와 TCP 연결을 설정한다.

2) HTTPS를 사용하는 경우, SSL/TLS 핸드셰이크로 추가적인 연결 설정. 이 때, 암호화된 통신을 설정하기 위해 인증서 교환, 암호화 알고리즘 협상 등이 이뤄진다.

## 4. HTTP 요청 전송

연결 설정이 완료된 후, 브라우저는 ‘www.google.com’에 대한 HTTP GET 요청을 전송한다.

## 5. HTTP 요청 응답

1) 구글 웹 서버는 요청된 리소스(HTML 페이지, 이미지 등)를 찾는다.

2) HTTP 응답을 생성하고, 콘텐츠와 상태코드를 포함하여 반환한다.

## 6. 브라우저가 받은 응답을 바탕으로 사용자에게 웹 페이지 제공

1) 암호화된 응답 데이터를 복호화하여 콘텐츠 확인

2) 수신한 HTML, CSS, JavaScript를 파싱하여 웹 페이지 렌더링

3) 모든 리소스가 로드되면, 완성된 웹 페이지 표시

---

참고

[NsLookup.io - 학습 센터 : DNS 스텁 리졸버란 무엇인가요](https://www.nslookup.io/learning/what-is-a-dns-resolver/)

[CLOUDFLARE - 학습 센터 : DNS란 무엇인가? | DNS 작동 방식](https://www.cloudflare.com/learning/dns/what-is-dns/)

[LENOVO - 용어집 : 도메인 이름 시스템(DNS) 리졸버란 무엇입니까?](https://www.lenovo.com/us/en/glossary/dns-resolver/?orgRef=https%253A%252F%252Fwww.google.com%252F)
