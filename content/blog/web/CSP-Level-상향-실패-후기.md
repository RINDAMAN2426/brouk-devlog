---
title: CSP Level 상향 실패 후기
date: 2021-12-02 00:37:00
category: web
draft: false
---

CSP Level을 3로 올리려 했으나 실패하고 나서 후기이다..

## 목적

1. `nonce`사용

- 레거시에서 `nonce`를 사용하려던 흔적이 있었으며, `nonce`를 사용하면 `named host`를 사용하지 않아도 `script`를 제어할 수 있으며, 외부에 서비스가 어떤 외부 사이트로부터 스크립트를 받아 오는지 감출 수가 있었다.

2. `strict-dynamic` 사용

- 외부의 분석 툴, GTM 등을 보다 효율적으로 쓰기 위해선 단순히 해당 스크립트만 불러올 문제가 아니라 그 모듈들이 실행하는 `inline-script`를 허용해주어야 할 필요가 있었다.

## 첫 번째 실패

- `Firefox`와 `Chrome`에서는 이슈가 없었다. `Firefox`, `Chrome`은 `CSP Level 3`를 지원하기 때문에, 위의 조합으로 안전하게 핸들링 할 수 있었다.

- `Safari`에서 문제가 발생했다. `strict-dynamic`은 `CSP Level 3`에서 서술된 스펙이지만, `Level 3`는 `LTS(?)`가 아니기 때문에 `Safari`는 `Level 2`까지 지원을 해준다. 덕분에 `script`가 모조리 실행이 안되, 텅빈 사이트를 구경할 수 있었다.

## 첫 번째 개선

- `self`와 `unsafe-inline`등을 `fallback`으로 사용하려 했다. [이 레퍼런스](https://content-security-policy.com/strict-dynamic/)를 참고하였는데 내가 영어 해석이 부족해서 잘 이해를 못한 건지.. 어쨌든 지원할 수 없는 형태의 `CSP` 구성이었다.

- 원인은 간단하다. `Level 2 Browser`는 일단 `strict-dynamic`을 사용할 수 없다. 또한 해당 브라우저에서 `nonce`를 사용하면 `unsafe-inline` 은 `ignored`다. 접근부터 잘못됐었다.

- 스오플을 뒤지다가 누군가 잘 정리해준 게 있어서 공유.. 참고하면 한눈에 파악하기 쉽다.

- https://speakerdeck.com/mikispag/content-security-policy-a-successful-mess-between-hardening-and-mitigation?slide=52

## 두 번째 개선

- 이슈도 알았고, 그럼 어떻게 해결할 것인가? `Safari`가 지원을 안해준다하니까 `Safari`만 별도의 `CSP` 정책을 가져가도록 하자! (실제로 이렇게 하는 사람들이 있었나보다 뒤늦게나마 다른 사례들도 보니까.. 이렇게 하라고 하는 사람도 있었다)

- 하지만 역시 쉽게 될리가 없다. 이슈가 터졌다.

- 인앱 웹뷰가 문제였다. `Instagram`, `Facebook`, `KakaoTalk` 전부다 `Level 3` 정책이 적용되고 있다. 이래선 안된다... (그 와중에 `Naver`는 잘 된다. 앱도, 인앱웹뷰도... 라이언을 좋아하지만 `Naver`가 더 좋은 회..)

- 갓갓 `UA`를 이용해서 `Safari`만 걸러내려했는데, 웹뷰 `UA`까지 쓰기엔 너무나 불안전하다.

- 차라리 `Chrome`, `Firefox` 혹은 `Client Hints`를 제공하는 갓갓 브라우저만 걸러서 정책을 상향할까도 생각했다...

## 결론

- 결론은 결국 `nonce`, `strict-dynamic` 도입은 취소했다.

- 한다해도 지원 브라우저만 별도로 적용하는게 맞을 것 같고.. 하지만 나중에 괜히 혼란만 야기할까봐.. 좀 더 정리가 되고 나면 그 때 다시 적용을 시도해볼까 한다...

- `github`을 보니까 이런 스크립트를 모두 핸들링하는 별도의 서버를 두고 해당 서버만 `named host`로 열어두는 것 같다. 모두 다 자체 구현하는 날이 온다면... 당연히 베스트 방안일 것 같다...

- 이거한다고 다른 것까지 구조 개선을 들어갔었는데.. 아쉬웠다..
