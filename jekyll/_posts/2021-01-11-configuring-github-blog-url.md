---
title: github 블로그 URL 수정
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
tags:
- github blog
- URL
toc: true
toc_sticky: true
markdown: kramdown
highlighter: rouge
categories:
- Trouble Shooting
---

github 블로그로 구성된 사이트들을 보다 보면 URL 주소가 자체 도메인을 사용하지 않는 경우 `username.github.io`로 되어 있는 것을 봐왔었다. 
그런데 내가 만든 블로그의 경우 URL 형식이 `username.github.io/username`으로 접근해야 했고, 문제를 인지하다 이제서야 고쳤다.  

블로그에 대한 저장소 이름을 `username`(lucaseo90)로 했었는데, `username.github.io`(lucaseo90.github.io) 형식으로 저장소를 변경해주니까 해결됐다.

이와 관련해서 jekyll 주소도 `username.github.io/username`에서 `username.github.io`로 모두 수정해줬다. 

# Reference
* [쉽고 빠르게 수준 급의 GitHub 블로그 만들기](https://dreamgonfly.github.io/blog/jekyll-remote-theme/)