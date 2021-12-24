---
title: Kong을 이용한 Gateway 테스트
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
tags:
- Kong
- API Gateway
toc: true
toc_sticky: true
markdown: kramdown
highlighter: rouge
categories:
- Practice
---

# 들어가면서
Kong Gateway를 이용한 gRPC 게이트웨이와 관련된 기능 테스트를 진행하는데 의문이 가는 부분이 생겨 기본적인 테스트를 진행했다.

# 실습
진행하고자 하는 테스트는 서버 2대가 실행되어 있을 때, Kong Gateway에서 각각 서버에 라우트 할 수 있도록 서비스 및 라우트를 등록하여 요청했을 때 의도하는데로 값을 조회해 올 수 있는지 확인하고자 하였다.

설치는 [Docker 환경에서 Kong 설치](https://lucaseo90.github.io/practice/docker-kong/) 환경과 같다.

실습을 위해 REST API 요청 가능한 서버가 두개 필요하여, [json-server](https://github.com/typicode/json-server) 를 활용했다. 

## JSON Server 스키마 정의
json-server가 실행될 때, 조회 가능한 데이터를 스키마로 정의할 수 있는데 각각 다음과 같이 구성했다.

각각 스키마를 blog.json 및 commerce.json이라고 정의했다.

```json
{
  "posts": [
    { "id": 1, "title": "json-server", "author": "typicode" }
  ],
  "comments": [
    { "id": 1, "body": "some comment", "postId": 1 }
  ],
  "profile": { "name": "typicode" }
}
```

```json
{
  "products": [
    { "id": 1, "name": "cpu", "price": 1024, "origin": "USA" }
  ],
  "orders": [
    { "id": 1, "productId": "1", "name": "lucas" }
  ]
}
```

## JSON Server 실행
다음 명령어를 통해 각각 서버를 3010 및 3020 포트를 할당받아 실행되도록 했다.

```shell
$ json-server --watch sample-schema/blog.json --port 3010
$ json-server --watch sample-schema/commerce.json --port 3020
```

## Service 설정
서비스 이름을 blog 및 commerce로 등록했다. 애플리케이션의 API를 호출하기 위한 Context를 url로 등록해주면 host, port, protocol 등의 필요한 값이 자동으로 등록된다. `docker.for.mac.localhost`는 Kong Gateway가 Docker로 실행되고 있기 때문에 host에 실행중인 MacOS와 통신하기 위한 호스트네임이라고 이해하면 될것 같다.

```shell
$ curl -XPOST localhost:8001/services --data name=blog --data url=http://docker.for.mac.localhost:3010/
$ curl -XPOST localhost:8001/services --data name=commerce --data url=http://docker.for.mac.localhost:3020/
```

## Route 설정
등록한 서비스에 대한 Route를 등록했다. 각각 이름을 json-server-blog 및 json-server-commerce로 등록했다. 서비스 별로 라우트가 등록되긴하나 같은 이름으로 등록하려고하면 `UNIQUE violation detected on~`과 같이 고유한 값이 아니기 때문에 등록할 수 없다는 메시지가 발생한다.

```shell
curl -XPOST localhost:8001/services/blog/routes --data name=json-server-blog --data 'paths[]=/json-server-blog' --data 'methods=GET'
curl -XPOST localhost:8001/services/commerce/routes --data name=json-server-commerce --data 'paths[]=/json-server-commerce' --data 'methods=GET'
```

여기서 주목할 설정은 paths[]인데, blog 서버의 주소는 localhost:3010이기 때문에, 해당 paths[] 값을 기준으로 Kong Gateway의 프록시 포트(8000)로 요청하면 `http://localhost:8000/json-server-blog`가 `localhost:3010`으로 라우팅하는 것을 확인할 수 있다. 

## 요청 테스트
curl 명령어로 테스트하면 등록한 서비스 및 라우트 설정에 따라 라우팅되는 것을 확인할 수 있다.

```shell
$ curl localhost:3010/posts
[
  {
    "id": 1,
    "title": "json-server",
    "author": "typicode"
  }
]

$ curl localhost:8000/json-server-blog/posts
[
  {
    "id": 1,
    "title": "json-server",
    "author": "typicode"
  }
]

$ curl localhost:3020/products
[
  {
    "id": 1,
    "name": "cpu",
    "price": 1024,
    "origin": "USA"
  }
]

$ curl localhost:8000/json-server-commerce/products
[
  {
    "id": 1,
    "name": "cpu",
    "price": 1024,
    "origin": "USA"
  }
]
```

# 참고자료
* [Expose your Services with Kong Gateway](https://docs.konghq.com/getting-started-guide/2.1.x/expose-services/)
* [Kong API Gateway #2 - Konga & API 등록/요청](https://ibks-platform.tistory.com/379?category=769802)
