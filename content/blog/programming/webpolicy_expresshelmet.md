---
title: WebSecurity (CORS+XSS+CSRF)
date: 2021-10-19 18:00
category: programming
draft: false
---

Express helmet이 지원하는 메소드들과 연관된 정책을 정리해보자.

​https://first-party-test.glitch.me/ 이 사이트를 이용하면 직접 헤더들을 테스트해볼 수 있다.

## Content Security Policy

- XSS, ClickJacking, Injection 등을 예방하기 위한 표준
- 현 사이트 내에서 사용할 수 있는 컨텐츠들에 대한 정의
- 헤더에 `Content-Security-Policy` 지정 혹은 `<meta http-equiv="Content-Security-Policy" ...>` 를 도큐먼트에 저장해야한다.
- `strict-dynamic` hash값 혹은 nonce값을 이용해서 명시적으로 신뢰할 수 있음을 지정하는 것 같다. 하위 스크립트들에게 이 신뢰도는 전파된다.

### Reference

https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy

https://cheatsheetseries.owasp.org/cheatsheets/Content_Security_Policy_Cheat_Sheet.html

## Cross Origin Embedder Policy

- `unsafe-none` : `CORP` 혹은 `CORS`로 명시적 권한 부여 없이 교차 출처 리소스를 가져올 수 있도록 한다.
- `require-corp` : `CORP` 혹은 `CORS`로 명시적으로 표시된 origin의 리소스만 가져올 수 있다.

### Reference

https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cross-Origin-Embedder-Policy

## Cross Origin Opener Policy

- 교차 출처 document가 새창에서 열렸을 경우 opener에 이전 document의 context 공유 여부에 대한 정책
- `unsafe-none` opene에서 context를 공유한다.
- `same-origin-allow-popups` 새 탭, 창에서는 context를 공유한다. (해당 창에서도)
- `same-origin` same-origin이 아니면 context가 공유되지 않는다.

**예시**

`A사이트`는 무조건 unsafe-none, B사이트는 케이스 별로 다르게

1. `B사이트`가 `unsafe-none`

   - `A사이트`에서 `window.open("B")` 를 했을 때
     - `B사이트` 에서 `window.opener`는 `A사이트`의 context를 참조한다. (내부 변수 값은 접근 안됨)
   - `B사이트`에서 `window.open("A")` 를 했을 때

     - `A사이트`는 `window.opener`는 B사이트의 context를 참조할 수 있다.(내부 변수 값은 접근 안됨)

   - `B사이트` 가 `window.open("B")` 를 했을 때
     - 새 창의 B사이트는 `window.opener`는 이전 창의 사이트의 context를 참조할 수 있다. 이 전 창에서 `window.a = "hello world"` 라는 값을 세팅하였다면 새 창에서는 `window.opener.a` 로 접근할 수 있다.

2) `B 사이트`가 `same-origin-allow-popups`

   - `A사이트`에서 `window.open("B")` 를 했을 때

     - `B사이트`에서는 `window.opener`는 `null`이되어 `A사이트`의 `context`를 참조하지 않는다.

   - `B사이트`에서 `window.open("A")` 를 했을 때

     - `A사이트`는 `window.opener`는 B사이트의 context를 참조할 수 있다.(내부 변수 값은 접근 안됨)

   - `B사이트` 가 `window.open("B")` 를 했을 때
     - 새 창의 B사이트는 `window.opener`는 이전 창의 사이트의 context를 참조할 수 있다. 이 전 창에서 `window.a = "hello world"` 라는 값을 세팅하였다면 새 창에서는 `window.opener.a` 로 접근할 수 있다.

3. `B 사이트`가 `same-origin`

   - `A사이트`에서 `window.open("B")` 를 했을 때

     - `B사이트`에서는 `window.opener`는 `null`이되어 `A사이트`의 `context`를 참조하지 않는다.

   - `B사이트`에서 `window.open("A")` 를 했을 때

     - `A사이트`는 `window.opener`는 null이 된다. (`same-origin-allow-popups`와의 차이점)

   - `B사이트` 가 `window.open("B")` 를 했을 때
     - 새 창의 B사이트는 `window.opener`는 이전 창의 사이트의 context를 참조할 수 있다. 이 전 창에서 `window.a = "hello world"` 라는 값을 세팅하였다면 새 창에서는 `window.opener.a` 로 접근할 수 있다.

내 사이트의 `context`가 다른 사이트의 `opener`에서 참조된다면, `tab nabbing` 공격에 취약점이 있을 수 있다. `window.opener.location` 을 이전 사이트와 유사한 사이트로 변경 한 후에 고객이 데이터를 다시 입력하게 된다면 그 데이터는 해커에게 넘어갈 수 있다.

`anchor 태그`에서 `target="_blank"`를 할 때 `noopener`를 적용해주긴 하지만 신경쓸 수 있도록 하자.

### Reference

https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cross-Origin-Opener-Policy

https://web.dev/coop-coep/ (여기 그림이 이해가는데 쉽게 도와준다)

https://tech.lezhin.com/2017/06/12/tabnabbing

https://xsleaks.dev/docs/defenses/opt-in/coop/ (여기 꺼 정리 글들이 내 이해에는 더 도움이 되었다)

## Cross Origin Resource Policy

특정 리소스들이 다른 origin으로 부터 로드되는 것을 방지 할 수 있는 정책이다.

- `same-site`
  - domain이 같은 경우 허용해주는 옵션이다. https://example1.domain.com 과 https://example2.domain.com 은 same site이다.
- `same-origin`
  - `origin`이 같아야 한다. (`scheme` + `host` + `port`)
- `cross-origin`
  - 다른 origin으로 부터 요청도 읽을 수 있다. `COEP`와 같이 사용되어야 유용하다.
  - 해당 리소스의 `Access-Control-Allow-Origin`도 설정되어야 한다. (`CORS`)

### Reference

https://developer.mozilla.org/en-US/docs/Web/HTTP/Cross-Origin_Resource_Policy_(CORP)

https://xsleaks.dev/docs/defenses/opt-in/corp/

## Expect-CT

CT위반사항에 대해서 정해진 시간 만큼 리포팅을 하는 옵션인것 같다.. 사실 CT에 대해 잘 몰라서 이부분은 추후에 다시 정리를 해야 할 것 같다.

- `max-age` 리포팅(expect) 기간, second 단위
- `report-uri` failure 케이스에 대해 수집할 URI
- `enforce` 위반 하는 경우 향후 연결을 거부해야함을 UA(browser)에게 알림

### Reference

https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Expect-CT

## Referrer Policy

Referrer 헤더에 얼마나 많은 정보를 담을지 지정하는 정책이다.

자세한 옵션들은 MDN을 한번 쭉 보는게 나을 듯 하다.

### Reference

https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referrer-Policy

## HSTS (Http Strict Transport Security)

반드시 HTTP 대신 HTTPS를 사용해야 함을 브라우저에게 알리는 정책

HTTPS로 접근한 적이 있다면 이후 브라우저는 자동으로 HTTPS만 사용하게 되어, `man in the middle` 공격에 있어 방지를 해줄 수 있다.

- `max-age` 알리는 기간(second)
- `includeSubDomains` 하위 도메인에게도 규칙을 적용하겠다는 값
- `preload` ...??

### Reference

https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/Strict-Transport-Security

## X-Content-Type-Options

`MIME 타입` 이외의 `Content-Type`에 대해선 해석을 제한할 수 있는 옵션이다. (`MIME Sniffing` 방지)

### Options

`nosniff`

### Reference

https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Content-Type-Options

## Origin-Agent-Cluster

일단 브라우저 호환성은 안좋다. `chrome` 88버전 이후, `firefox`에서는 해봤는데 안된다.

`same-site` 이지만 `cross-origin`간의 `synchronous scripting access` 를 방지해주는 옵션이다.

즉 `mainsite.com` 내부의 `chat.mainsite.com`간의 리소스 독립성을 두어 서로 간의 영향이 가지 않도록 하는 옵션.

`?1` 의 형태로 `boolean` 값으로 넣는다.

### Reference

https://web.dev/origin-agent-cluster/

https://www.python2.net/questions-1185531.htm

## X-DNS-Prefetch-Control

다른 도메인에 연결할 때 해당 dns에 대한 handshake를 미리 수행하여 리소스 요청에 있어서 수행 시간을 줄이는 방법이다.

`on`, `off` 값으로 제어할 수 있다.

### Reference

https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-DNS-Prefetch-Control

https://couplewith.tistory.com/entry/%EA%BF%80%ED%8C%81-%EC%9B%B9%EC%84%9C%EB%B9%84%EC%8A%A4-%EC%84%B1%EB%8A%A5-%EC%B5%9C%EC%A0%81%ED%99%94-dns-prefetch

## X-Download-Options

갓갓 IE8에만 적용되는 옵션. ~~어차피 없어져버릴 IE니까 무시해버리기로 하자~~

`potentially-unsafe content` 에 대해서 브라우저에서 바로 열리는 걸 막고 저장하도록 해준다.

옵션은 `noopen` 밖에 없다.

https://www.nwebsec.com/HttpHeaders/SecurityHeaders/XDownloadOptions
갓갓 IE8을 가지고 계시다면 여기가서 테스트해보면 될거같다..

## X-Frame-Options

해당 페이지를 `iframe` 등에서 렌더링 할 수 있는지에 여부에 대해서 나타낸다. `ClickJacking`을 막는데 쓰인다.

메타태그로는 안된다. 무조건 HTTP 헤더에 설정되어야 한다.

### Reference

https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/X-Frame-Options

## X-Permitted-Cross-Domain-Policies

웹 클라이언트 (Adobe Flash 같은 애들)가 도메인을 넘어 데이터를 다룰수 있는 지에 대한 정책 파일 (예`crossdomain.xml`)이 허용 되었는지에 대해 명시한다.

### Options

- `none` 어디에도 해당 정책 파일이 없음을 명시

- `master-only` master 정책 파일만 가능

- `by-content-type` : `Content-Type: text/x-cross-domain-policy` 와 함께 제공되는 정책 파일만 허용

- `by-ftp-filename` file명이 `crossdomain.xml`로 끝나는 URL만 허용

- `all` 모든 정책파일이 허용

### Reference

https://owasp.org/www-project-secure-headers/

## X-Powered-By

hosting혹은 framework에 의해 설정될 수 있다. 특정 버전을 노린 공격이 있을 수도 있으므로 왠만하면 이 헤더는 삭제할 수 있도록 하자.

### Reference

https://developer.mozilla.org/ko/docs/Web/HTTP/Headers

## X-XSS-Protection

CSP를 이용해 inline script를 방지할 수 있으나, 이를 지원하지 않는 구형 웹브라우저를 위함. (그래서 IE 8이랑 사파리밖에 지원을 안한다 ㅎㅎ 역시 두 브라우저.. 용호상박..)

XSS 공격이 감지되면 payload를 중지한다.

### Reference

https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/X-XSS-Protection
