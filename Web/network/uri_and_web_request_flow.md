# URI, 웹 브라우저 요청 흐름, 클라이언트 서버 구조

## URI (Uniform Resource Identifier)

### URI란 무엇인가?
URI는 인터넷 상의 자원을 고유하게 식별하기 위한 통일된 방식입니다. 쉽게 말해, 인터넷에 있는 리소스의 "주소"라고 생각할 수 있습니다.

### URI의 종류
URI는 크게 두 가지 종류로 나눌 수 있습니다:

1. **URL (Uniform Resource Locator)** - 자원의 위치를 나타냄
   - 예: `https://www.example.com/products/1234`
   - 리소스가 어디에 있는지 알려줍니다

2. **URN (Uniform Resource Name)** - 자원의 이름을 나타냄
   - 예: `urn:isbn:9780141036144`
   - 위치에 관계없이 리소스 자체의 이름을 지정합니다

### URL의 구성 요소
URL은 가장 흔히 사용되는 URI 형태로, 다음과 같은 구성 요소를 가집니다:

```
scheme://[userinfo@]host[:port][/path][?query][#fragment]
```

- **scheme (스킴)**: 사용할 프로토콜 (http, https, ftp 등)
- **userinfo**: 사용자 정보 (거의 사용되지 않음)
- **host**: 서버의 호스트명 또는 IP 주소
- **port**: 서버 포트 번호 (생략 시 기본값 사용: http=80, https=443)
- **path**: 리소스 경로
- **query**: 쿼리 파라미터 (key=value 형태, &로 구분)
- **fragment**: 문서 내 특정 부분을 가리키는 앵커

### URL 예시와 분석

```
https://www.example.com:443/products?category=books&sort=price#reviews
```

- **scheme**: https
- **host**: www.example.com
- **port**: 443
- **path**: /products
- **query**: category=books&sort=price
- **fragment**: reviews

## 웹 브라우저 요청 흐름

웹 페이지가 표시되기까지의 과정을 단계별로 살펴보겠습니다.

### 1. URL 입력
사용자가 브라우저 주소창에 URL을 입력합니다 (예: `https://www.example.com`).

### 2. DNS 조회
브라우저는 입력된 도메인 이름(www.example.com)을 IP 주소로 변환하기 위해 DNS(Domain Name System) 서버에 쿼리를 보냅니다.
1. 브라우저 캐시 확인
2. OS 캐시 확인
3. 라우터 캐시 확인
4. ISP DNS 서버 확인
5. 재귀적 DNS 조회 수행

### 3. TCP/IP 연결 수립
브라우저는 찾은 IP 주소로 서버에 연결을 시도합니다.
1. 3-way handshake로 TCP 연결 수립 (SYN → SYN-ACK → ACK)
2. HTTPS의 경우 추가로 TLS 핸드셰이크 진행

### 4. HTTP 요청 전송
TCP 연결이 수립되면, 브라우저는 HTTP 요청 메시지를 생성하여 서버로 전송합니다.

```
GET / HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0 ...
Accept: text/html,application/xhtml+xml,...
```

### 5. 서버 처리
서버는 요청을 받아 처리합니다.
1. 웹 서버가 요청 수신
2. 요청 분석
3. 필요한 리소스 검색 또는 동적 컨텐츠 생성
4. 응답 생성

### 6. HTTP 응답 반환
서버는 처리 결과를 HTTP 응답으로 브라우저에 반환합니다.

```
HTTP/1.1 200 OK
Date: Tue, 15 Nov 2023 08:12:31 GMT
Content-Type: text/html
Content-Length: 5426

<!DOCTYPE html>
<html>
...
</html>
```

### 7. 브라우저 렌더링
브라우저는 받은 HTML 응답을 파싱하고 화면에 표시합니다.
1. HTML 파싱 → DOM(Document Object Model) 트리 생성
2. CSS 파싱 → CSSOM(CSS Object Model) 트리 생성
3. JavaScript 실행
4. 렌더 트리 생성 (DOM + CSSOM)
5. 레이아웃 계산 (요소 위치와 크기 결정)
6. 페인팅 (화면에 픽셀 그리기)

### 8. 추가 리소스 로딩
HTML 내에 포함된 추가 리소스(이미지, CSS, JavaScript 등)에 대해 2~7단계를 반복합니다.

## 클라이언트 서버 구조

### 클라이언트 서버 모델이란?
클라이언트 서버 구조는 네트워크 아키텍처로, 서비스를 요청하는 클라이언트와 서비스를 제공하는 서버로 역할이 분리된 모델입니다.

### 구성 요소

#### 클라이언트 (Client)
- 사용자가 직접 상호작용하는 프로그램 또는 시스템
- 서비스를 요청하는 주체
- 예: 웹 브라우저, 모바일 앱, 데스크톱 애플리케이션

#### 서버 (Server)
- 클라이언트의 요청을 처리하고 응답을 제공하는 프로그램 또는 시스템
- 서비스를 제공하는 주체
- 예: 웹 서버, 데이터베이스 서버, 파일 서버, 메일 서버

### 작동 방식
1. 클라이언트가 서버에 요청(Request)을 보냄
2. 서버는 요청을 처리
3. 서버가 클라이언트에게 응답(Response)을 반환
4. 클라이언트는 응답을 받아 처리하고 사용자에게 표시

### 특징

#### 장점
- **역할 분리**: 클라이언트와 서버는 각자의 역할에 집중할 수 있습니다.
- **확장성**: 필요에 따라 클라이언트나 서버를 독립적으로 확장할 수 있습니다.
- **유지보수성**: 서버 측 로직이 변경되어도 클라이언트를 업데이트할 필요가 없는 경우가 많습니다.
- **리소스 공유**: 여러 클라이언트가 하나의 서버 리소스를 공유할 수 있습니다.

#### 단점
- **네트워크 의존성**: 네트워크 연결이 필요하며, 네트워크 문제 시 서비스 이용 불가
- **서버 과부하**: 많은 클라이언트가 동시에 요청하면 서버에 부하가 발생할 수 있음
- **단일 실패 지점**: 서버 장애 시 모든 클라이언트에 영향

### 발전된 구조
- **3-tier Architecture**: 프레젠테이션 계층(UI), 애플리케이션 계층(비즈니스 로직), 데이터 계층(데이터 저장)으로 구성
- **Microservices**: 작고 독립적인 여러 서비스로 시스템을 구성
- **Serverless**: 서버 관리 없이 함수 단위로 서비스 제공

## 정리

인터넷의 작동 방식은 URI를 통한 자원 식별, 웹 브라우저의 요청 처리 흐름, 그리고 클라이언트와 서버 간의 상호작용을 기반으로 합니다. 이 세 가지 개념은 현대 웹 애플리케이션의 기본 구조를 이루는 핵심 요소입니다.