---
title: Restful API
date: 2020-04-20 11:04:79
category: programming
draft: false
---

# Reference

- [Medium(@dydrlaks) - REST API ?](https://medium.com/@dydrlaks/rest-api-3e424716bab)
- [Velog(@stampid) - REST API 와 RESTful API](https://velog.io/@stampid/REST-API%EC%99%80-RESTful-API#1-rest%EB%9E%80)

# REST API

## REST

`REST(Representational State Transfer)`는 HTTP기반으로 필요한 자원에 접근하는 방식에 대한 아키텍쳐입니다.

## REST의 속성

- 모든 resource는 고유 URI를 가지고 있다.
- 모든 request는 클라이언트가 요청할 때마다 필요한 정보를 주기 때문에 세션정보를 보관할 필요는 없다.
- HTTP Method (GET, POST, PUT, DELETE)를 통하여 접근되어야 한다.
- 서비스 내 하나의 resource가 주변에 연관된 resources와 연결되어 표현되어야 한다.

## REST의 구성요소

- Resource : URI
- HTTP Method : GET, POST, PUT, DELETE
- Message : Header (Body의 Data format), Body (자원에 대한 정보)

> Header는 `Accept`라는 요청 헤더와 `Content-type`이라는 응답 헤더로 이루어져있습니다.

## REST의 특징

Rest의 속성으로부터 비롯되는 특징은 다음과 같습니다.

- Uniform Interface : 어떠한 플랫폼에서 사용하여도 HTTP 표준만 따른다면 사용가능합니다.
- Stateless : 세션의 정보를 보관하지 않습니다. 각각의 요청은 완전히 다른 것으로 인식하고 처리를 합니다.
- Cacheable : HTTP의 캐싱 기능 적용이 가능합니다.
- Self-descriptiveness : Rest API 메세지만 보고도 쉽게 이해할 수 있습니다.
- Client-Server : 자원을 보유한 쪽이 Server, 자원을 요청하는 쪽이 Client입니다. 각각의 역할 구분이 명확하여 개발도 명확해집니다.
- Layerd System : 로드 밸런싱, 암호화, 사용자 인증 등을 추가하여 다중 계층 구조로 유연하게 설계할 수 있습니다.
