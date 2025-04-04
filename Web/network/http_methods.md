# 🔄 HTTP 메서드

## 📝 HTTP 메서드란?

HTTP 메서드는 클라이언트가 웹 서버에게 요청의 목적과 종류를 알려주는 방식입니다. 각 메서드는 서버에서 수행할 작업의 유형을 나타냅니다. 🚀

## 🛠️ 주요 HTTP 메서드

### GET
- **용도**: 리소스 조회
- **특징**:
  - 서버에서 데이터를 조회만 함 (READ)
  - URL에 모든 파라미터가 노출됨
  - 캐싱 가능
  - 북마크 가능
  - 브라우저 히스토리에 남음
  - 데이터 길이 제한 있음 (URL 길이 제한)
  - 멱등성(Idempotent) 보장: 여러 번 호출해도 같은 결과

```http
GET /users?id=123 HTTP/1.1
Host: api.example.com
```

### POST
- **용도**: 리소스 생성, 데이터 처리
- **특징**:
  - 새로운 리소스 생성 (CREATE)
  - 데이터가 요청 본문(body)에 포함됨
  - URL에 데이터가 노출되지 않음
  - 캐싱 어려움
  - 북마크 불가능
  - 데이터 길이 제한 없음
  - 멱등성 보장 안됨: 여러 번 호출하면 여러 개 리소스 생성될 수 있음

```http
POST /users HTTP/1.1
Host: api.example.com
Content-Type: application/json

{
  "name": "홍길동",
  "email": "hong@example.com"
}
```

### PUT
- **용도**: 리소스 완전 대체(덮어쓰기)
- **특징**:
  - 리소스를 완전히 대체 (UPDATE)
  - 해당 리소스가 없으면 생성 가능
  - 클라이언트가 리소스의 위치(URI)를 알고 있어야 함
  - 멱등성 보장: 여러 번 호출해도 결과 동일

```http
PUT /users/123 HTTP/1.1
Host: api.example.com
Content-Type: application/json

{
  "name": "홍길동",
  "email": "hong@example.com"
}
```

### PATCH
- **용도**: 리소스 부분 수정
- **특징**:
  - 리소스를 부분적으로 수정 (UPDATE)
  - PUT과 달리 일부 필드만 변경 가능
  - 멱등성이 보장되지 않을 수 있음 (구현에 따라 다름)

```http
PATCH /users/123 HTTP/1.1
Host: api.example.com
Content-Type: application/json

{
  "email": "newhong@example.com"
}
```

### DELETE
- **용도**: 리소스 삭제
- **특징**:
  - 지정된 리소스 삭제 (DELETE)
  - 멱등성 보장: 여러 번 호출해도 결과는 동일 (이미 삭제된 상태)

```http
DELETE /users/123 HTTP/1.1
Host: api.example.com
```

### HEAD
- **용도**: GET과 동일하지만 응답 본문을 반환하지 않음
- **특징**:
  - 리소스의 메타데이터만 확인 (존재 여부, 크기, 수정 시간 등)
  - 대용량 리소스 다운로드 전 확인 용도
  - 멱등성 보장

```http
HEAD /files/large-image.jpg HTTP/1.1
Host: api.example.com
```

### OPTIONS
- **용도**: 서버가 지원하는 메서드 확인
- **특징**:
  - 사전 요청(preflight)으로 CORS에서 활용
  - 리소스에 대해 허용된 통신 옵션 확인
  - 멱등성 보장

```http
OPTIONS /api/users HTTP/1.1
Host: api.example.com
```

## 💡 HTTP 메서드의 속성

### 안전(Safe Methods)
- 서버의 상태를 변경하지 않는 메서드
- 읽기 전용 작업만 수행
- **안전한 메서드**: GET, HEAD, OPTIONS

### 멱등성(Idempotent Methods)
- 동일한 요청을 여러 번 보내도 결과가 같은 속성
- 서버의 상태가 같게 유지됨
- **멱등 메서드**: GET, HEAD, PUT, DELETE, OPTIONS
- **비멱등 메서드**: POST, PATCH(일반적으로)

### 캐시 가능(Cacheable Methods)
- 응답을 캐시할 수 있는 메서드
- **캐시 가능 메서드**: GET, HEAD
- **조건부 캐시 가능**: POST, PATCH(드물게)

## 📋 HTTP 메서드 비교표

| 메서드 | 리소스 조작 | 안전 | 멱등 | 캐시 가능 | 요청 본문 | 응답 본문 |
|-------|-----------|-----|-----|----------|----------|----------|
| GET | 조회 | ✅ | ✅ | ✅ | ❌ | ✅ |
| POST | 생성 | ❌ | ❌ | 조건부 | ✅ | ✅ |
| PUT | 완전 대체 | ❌ | ✅ | ❌ | ✅ | ✅ |
| PATCH | 부분 수정 | ❌ | ❌ | 조건부 | ✅ | ✅ |
| DELETE | 삭제 | ❌ | ✅ | ❌ | ❌ | ✅ |
| HEAD | 헤더 조회 | ✅ | ✅ | ✅ | ❌ | ❌ |
| OPTIONS | 지원 옵션 조회 | ✅ | ✅ | ❌ | ❌ | ✅ |

## 🌐 RESTful API에서의 HTTP 메서드 활용

RESTful API 설계에서는 HTTP 메서드를 CRUD(Create, Read, Update, Delete) 작업에 매핑하여 사용합니다:

| CRUD 작업 | HTTP 메서드 | 예시 URL |
|----------|-----------|----------|
| Create | POST | POST /users |
| Read | GET | GET /users/123 |
| Update | PUT, PATCH | PUT /users/123 |
| Delete | DELETE | DELETE /users/123 |

### RESTful API 설계 예시
- **회원 목록 조회**: GET /users
- **특정 회원 조회**: GET /users/{id}
- **회원 생성**: POST /users
- **회원 정보 수정**: PUT /users/{id} 또는 PATCH /users/{id}
- **회원 삭제**: DELETE /users/{id}

## 🔍 실무에서의 HTTP 메서드 선택 기준

1. **GET**: 데이터 조회가 목적일 때, 안전하고 캐싱 가능한 요청에 사용
2. **POST**: 새 리소스 생성, 복잡한 데이터 처리, 큰 데이터 전송 시 사용
3. **PUT**: 리소스를 완전히 대체할 때, 멱등성이 필요할 때 사용
4. **PATCH**: 리소스의 일부만 수정할 때 사용
5. **DELETE**: 리소스 삭제 시 사용

## 🎯 정리

HTTP 메서드는 웹 API 설계의 핵심으로, 각 메서드의 특성과 용도를 이해하면 더 효율적이고 명확한 API를 설계할 수 있습니다. 메서드의 안전성, 멱등성, 캐시 가능성을 고려하여 상황에 맞는 메서드를 선택하는 것이 중요합니다. 📚 