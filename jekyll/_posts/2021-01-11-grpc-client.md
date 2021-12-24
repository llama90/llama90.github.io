---
title: grpc 클라이언트
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
tags:
- gRPC
- grpcurl
- grpcui
- milkman
toc: true
toc_sticky: true
markdown: kramdown
highlighter: rouge
categories:
- Practice
---

# 들어가면서
REST 기반 API를 작성하면 명령행 기반의 `curl` 및 `postman` 애플리케이션를 사용하여 테스트해보는 것처럼, 
gRPC의 경우 명령행 기반의 [grpcurl](https://github.com/fullstorydev/grpcurl),
gRPC 서버를 지정해주면, proto 명세를 기반으로 테스트 가능한 웹페이지를 구성해주는 [grpcui](https://github.com/fullstorydev/grpcui) 
및 postman과 유사한 인터페이스를 가진 [milkman](https://github.com/warmuuh/milkman)이 있다.

# grpcurl
명령행 기반으로 `curl`과 유사하다. 

# grpcui
서비스 정의(Service Definition)을 기반으로, Swagger처럼 명세도 확인할 수 있는 웹서비스를 구성해준다. 

```
$ grpcui -plaintext localhost:9080
gRPC Web UI available at http://127.0.0.1:53637/
```

![grpcui browser](https://user-images.githubusercontent.com/6668548/104129066-50cd9f80-53ae-11eb-8613-d28a2d8acb0d.png)

# milkman 
gRPC를 지원하는 postman과 유사한 API 테스트 애플리케이션으로 보인다. 설치형 애플리케이션이고 macOS 기준으로 `brew` 이용해서 쉽게 설치할 수 있다. 
특이한 점은 이러한 gRPC 테스트를 지원하는 클라이언트 프로그램들이 go언어 기반인데, `milkman`의 경우 java로 개발되었다.   

![milkman](https://user-images.githubusercontent.com/6668548/104129069-54612680-53ae-11eb-9d5e-b54a2d194b2e.png)

# Reference
* [awesome-grpc](https://github.com/grpc-ecosystem/awesome-grpc)
* [grpcurl](https://github.com/fullstorydev/grpcurl)
* [grpcui](https://github.com/fullstorydev/grpcui)
* [milkman](https://github.com/warmuuh/milkman)
