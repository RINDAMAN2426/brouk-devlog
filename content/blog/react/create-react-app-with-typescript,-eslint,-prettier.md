---
title: Typescript CRA 프로젝트 eslint, prettier 설정
date: 2020-03-23 13:03:85
category: 'react'
draft: false
---

# Reference

### [Medium - Create react app typescript: eslint and prettier](https://medium.com/@feralamillo/create-react-app-typescript-eslint-and-prettier-699277b0b913)

> 원본 내용을 토대로 필요한 부분만 정리하였습니다.

## 1. CRA Project 시작하기

```sh
$ npx create-react-app [project-name] --template typescript
```

> create-react-app을 따로 설치해도 되나 최신버전에 맞춰서 생성하기 위해서 `npx`를 사용하자

## 2. Dependency 설치

```sh
$ yarn add -D eslint eslint-config-airbnb eslint-config-airbnb-typescript eslint-config-prettier eslint-config-react-app eslint-import-resolver-typescript eslint-loader eslint-plugin-flowtype eslint-plugin-import eslint-plugin-jsx-a11y eslint-plugin-react eslint-plugin-react-hooks  @typescript-eslint/parser @typescript-eslint/eslint-plugin prettier prettier-eslint prettier-eslint-cli eslint-plugin-prettier
```

> 원본 글에서는 `babel-eslint`도 설치를 하나 설치시 CRA로 설정한 webpack과 충돌이 일어나기 때문에 제외 하고 설치해주자. `Dependencis`를 보면 기존의 eslint 설치와 비슷한 맥락이다.

## 3. Config 관련 설정

### .eslintrc

```json
{
  "plugins": ["prettier", "@typescript-eslint"],
  "extends": ["airbnb-typescript", "react-app", "prettier"],
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "project": "./tsconfig.json"
  },
  "rules": {
    "import/no-cycle": 0
  }
}
```

> 기존 eslint 설정에서 parser 부분만 `typescript`에 맞춰 적용해주면 된다.
> rules는 최대한 건드리지 않는 방식으로 하고 싶어서 원본내용에서 많이 삭제했다.

### .eslintignore

```
build/*
public/*
src/react-app-env.d.ts
src/serviceWorker.ts
```

### .prettierrc

```json
{
  "printWidth": 80,
  "singleQuote": true,
  "trailingComma": "es5",
  "tabWidth": 2
}
```

여기까지 설정하면 vscode에서 실시간으로 eslint, prettier 관련 이슈를 잡아주는 걸 확인할 수 있다. 물론 당연히 관련 `EXTENSION`은 전부 설치 해주어야 한다.
다음에는 CRA로 만드는 프로젝트가 아니라 기본 javascript 프로젝트에서 설정하는 법을 포스팅 해두어야겠다.
