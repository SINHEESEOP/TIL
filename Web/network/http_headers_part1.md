# HTTP 헤더(1) - 기본 개념과 표현

## HTTP 헤더란?

HTTP 헤더는 클라이언트와 서버가 요청 또는 응답으로 부가적인 정보를 전송할 수 있도록 해주는 필드입니다. HTTP 전송에 필요한 모든 부가정보를 담고 있습니다. 예를 들어, 메시지 바디의 내용, 메시지 바디의 크기, 압축 여부, 인증 정보, 요청 클라이언트, 서버 정보, 캐시 관리 정보 등이 있습니다.

### 1-1. 헤더의 형식과 구조

헤더는 다음과 같은 형식으로 구성됩니다:
```
field-name: field-value
```

예시:
```http
Content-Type: text/html;charset=UTF-8
Content-Length: 3423
Cache-Control: max-age=3600
```

### 1-2. 과거 헤더 분류 (RFC2616)

과거 RFC2616에서는 헤더를 다음과 같이 분류했습니다:

1. **General 헤더**: 요청과 응답 모두에 적용되는 헤더
   - 예: `Date`, `Connection`

2. **Request 헤더**: 요청 정보를 담는 헤더 
   - 예: `User-Agent`, `Accept`

3. **Response 헤더**: 응답 정보를 담는 헤더
   - 예: `Server`, `Allow`

4. **Entity 헤더**: 엔티티 바디 정보를 담는 헤더
   - 예: `Content-Type`, `Content-Length`

### 1-3. 최신 표준으로의 변화 (RFC7230~7235)

HTTP 표준이 RFC7230~7235로 변경되면서 '엔티티(Entity)'라는 개념이 '표현(Representation)'으로 변경되었습니다. 이는 HTTP 메시지의 본문이 실제 리소스가 아닌, 리소스의 표현이라는 개념을 명확히 한 것입니다.

## HTTP 메시지 바디와 표현

### 2-1. 메시지 바디(Message Body)

HTTP 메시지 바디는 실제 전송할 데이터를 담는 부분입니다. 이것은 요청이나 응답에서 전달할 실제 정보를 포함합니다.

메시지 바디는 다음과 같은 다양한 형태를 가질 수 있습니다:
- HTML 문서
- JSON 데이터
- XML 데이터
- 바이너리 파일(이미지, 비디오 등)
- 폼 데이터

### 2-2. 표현(Representation)

'표현'은 리소스의 특정 상태를 반영한 정보입니다. 동일한 리소스라도 요청에 따라 다른 형태로 표현될 수 있습니다(예: HTML, JSON, XML 등).

표현은 다음 두 가지 요소로 구성됩니다:
1. **표현 메타데이터(표현 헤더)**: 표현 데이터를 해석하는 데 필요한 정보
2. **표현 데이터(메시지 바디)**: 실제 전송되는 데이터

```
HTTP/1.1 200 OK
[ 표현 헤더 ]
Content-Type: text/html;charset=UTF-8
Content-Length: 3423

[ 표현 데이터 ]
<html>
  <body>...</body>
</html>
```

## 표현 헤더(Representation Headers)

### 3-1. Content-Type

**데이터의 형식을 지정하는 헤더입니다.** 미디어 타입, 문자 인코딩을 나타냅니다.

**주요 값:**
- `text/html`: HTML 문서
- `text/plain`: 일반 텍스트
- `application/json`: JSON 데이터
- `application/xml`: XML 데이터
- `image/png`, `image/jpeg`: 이미지 데이터

**예시:**
```http
Content-Type: text/html;charset=UTF-8
Content-Type: application/json
```

**활용:**
서버는 응답의 `Content-Type`을 명확히 설정해야 클라이언트가 올바르게 데이터를 해석할 수 있습니다. API 응답에서는 주로 `application/json`을 사용합니다.

### 3-2. Content-Encoding

**데이터 압축 방식을 지정하는 헤더입니다.** 표현 데이터를 압축하면 전송 효율이 높아집니다.

**주요 값:**
- `gzip`: GNU zip 압축
- `deflate`: zlib 압축
- `br`: Brotli 압축

**예시:**
```http
Content-Encoding: gzip
```

**작동 방식:**
1. 서버에서 원본 데이터를 gzip 등으로 압축
2. 압축 정보를 Content-Encoding에 명시
3. 클라이언트는 해당 인코딩 방식에 맞게 압축 해제

### 3-3. Content-Language

**데이터의 자연 언어를 지정하는 헤더입니다.** 표현 데이터가 어떤 언어로 작성되었는지 나타냅니다.

**주요 값:**
- `ko`: 한국어
- `en`: 영어
- `en-US`: 미국 영어
- `en-GB`: 영국 영어

**예시:**
```http
Content-Language: ko
Content-Language: en-US
```

**활용:**
다국어 지원 웹사이트에서 현재 문서의 언어를 명시하는 데 사용됩니다.

### 3-4. Content-Length

**표현 데이터의 길이(바이트 단위)를 지정하는 헤더입니다.**

**예시:**
```http
Content-Length: 5327
```

**주의 사항:**
- `Transfer-Encoding: chunked`를 사용할 경우 Content-Length를 사용하지 않습니다.
- 정확한 길이를 지정해야 클라이언트가 메시지 바디를 올바르게 해석할 수 있습니다.

## 콘텐츠 협상(Content Negotiation)

콘텐츠 협상은 클라이언트가 선호하는 표현을 서버에게 요청하는 과정입니다. 클라이언트는 요청 시 **Accept 관련 헤더**를 사용하여 선호하는 미디어 타입, 언어 등을 알려주고, 서버는 이를 고려하여 적절한 표현을 제공합니다.

### 4-1. 콘텐츠 협상 헤더들

모든 콘텐츠 협상 헤더는 클라이언트가 요청 시 사용합니다:

1. **Accept**: 클라이언트가 선호하는 미디어 타입 표현
   ```http
   Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
   ```

2. **Accept-Charset**: 클라이언트가 선호하는 문자 인코딩
   ```http
   Accept-Charset: utf-8
   ```

3. **Accept-Encoding**: 클라이언트가 선호하는 압축 인코딩
   ```http
   Accept-Encoding: gzip, deflate, br
   ```

4. **Accept-Language**: 클라이언트가 선호하는 언어
   ```http
   Accept-Language: ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7
   ```

### 4-2. Quality Values (q값)

클라이언트는 선호도를 0~1 사이의 숫자(q값)로 표현할 수 있습니다. 값이 클수록 선호도가 높습니다. 기본값은 1입니다.

**예시:** `Accept-Language: ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7`
- `ko-KR`: q=1 (기본값, 가장 선호)
- `ko`: q=0.9 (두 번째 선호)
- `en-US`: q=0.8 (세 번째 선호)
- `en`: q=0.7 (네 번째 선호)

### 4-3. 협상과 우선순위

1. **구체적인 것이 우선합니다.** `text/html`은 `text/*`보다 우선적으로 고려됩니다.
2. **선호도(q값)가 높은 것이 우선합니다.**

예를 들어, 다음과 같은 요청:
```http
Accept: text/*, text/plain, text/plain;format=flowed, */*
```

서버는 우선순위에 따라:
1. `text/plain;format=flowed`
2. `text/plain`
3. `text/*`
4. `*/*`

순으로 고려합니다.

## 전송 방식

HTTP에는 데이터를 전송하는 여러 방식이 있습니다. 각 방식은 특정 상황에 맞게 선택됩니다.

### 5-1. 단순 전송 (Content-Length)

서버가 응답 데이터의 정확한 길이를 알고 있을 때 사용합니다.

```http
HTTP/1.1 200 OK
Content-Type: text/html;charset=UTF-8
Content-Length: 3423

<html>
  <body>...</body>
</html>
```

### 5-2. 압축 전송 (Content-Encoding)

데이터를 압축하여 전송함으로써 네트워크 대역폭을 절약합니다.

```http
HTTP/1.1 200 OK
Content-Type: text/html;charset=UTF-8
Content-Encoding: gzip
Content-Length: 521

gzipped-data
```

### 5-3. 분할 전송 (Transfer-Encoding)

데이터를 여러 청크(chunk)로 나누어 순차적으로 전송합니다. 전체 데이터 크기를 미리 알 수 없을 때 유용합니다.

```http
HTTP/1.1 200 OK
Content-Type: text/plain
Transfer-Encoding: chunked

5
Hello
5
World
0

```

각 청크는 크기(16진수) + 개행 + 내용 + 개행으로 구성되며, 마지막은 0 크기 청크로 표시합니다.

**특징:**
- Content-Length를 사용하지 않습니다.
- 데이터를 순차적으로 생성하면서 전송할 수 있습니다.
- 스트리밍에 적합합니다.

### 5-4. 범위 전송 (Range, Content-Range)

클라이언트가 특정 범위의 데이터만 요청할 수 있습니다. 대용량 파일 다운로드 중 중단된 경우 이어받기 등에 유용합니다.

**요청:**
```http
GET /file.zip HTTP/1.1
Range: bytes=1001-2000
```

**응답:**
```http
HTTP/1.1 206 Partial Content
Content-Type: application/zip
Content-Range: bytes 1001-2000/67589
Content-Length: 1000

partial-content
```

**특징:**
- 클라이언트는 `Range` 헤더로 특정 바이트 범위를 요청합니다.
- 서버는 `Content-Range` 헤더로 응답하고 상태 코드 206(Partial Content)를 반환합니다.
- 대용량 파일을 나누어 다운로드하거나 이어받기에 유용합니다.

## 캐시 검증 헤더

웹 캐시는 네트워크 트래픽을 줄이고 웹사이트 성능을 개선하기 위해 중요합니다. 캐시 검증 헤더는 캐시된 리소스가 여전히 유효한지 확인하는 데 사용됩니다.

### 6-1. 검증 헤더의 필요성

- 네트워크 대역폭 절약
- 빠른 응답 시간
- 서버 부하 감소

캐시 검증 없이는 리소스가 변경되었는지 확인하기 위해 전체 리소스를 다시 다운로드해야 합니다. 검증 헤더를 사용하면 변경되지 않은 경우 리소스를 다시 다운로드하지 않고 캐시된 버전을 사용할 수 있습니다.

### 6-2. ETag (Entity Tag)

**리소스의 특정 버전에 대한 식별자입니다.** 서버는 리소스의 내용이 변경될 때마다 새로운 ETag를 생성합니다.

```http
ETag: "33a64df551425fcc55e4d42a148795d9f25f89d4"
```

**역할:** 
- 리소스의 정확한 버전을 식별합니다.
- 아주 미세한 변경도 감지할 수 있습니다.

**특징:**
- 서버는 고유한 해시값이나 버전 번호를 사용하여 ETag를 생성합니다.
- 파일의 내용이 변경되면 ETag도 변경됩니다.
- Last-Modified보다 더 정밀한 비교가 가능합니다.

### 6-3. Last-Modified

**리소스가 마지막으로 수정된 시간을 나타냅니다.**

```http
Last-Modified: Wed, 21 Oct 2023 07:28:00 GMT
```

**역할:**
- 리소스의 수정 시간을 기준으로 변경 여부를 판단합니다.
- 대략적인 변경 감지에 적합합니다.

**특징:**
- 초 단위까지만 정밀도를 제공합니다.
- 1초 내에 여러 번 변경된 경우 감지하지 못할 수 있습니다.
- 변경 시간만으로 판단하므로 내용이 실제로 변경되지 않았어도 시간이 변경되면 다른 리소스로 간주합니다.

### 6-4. 조건부 요청 헤더

클라이언트는 이전에 받은 ETag나 Last-Modified 값을 사용하여 조건부 요청을 보낼 수 있습니다. 이를 통해 리소스가 변경되었을 때만 새로운 리소스를 받을 수 있습니다.

#### If-None-Match

**이전에 받은 ETag를 서버에 전달하여 리소스가 변경되었는지 확인합니다.**

```http
If-None-Match: "33a64df551425fcc55e4d42a148795d9f25f89d4"
```

**역할:**
- 캐시 검증에 사용됩니다.
- ETag 값이 일치하지 않으면(변경되었으면) 새 리소스를 받습니다.

**작동 방식:**
1. 클라이언트가 ETag와 함께 리소스를 받음
2. 나중에 같은 리소스를 요청할 때 `If-None-Match`에 이전 ETag 값을 포함
3. 서버는 현재 리소스의 ETag와 비교
4. 일치하면(변경 없음): 304 Not Modified 응답 (본문 없음)
5. 일치하지 않으면(변경됨): 200 OK와 새 리소스 전송

#### If-Modified-Since

**이전에 받은 Last-Modified 시간을 서버에 전달하여 리소스가 그 이후로 수정되었는지 확인합니다.**

```http
If-Modified-Since: Wed, 21 Oct 2023 07:28:00 GMT
```

**역할:**
- 시간 기반으로 캐시 검증을 수행합니다.
- 지정된 시간 이후에 수정되었으면 새 리소스를 받습니다.

**작동 방식:**
1. 클라이언트가 Last-Modified와 함께 리소스를 받음
2. 나중에 같은 리소스를 요청할 때 `If-Modified-Since`에 이전 Last-Modified 값을 포함
3. 서버는 리소스의 최신 수정 시간과 비교
4. 수정되지 않았으면: 304 Not Modified 응답 (본문 없음)
5. 수정되었으면: 200 OK와 새 리소스 전송

### 6-5. 조건부 변경 요청 헤더

조건부 변경 요청 헤더는 안전한 리소스 업데이트를 위해 사용됩니다. 이를 통해 여러 클라이언트가 동시에 같은 리소스를 수정할 때 충돌을 방지할 수 있습니다.

#### If-Match

**지정된 ETag와 서버의 리소스 ETag가 일치할 때만 요청을 처리합니다.**

```http
If-Match: "33a64df551425fcc55e4d42a148795d9f25f89d4"
```

**역할:**
- 같을 때만 수정을 허용합니다.
- 낙관적 잠금(Optimistic Locking)을 구현하는 데 사용됩니다.

**활용 예시:**
1. 클라이언트 A와 B가 동시에 같은 리소스를 가져옴 (ETag: "abc123")
2. 클라이언트 A가 수정하고 `If-Match: "abc123"`과 함께 PUT 요청
3. 서버는 요청을 처리하고 새 ETag "def456"을 반환
4. 클라이언트 B가 수정하고 `If-Match: "abc123"`과 함께 PUT 요청
5. 서버의 현재 ETag는 "def456"이므로 일치하지 않음
6. 서버는 412 Precondition Failed 응답

#### If-Unmodified-Since

**지정된 시간 이후로 리소스가 수정되지 않았을 때만 요청을 처리합니다.**

```http
If-Unmodified-Since: Wed, 21 Oct 2023 07:28:00 GMT
```

**역할:**
- 수정되지 않았을 때만 수정을 허용합니다.
- 시간 기반의 낙관적 잠금을 구현합니다.

**활용:**
- 파일 업로드 재개
- 안전한 리소스 업데이트
- 동시성 제어

### 6-6. 캐시 검증 예시

#### ETag/If-None-Match 예시:

**첫 번째 요청:**
```http
GET /resource HTTP/1.1
Host: example.com
```

**첫 번째 응답:**
```http
HTTP/1.1 200 OK
Content-Type: text/html
ETag: "33a64df5"
Content-Length: 1024

<html>...</html>
```

**두 번째 요청 (캐시 검증):**
```http
GET /resource HTTP/1.1
Host: example.com
If-None-Match: "33a64df5"
```

**두 번째 응답 (변경 없음):**
```http
HTTP/1.1 304 Not Modified
ETag: "33a64df5"
```

#### Last-Modified/If-Modified-Since 예시:

**첫 번째 요청:**
```http
GET /resource HTTP/1.1
Host: example.com
```

**첫 번째 응답:**
```http
HTTP/1.1 200 OK
Content-Type: text/html
Last-Modified: Wed, 21 Oct 2023 07:28:00 GMT
Content-Length: 1024

<html>...</html>
```

**두 번째 요청 (캐시 검증):**
```http
GET /resource HTTP/1.1
Host: example.com
If-Modified-Since: Wed, 21 Oct 2023 07:28:00 GMT
```

**두 번째 응답 (변경 없음):**
```http
HTTP/1.1 304 Not Modified
Last-Modified: Wed, 21 Oct 2023 07:28:00 GMT
```

## 정리

HTTP 헤더는 웹 통신에서 중요한 메타데이터 역할을 합니다. 표현 헤더를 통해 데이터의 형식과 특성을 정의하고, 콘텐츠 협상을 통해 클라이언트가 선호하는 표현을 서버가 제공할 수 있습니다. 또한 다양한 전송 방식을 통해 효율적인 데이터 전송이 가능합니다.

캐시 검증 헤더는 네트워크 효율성을 크게 향상시키며, ETag와 Last-Modified를 통해 리소스의 변경 여부를 정확하게 판단할 수 있습니다. 조건부 요청 헤더(If-None-Match, If-Modified-Since)와 조건부 변경 요청 헤더(If-Match, If-Unmodified-Since)는 리소스의 효율적인 전송과 안전한 업데이트를 가능하게 합니다.

다음 파트에서는 일반 정보 헤더, 특별한 정보 헤더, 인증, 그리고 쿠키 관련 헤더에 대해 알아보겠습니다.