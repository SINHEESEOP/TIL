# HTTP 헤더

## HTTP 헤더란?

HTTP 헤더는 클라이언트와 서버가 요청 또는 응답으로 부가적인 정보를 전송할 수 있도록 해주는 필드입니다. 헤더는 이름과 값으로 구성되며, 콜론(:)으로 구분됩니다.

```
헤더이름: 헤더값
```

## HTTP 헤더의 분류

HTTP 헤더는 다음과 같이 분류할 수 있습니다:

### 1. 일반 헤더 (General Headers)
- 요청과 응답 모두에 적용되는 헤더
- 메시지 전체에 적용되는 정보 포함

### 2. 요청 헤더 (Request Headers)
- 클라이언트가 서버로 보내는 요청 메시지에만 사용
- 요청에 대한 정보와 클라이언트 정보 제공

### 3. 응답 헤더 (Response Headers)
- 서버가 클라이언트로 보내는 응답 메시지에만 사용
- 응답에 대한 부가 정보 제공

### 4. 엔티티 헤더 (Entity Headers)
- 요청과 응답 메시지의 본문에 대한 정보를 포함
- 컨텐츠 길이, 유형 등 정보 제공

## 주요 HTTP 헤더

### 일반 헤더 (General Headers)
- **Date**: 메시지가 생성된 날짜와 시간
- **Connection**: 클라이언트와 서버 간 연결에 대한 옵션
  - `keep-alive`: 연결 유지
  - `close`: 연결 종료
- **Cache-Control**: 캐싱 메커니즘 지시어
- **Transfer-Encoding**: 전송 데이터 인코딩 방식

### 요청 헤더 (Request Headers)
- **Host**: 요청한 호스트 정보 (도메인)
- **User-Agent**: 클라이언트 애플리케이션 정보
- **Accept**: 클라이언트가 받을 수 있는 컨텐츠 타입
- **Accept-Encoding**: 클라이언트가 이해할 수 있는 압축 방식
- **Accept-Language**: 클라이언트가 선호하는 언어
- **Authorization**: 인증 토큰 정보
- **Referer**: 현재 요청이 시작된 이전 웹 페이지 주소
- **Cookie**: 서버에 전송하는 쿠키 정보

### 응답 헤더 (Response Headers)
- **Server**: 요청을 처리한 서버 소프트웨어 정보
- **Set-Cookie**: 클라이언트에게 쿠키 설정을 지시
- **WWW-Authenticate**: 리소스 접근 시 필요한 인증 방식
- **Access-Control-Allow-Origin**: CORS 허용 출처
- **Location**: 리다이렉션 대상 URL

### 엔티티 헤더 (Entity Headers)
- **Content-Type**: 본문의 미디어 타입
- **Content-Length**: 본문의 크기 (바이트)
- **Content-Encoding**: 본문의 압축 방식
- **Content-Language**: 본문의 자연 언어
- **Content-Location**: 본문의 실제 위치
- **Expires**: 응답 컨텐츠의 만료 일시
- **Last-Modified**: 리소스의 최종 수정 일시

## HTTP 헤더 활용 예시

### 1. 캐시 제어
```
Cache-Control: max-age=3600, must-revalidate
```
- 리소스를 3600초(1시간) 동안 캐시할 수 있음
- 캐시된 리소스를 사용하기 전에 재검증 필요

### 2. 컨텐츠 타입 지정
```
Content-Type: application/json; charset=UTF-8
```
- 본문이 JSON 형식이며 UTF-8 인코딩을 사용함을 나타냄

### 3. CORS 설정
```
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET, POST, PUT
Access-Control-Allow-Headers: Content-Type, Authorization
```
- 모든 도메인에서의 요청 허용
- GET, POST, PUT 메서드 허용
- Content-Type, Authorization 헤더 허용

### 4. 인증
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```
- JWT 토큰을 사용한 Bearer 인증 방식

## HTTP 헤더 사용 시 주의 사항

1. **보안 관련 헤더**
   - HTTPS 사용 시: `Strict-Transport-Security`
   - XSS 방지: `Content-Security-Policy`
   - 클릭재킹 방지: `X-Frame-Options`

2. **성능 최적화**
   - 적절한 캐시 헤더 설정
   - 압축 활용 (Accept-Encoding, Content-Encoding)
   - 필요 없는 헤더는 생략하여 메시지 크기 최소화

3. **호환성**
   - 모든 브라우저가 모든 헤더를 지원하지 않음
   - 표준 헤더 우선 사용
   - 비표준(X- 접두사) 헤더는 특정 상황에만 사용 