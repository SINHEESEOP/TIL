# 🔖 HTTP 헤더(2) - 정보, 인증, 쿠키

## 📌 일반 정보 헤더

일반 정보 헤더는 요청이나 응답에 대한 추가 정보를 제공합니다. 이 헤더들은 메시지 자체나 클라이언트, 서버에 대한 정보를 담고 있습니다. 🧩

### 6-1. From

**클라이언트 사용자의 이메일 주소를 포함하는 헤더입니다.**

```http
From: user@example.com
```

**특징:**
- 일반적인 웹 브라우저에서는 거의 사용되지 않습니다.
- 주로 크롤러나 봇 등에서 관리자 연락처를 제공하기 위해 사용됩니다.
- 개인정보 노출 우려가 있어 실무에서는 사용 빈도가 낮습니다.

### 6-2. Referer

**현재 요청이 시작된 이전 웹 페이지의 주소를 나타내는 헤더입니다.**

```http
Referer: https://www.google.com/search?q=example
```

**특징:**
- 사용자가 어디에서 왔는지 추적하는 데 유용합니다.
- 웹 분석, 유입 경로 분석에 활용됩니다.
- 오타가 있는 헤더명입니다! 'Referrer'가 아닌 'Referer'입니다. (HTTP 명세의 철자 오류가 표준이 되었습니다)

**활용:**
- 웹사이트 내에서 사용자 이동 경로 추적
- 마케팅 캠페인 효과 측정
- 보안 목적으로 비정상적인 접근 감지

### 6-3. User-Agent

**클라이언트 애플리케이션에 대한 정보를 제공하는 헤더입니다.**

```http
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/96.0.4664.110 Safari/537.36
```

**포함 정보:**
- 브라우저 종류와 버전
- 운영 체제 정보
- 디바이스 정보 (모바일 등)

**활용:**
- 클라이언트 환경별 최적화된 콘텐츠 제공
- 통계 수집
- 특정 브라우저에 대한 대응 코드 작성

### 6-4. Server

**서버 소프트웨어 정보를 제공하는 응답 헤더입니다.**

```http
Server: Apache/2.4.41 (Ubuntu)
```

**특징:**
- 웹 서버의 종류와 버전 정보를 알려줍니다.
- 보안 관점에서는 최소한의 정보만 노출하는 것이 좋습니다.

**보안 고려사항:**
- 상세한 버전 정보를 노출하면 보안 취약점이 있는 버전을 공격자가 알 수 있습니다.
- 실제 운영 환경에서는 간략하게 설정하는 것이 좋습니다.
  ```http
  Server: Apache
  ```

### 6-5. Date

**메시지가 생성된 날짜와 시간을 나타내는 헤더입니다.**

```http
Date: Tue, 15 Nov 2023 08:12:31 GMT
```

**특징:**
- HTTP 1.1 응답에서는 필수 헤더입니다.
- 항상 GMT(그리니치 표준시)로 표시합니다.
- 캐싱, 로깅, 요청 타임아웃 계산 등에 활용됩니다.

## 🔍 특별한 정보 헤더

특별한 정보 헤더는 HTTP 통신에서 특별한 목적을 가진 헤더로, 요청이나 응답의 동작 방식에 영향을 미칩니다. 📋

### 7-1. Host

**요청한 호스트명과 포트 번호를 지정하는 요청 헤더입니다.**

```http
Host: www.example.com:443
```

**특징:**
- HTTP/1.1에서 **유일한 필수 헤더**입니다!
- 하나의 IP 주소에 여러 도메인이 호스팅되는 가상 호스트 환경에서 중요합니다.

**예시 상황:**
하나의 서버(IP: 123.456.789.10)에 여러 웹사이트가 호스팅된 경우:
- www.example.com
- www.test.com
- blog.example.com

이 경우 서버는 `Host` 헤더를 보고 어떤 웹사이트의 리소스를 제공할지 결정합니다.

```http
GET /index.html HTTP/1.1
Host: www.example.com
```
이면 example.com의 index.html을 제공하고,

```http
GET /index.html HTTP/1.1
Host: www.test.com
```
이면 test.com의 index.html을 제공합니다.

### 7-2. Location

**클라이언트를 지정된 URI로 리다이렉션시키는 응답 헤더입니다.**

```http
Location: https://www.example.com/new-page
```

**주로 사용되는 상황:**
- **3xx(리다이렉션) 상태 코드와 함께 사용됩니다.**
  - 301: 영구 이동
  - 302: 임시 이동
  - 303: See Other
  - 307: 임시 리다이렉션
  - 308: 영구 리다이렉션
- 201(Created) 상태 코드와 함께 새로 생성된 리소스의 URI를 알려줄 때 사용됩니다.

**활용 예시:**
1. 웹사이트 주소 변경
2. HTTPS로 강제 전환
3. 로그인 후 이전 페이지로 리다이렉션
4. 리소스 생성 후 상세 페이지로 이동

### 7-3. Allow

**리소스에 대해 허용되는 HTTP 메서드를 나열하는 응답 헤더입니다.**

```http
Allow: GET, POST, HEAD
```

**특징:**
- 405 Method Not Allowed 응답과 함께 반드시 포함해야 합니다.
- 클라이언트에게 해당 리소스에 대해 어떤 메서드를 사용할 수 있는지 안내합니다.

**활용:**
API 설계 시 리소스별로 허용되는 메서드를 명확히 알려주는 용도로 활용할 수 있습니다.

### 7-4. Retry-After

**클라이언트가 다음 요청을 보내기 전에 기다려야 하는 시간을 알려주는 응답 헤더입니다.**

```http
Retry-After: 120
```
또는 날짜 형식으로:
```http
Retry-After: Fri, 31 Dec 2023 23:59:59 GMT
```

**주로 사용되는 상황:**
- 503 Service Unavailable: 서버가 일시적으로 요청을 처리할 수 없음
- 429 Too Many Requests: 클라이언트가 너무 많은 요청을 보냄

**활용:**
- 서버 유지보수 시 재시도 시간 안내
- 트래픽 제한(Rate Limiting) 구현
- 계획된 다운타임 알림

## 🔐 인증 관련 헤더

인증 관련 헤더는 클라이언트가 서버에 자신을 인증하고, 보호된 리소스에 접근할 수 있도록 하는 메커니즘을 제공합니다. 🔒

### 8-1. Authorization

**클라이언트가 서버에 인증 정보(credentials)를 제공하는 요청 헤더입니다.**

```http
Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=
```

**자주 사용되는 인증 스킴:**
- **Basic**: 사용자 이름과 비밀번호를 Base64로 인코딩 (안전하지 않음)
- **Bearer**: JWT나 OAuth 토큰을 전달 (주로 API 인증에 사용)
- **Digest**: 해시 기반의 인증 방식

**Bearer 토큰 예시:**
```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### 8-2. WWW-Authenticate

**서버가 클라이언트에 인증이 필요하다는 것과 인증 방법을 알려주는 응답 헤더입니다.**

```http
WWW-Authenticate: Basic realm="Access to the private site", charset="UTF-8"
```

**특징:**
- 401 Unauthorized 응답과 함께 반드시 전송해야 합니다.
- realm 속성은 보호 영역(protected area)의 이름을 지정합니다.

**흐름:**
1. 클라이언트가 보호된 리소스 요청
2. 서버가 401 + WWW-Authenticate 응답
3. 브라우저가 인증 대화상자 표시
4. 사용자가 자격 증명 입력
5. 클라이언트가 Authorization 헤더와 함께 재요청
6. 인증 성공 시 리소스 접근 허용

## 🍪 쿠키

쿠키는 서버가 클라이언트에 데이터를 저장하는 매커니즘으로, HTTP의 Stateless 특성을 보완하여 상태 유지(세션 관리, 개인화, 추적 등)를 가능하게 합니다. 🍪

### 9-1. 쿠키 관련 헤더

#### Set-Cookie
**서버가 클라이언트에 쿠키를 설정하는 응답 헤더입니다.**

```http
Set-Cookie: user=홍길동; expires=Wed, 21 Oct 2023 07:28:00 GMT; path=/; domain=example.com; secure; httpOnly
```

#### Cookie
**클라이언트가 서버에 이전에 저장된 쿠키를 전송하는 요청 헤더입니다.**

```http
Cookie: user=홍길동; session_id=abc123
```

### 9-2. 쿠키 동작 방식

1. 서버가 클라이언트에 응답과 함께 `Set-Cookie` 헤더 전송
2. 브라우저는 쿠키를 저장
3. 이후 해당 서버에 요청 시 자동으로 `Cookie` 헤더에 저장된 쿠키 포함
4. 서버는 쿠키를 통해 클라이언트를 식별하고 상태 유지

### 9-3. 쿠키 속성

#### 생명주기 속성

**Expires**
- 쿠키의 만료 날짜를 지정합니다.
- 지정된 날짜가 지나면 쿠키는 삭제됩니다.
```http
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2023 07:28:00 GMT
```

**Max-Age**
- 쿠키의 수명을 초 단위로 지정합니다.
- Expires보다 우선합니다.
```http
Set-Cookie: id=a3fWa; Max-Age=86400  <!-- 24시간 -->
```

**세션 쿠키와 영속적 쿠키:**
- **세션 쿠키**: Expires나 Max-Age 속성이 없으면 브라우저 종료 시 쿠키 삭제
- **영속적 쿠키**: Expires나 Max-Age 속성이 있으면 지정된 시간까지 쿠키 유지

#### 보안 속성

**Secure**
- HTTPS 프로토콜을 사용하는 경우에만 쿠키를 전송합니다.
- 쿠키 도청을 방지합니다.
```http
Set-Cookie: id=a3fWa; Secure
```

**HttpOnly**
- JavaScript를 통한 쿠키 접근을 방지합니다.
- XSS(Cross-Site Scripting) 공격으로부터 보호합니다.
```http
Set-Cookie: id=a3fWa; HttpOnly
```

**SameSite**
- CSRF(Cross-Site Request Forgery) 공격을 방지합니다.
- 옵션:
  - **Strict**: 같은 사이트의 요청에만 쿠키 전송
  - **Lax**: 일부 크로스 사이트 요청에는 쿠키 전송 허용 (기본값)
  - **None**: 모든 크로스 사이트 요청에 쿠키 전송 (Secure 속성 필요)
```http
Set-Cookie: id=a3fWa; SameSite=Strict
```

#### 도메인 및 경로 속성

**Domain**
- 쿠키를 전송할 도메인을 지정합니다.
- 지정하지 않으면 현재 호스트에만 적용됩니다.
- 서브도메인 포함을 원하면 명시적으로 지정해야 합니다.
```http
Set-Cookie: id=a3fWa; Domain=example.com
```

**Path**
- 쿠키를 전송할 URL 경로를 지정합니다.
- 지정한 경로와 그 하위 경로에만 쿠키가 전송됩니다.
```http
Set-Cookie: id=a3fWa; Path=/blog
```

### 9-4. 쿠키의 한계와 대안

**한계:**
- 용량 제한: 쿠키 하나는 4KB로 제한되며, 도메인당 총 용량도 제한됨
- 보안 문제: 민감한 정보는 저장하기에 안전하지 않음
- 매 요청마다 전송되어 대역폭 낭비 가능성
- 제3자 쿠키는 개인정보 보호 문제 야기

**대안 및 보완 기술:**
- **Web Storage API**
  - **localStorage**: 브라우저를 닫아도 유지되는 영구 저장소
  - **sessionStorage**: 탭/창을 닫으면 삭제되는 임시 저장소
- **IndexedDB**: 대용량 구조화된 데이터 저장에 적합
- **Progressive Web App(PWA)**: 오프라인 경험과 로컬 저장 기능 제공

## 💭 정리

HTTP 헤더는 웹 통신의 핵심 요소로, 메타데이터를 통해 클라이언트와 서버 간의 정보 교환을 가능하게 합니다.

**일반 정보 헤더**는 기본적인 메시지 컨텍스트를 제공하며, **특별한 정보 헤더**는 요청이나 응답의 특별한 동작을 제어합니다. **인증 관련 헤더**는 보호된 리소스에 대한 접근 제어 매커니즘을 제공하고, **쿠키**는 HTTP의 Stateless 특성을 보완하여 사용자 상태 유지를 가능하게 합니다.

이러한 HTTP 헤더들은 웹 애플리케이션 개발, API 설계, 보안 구현 등에서 핵심적인 역할을 하므로, 개발자는 주요 헤더의 기능과 올바른 사용법을 이해하는 것이 중요합니다. 📚 