---
title: Kong을 이용한 gRPC-Gateway Plugin 테스트
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
tags:
- gRPC
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
API Gateway  오픈소스인 Kong에 gRPC를 구성하는 방법에 대한 실습을 진행했다. 

# 실습
Kong 문서중에 `Introduction to Kong gRPC Plugins`를 따라 진행했다.

## Service 설정
`grpcbin-service`라는 이름의 Service를 등록한다. 

```shell
$ curl -X POST localhost:8001/services \
    --data 'name=grpcbin-service' \
    --data 'url=grpc://grpcb.in:9000'
```

![Added service](https://user-images.githubusercontent.com/6668548/100203027-66455200-2f45-11eb-82ef-26b106af910e.png)

## Service에 대한 Route 설정
등록한 Service에 대한 `grpcbin-get-route`라는 이름의 Route를 등록한다.

```shell
$ curl -X POST localhost:8001/services/grpcbin-service/routes \
    --data 'name=grpcbin-get-route' \
    --data 'paths=/' \
    --data 'methods=GET' \
    --data 'headers.x-grpc=true'
```

![Added route on the service](https://user-images.githubusercontent.com/6668548/100203078-7d843f80-2f45-11eb-8fd1-906b2fadd5a3.png)

## gRPC-Gateway Plugin 설정
등록한 Route에 대한 gRPC-Gateway Plugin을 등록한다.  

```shell
$ curl -X POST localhost:8001/routes/grpcbin-get-route/plugins \
    --data 'name=grpc-gateway' \
    --data 'config.proto=/usr/local/kong/hello-gateway.proto'
```

![Added gRPC-Gateway plugin on the route](https://user-images.githubusercontent.com/6668548/100203127-8bd25b80-2f45-11eb-83ea-fb67c0c0219f.png)

등록된 gRPC-Gateway Plugin 설정은 아래와 같다.
* consumer: 등록된 API가 제한된 사용자에게 제공될 필요가 있으면 설정
* proto: /usr/local/kong/hello-gateway.proto

![Added gRPC](https://user-images.githubusercontent.com/6668548/100203179-9bea3b00-2f45-11eb-9238-bcaf594786a4.png)

## protobuf 정의
`hello-gateway.proto`라는 이름의 protobuf를 정의한다.

```proto
syntax = "proto3";

package hello;

service HelloService {
  rpc SayHello(HelloRequest) returns (HelloResponse) {
    option (google.api.http) = {
      get: "/v1/messages/{name}"
      additional_bindings {
        get: "/v1/messages/legacy/{name=**}"
      }
      post: "/v1/messages/"
      body: "*"
    }
  }
}


// The request message containing the user's name.
message HelloRequest {
  string name = 1;
}

// The response message containing the greetings
message HelloResponse {
  string message = 1;
}
```


## Kong 컨테이너에 protobuf 정의 파일 복사
정의한 protobuf를 Kong 컨테이너에 복사한다. 
`kong:/usr/local/kong`에서 `kong`은 생성한 컨테이너의 이름이다.

```shell
$ docker cp hello-gateway.proto kong:/usr/local/kong/
```

### 복사한 protobuf 파일 확인
Kong 컨테이너에 접속하여 파일이 있는지 확인한다. 아래 방법이 더 효율적이다.

```shell
$ docker exec -it kong bash
bash-5.0$ cd /usr/local/kong/
bash-5.0$ ls
```

또는 

```shell
$ docker exec -it kong ls /usr/local/kong/hello-gateway.proto
/usr/local/kong/hello-gateway.proto
```

## 등록된 gRPC API 확인
curl을 이용해 등록한 gRPC API를 확인한다. 

```shell
$ curl -X GET localhost:8000/v1/messages/kong2.2 \
    --header 'x-grpc: true'
```

위 과정을 수행하면 아래와 같은 응답을 확인할 수 있다.

```
{"message":"hello kong2.2"}
```

# 더 생각해 봐야할 부분
위 과정대로라면 새로운 API를 지원하기 위해서 매번 Kong이 설치된 서버에 파일을 복사해야 하는 번거로움이 있는데, 효율적인 방법이 없을까 하는 생각이 들었다. 

## gRPC API 설정에 URL 등록
`gRPC API`의 설정들 중 protobuf 경로를 지정하기 위한 `proto` 속성에 protobuf 정의 파일에 대한 [github URL](https://github.com/lucaseo90/etc-snippet/blob/main/grpc/kong-test/hello-gateway.proto)을 등록해 봤다. API를 확인해 보면 `{"message":"An unexpected error occurred"}`라는 메시지를 반환하면서 정상적으로 동작하지 않는다.

추가로 검토한 부분은 [Kong Admin API](https://docs.konghq.com/2.0.x/admin-api/) 중에 proto 파일을 업로드하는 API가 있지 않을까 `upload` 및 `proto`로 찾아봤는데 없었다.. 

# Reference
* [Introduction to Kong gRPC Plugins](https://docs.konghq.com/enterprise/2.1.x/plugins/grpc/)
* [Admin API](https://docs.konghq.com/2.0.x/admin-api/)
