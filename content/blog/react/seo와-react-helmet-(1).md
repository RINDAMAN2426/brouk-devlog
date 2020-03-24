---
title: SEO - sitemap작성 (1)
date: 2020-03-24 22:03:64
category: react
draft: false
---

## Reference

##### [delivan.dev](https://delivan.dev)

> 이 포스팅은 delivan님의 글을 읽고 제 개인적인 학습 정리 용도로 적었기 때문에 원본 글을 꼭 참고 하길 바랍니다.

---

gatsby blog를 만들면서 구글 검색에도 내 포스팅이 올라오면 좋을 것 같아서 알아보던 중 SEO의 개념에 대해서 처음 알게 됬습니다.

간략하게 말하면 구글에 내 사이트가 검색되기 위해서는 `sitemap.xml` 파일을 작성해서 내 사이트에 대해서 알리고 구글 엔진이 크롤링 하여 색인 등록이 되면 비로소 완료되는 것이다.
즉 `sitemap`은 사이트의 설계도 및 지도 역할을 하는 파일입니다.

`sitemap.xml` 작성을 도와주는 https://www.web-site-map.com/ 이라는 사이트도 있지만 gatsby의 경우 작성을 도와주는 library가 있습니다..

## gatsby-plugin-sitemap

```sh
$ yarn add gatsby-plugin-sitemap
```

설치를 완료 했으면 `gatsby-config.js` 열어 plugins에 추가 하도록 합니다.

제 경우 gatsby-statrt-bee 템플릿을 사용했기 때문에 별도의 config 파일은 수정이 필요 없었습니다.

##### gatsby-config.js

```json
module.exports = {
  ...
  plugins:[
    ...
    'gatsby-plugin-sitemap',
  ]
}
```

> `gatsby-starter-bee` 템플릿을 사용할 경우 아래 meta-config 설정도 해주시길 바랍니다.

##### gatsby-meta-config.js

```json
module.exports = {
  ...
  siteUrl: `your blog url`
  ...
  ga: `google analytics tracking ID`
}
```

siteUrl을 배포한 블로그 URL로 변경해주고 ga는 추후에 설명하겠지만 자신의 Google Analytics 추적 ID를 작성하면 됩니다.

템플릿 코드에 주석처리로 설명이 잘 되있어서 참고를 꼭 하길 바랍니다.

## robots.txt

```sh
yarn add gatsby-plugin-robots-txt
```

'robots.txt' 은 크롤러가 어디까지 크롤링을 하게 해줄지 크롤링 봇에게 전달할 정보를 작성하는 파일입니다.
위 library를 추가하면 옵션만 지정하면 build 폴더에 자동으로 robots.txt 파일을 만들어 주는 걸 확인할 수 있습니다.

##### gatsby-config.js

```json
module.exports = {
  ...
  plugins: [
    ...
    {
      resolve: 'gatsby-plugin-robots-txt',
      options: {
        host: 'your blog url',
        sitemap: 'your blog url/sitemap.xml',
        policy: [{userAgent: '*', allow: '/}]
      }
    }
  ]
}
```

host에는 본인의 url sitemap에는 `/sitemap.xml`을 붙여주면 됩니다.

`user-agent`는 어떤 크롤러 봇이 접근할 지 지정하는 옵션이고 allow는 어떤 url접근을 허용할지, disallow는 접근 거부 url을 명세하는 옵션이다.

위와 같이 작성한 후에는 Google Search Console에 URL과 sitemap을 등록해주면 됩니다.

Google Search Console 등록을 위해선 `google-site-verification`을 meta태그에 등록을 해야하는데

이 부분부터는 `react-helmet`과 함께 다음 포스트에 작성하도록 하겠습니다.
