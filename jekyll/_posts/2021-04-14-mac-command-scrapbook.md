---
title: Mac Command Scrapbook
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
tags:
- Mac
- Command
- Scrapbook
toc: true
toc_sticky: true
markdown: kramdown
highlighter: rouge
categories:
- Scrapbook
---

# [특정 포트를 사용중인 프로세스 확인/종료](https://new93helloworld.tistory.com/138)
```shell
sudo lsof -i :"port number"
sudo kill -9 "process id"                                                                                  
```
