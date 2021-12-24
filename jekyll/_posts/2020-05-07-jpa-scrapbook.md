---
title: JPA Scrapbook
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
tags:
- JPA
- Scrapbook
toc: true
toc_sticky: true
markdown: kramdown
highlighter: rouge
categories:
- Scrapbook
---

# [@Entity 모델 객체는 Serializable을 꼭 구현해야 하나?](https://everydayminder.wordpress.com/2013/07/10/entity-%EB%AA%A8%EB%8D%B8-%EA%B0%9D%EC%B2%B4%EB%8A%94-serializable%EC%9D%84-%EA%BC%AD-%EA%B5%AC%ED%98%84%ED%95%B4%EC%95%BC-%ED%95%98%EB%82%98/)

~ cannot be cast to java.io.Serializable
at org.hibernate.type.ManyToOneType.hydrate(ManyToOneType.java:188) ~ 와 같은 에러가 발생하여 검색 



