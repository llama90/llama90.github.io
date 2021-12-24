---
title: gRPC Gateway
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
tags:
- gRPC
- gRPC Gateway
toc: true
toc_sticky: true
markdown: kramdown
highlighter: rouge
categories:
- Practice
---

# gRPC Gateway란?
Google 프로토콜 버퍼 컴파일러 protoc의 플러그인으로, gRPC 서비스 정의를 읽고 RESTful JSON API를 gRPC로 변환하는 역방향 프록시 서버를 생성해주는 기능을 수행한다.
API 버전에 대한 호환성 유지 또는 gRPC에서 미진한 언어 및 클라이언트를 지원하기 위한 목적으로 JSON 포맷으로 구성된 RESTful API를 제공할 수 있다.

## 설치
### go 언어 다운로드 및 설치
[Download and install](https://golang.org/doc/install?download=go1.15.5.darwin-amd64.pkg)에서 Mac 용 설치 파일을 다운받아서 설치했다.

```shell
$ go version
go version go1.15.5 darwin/amd64
```

### Protocol Buffer 설치
커맨드 라인 환경에서 다음과 같은 과정을 수행한다. grpc-gateway를 사용하기 위해서는 Google 프로토콜 버퍼 컴파일러 protoc v3.0.0 이상을 설치해야 한다.

1. [protobuf-all-3.14.0.tar.gz](https://github.com/protocolbuffers/protobuf/releases/download/v3.14.0/protobuf-all-3.14.0.tar.gz) 파일을 다운 받아 압축을 풀고, 압축이 풀린 경로로 이동한다.
2. (optional, 3번을 수행했는데 명령어 실행에 문제가 없다면 진행할 필요 없다) brew 명령을 이용해 autoconf 및 automake 패키지를 설치한다.
```shell
$ brew install autoconf && brew install automake
```
3. protobuf 설치를 위해 다음 명령을 실행한다. (다소 시간이 소요된다)
```shell
$ ./autogen.sh && ./configure && make
... 
$ make check
...
PASS: protobuf-test
PASS: protobuf-lazy-descriptor-test
PASS: protobuf-lite-test
PASS: google/protobuf/compiler/zip_output_unittest.sh
PASS: google/protobuf/io/gzip_stream_unittest.sh
PASS: protobuf-lite-arena-test
PASS: no-warning-test
============================================================================
Testsuite summary for Protocol Buffers 3.14.0
============================================================================
# TOTAL: 7
# PASS:  7
# SKIP:  0
# XFAIL: 0
# FAIL:  0
# XPASS: 0
# ERROR: 0
============================================================================
$ sudo make install
...
$ which proto
$ protoc --version
libprotoc 3.14.0
```

> **Autoconf**  
> Autoconf는 셸 스크립트를 만드는 도구  
> **Automake**  
> 포팅할 수 있는 makefile"들을 만들어 내는 프로그래밍 도구이며 소프트웨어를 컴파일하는데 사용


### grpc-gateway 바이너리 설치
`README.md`에 있는 아래 명령을 수행하라고 하는데 설치되지 않는다. 

```shell
$ go install \
    github.com/grpc-ecosystem/grpc-gateway/v2/protoc-gen-grpc-gateway \
    github.com/grpc-ecosystem/grpc-gateway/v2/protoc-gen-openapiv2 \
    google.golang.org/protobuf/cmd/protoc-gen-go \
    google.golang.org/grpc/cmd/protoc-gen-go-grpc
```

go의 모듈에 대한 개념도 이해하지 못한 상황이고, 여기저기 구글링하여 아래와 같이 명령을 수행하면 되는것을 확인했다.

환경변수도 따로 지정하지 않으면 `GOPATH` 및 `GOBIN`에 대한 에러 메시지를 확인할 수 있다.

```shell
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GOBIN=$GOPATH/bin
export PATH=$PATH:$GOROOT:$GOPATH:$GOBIN
```

```shell
$ brew install dep
$ go get -u github.com/grpc-ecosystem/grpc-gateway/v2/protoc-gen-openapiv2
$ go get -u github.com/grpc-ecosystem/grpc-gateway/v2/protoc-gen-grpc-gateway
$ go get -u google.golang.org/protobuf/cmd/protoc-gen-go
$ go get -u google.golang.org/grpc/cmd/protoc-gen-go-grpc
```

## 사용
### 프로토콜 버퍼를 사용해서 gRPC 서비스를 정의 (파일: `your_service.proto`)

```go
syntax = "proto3";
package your.service.v1;
option go_package = "github.com/yourorg/yourprotos/gen/go/your/service/v1";
message StringMessage {
  string value = 1;
}

service YourService {
  rpc Echo(StringMessage) returns (StringMessage) {}
}
```
### gRPC stub 생성  
이 단계에서는 서비스를 구현하고 클라이언트에서 소비하는 데 사용할 수있는 gRPC 스텁을 생성한다
다음은 Go 스텁을 생성하기위한 protoc 명령이다.

```shell
protoc -I . \
  --go_out ./gen/go/ --go_opt paths=source_relative \
  --go-grpc_out ./gen/go/ --go-grpc_opt paths=source_relative \
  your/service/v1/your_service.proto
```

지정된 경로에 아래와 같이 파일이 생성되는 것을 확인할 수 있다.

```shell
$ ls -al gen/go/your/service/v1/your_service*
-rw-r--r--  1 abc  staff  6071 11 30 20:40 gen/go/your/service/v1/your_service.pb.go
-rw-r--r--  1 abc  staff  3298 11 30 20:40 gen/go/your/service/v1/your_service_grpc.pb.go
```

### gRPC에 서비스 구현
1. (선택적으로) 다른 프로그래밍 언어로 gRPC stub을 생성  
예를 들어 다음은 `your/service/v1/your_service.proto`를 기반으로 Ruby 용 gRPC 코드를 생성
```shell
protoc -I . --ruby_out ./gen/ruby your/service/v1/your_service.proto
protoc -I . --grpc-ruby_out ./gen/ruby your/service/v1/your_service.proto
```
2. googleapis-common-protos gem (또는 이에 상응하는 언어)을 프로젝트에 대한 종속성으로 추가
3. gRPC 서비스 스텁 구현

### `protoc-gen-grpc-gateway`를 사용하여 리버스 프록시 생성
이 시점에서 세 가지 옵션이 존재한다
* 더 이상 수정하지 않고 HTTP 의미 체계 (메서드, 경로 등)에 대한 기본 매핑을 사용
  * 이것은 모든 .proto 파일에서 작동하지만 HTTP 경로, 요청 매개 변수 또는 이와 유사한 설정을 허용하지 않음
* 사용자 지정 매핑을 사용하기위한 추가 .proto 수정
  * .proto 파일의 매개 변수를 사용하여 사용자 지정 HTTP 매핑을 설정
* .proto 수정은 없지만 외부 구성 파일 사용
  * 사용자 지정 HTTP 매핑을 설정하기 위해 외부 구성 파일에 의존
  * 소스 proto 파일을 제어 할 수 없을 때 가장 유용

**기본 매핑 사용**
.proto 파일을 추가로 수정할 필요는 없지만 플러그인을 실행할 때 특정 옵션을 활성화해야한다. 
`generate_unbound_methods`를 활성화해야합니다.

이 옵션이 활성화 된 경우 protoc 실행은 다음과 같습니다.

```shell
protoc -I. --grpc-gateway_out ./gen/go \
  --grpc-gateway_opt logtostderr = true \
  --grpc-gateway_opt paths = source_relative \
  --grpc-gateway_opt generate_unbound_methods = true \
  your/service/v1/your_service.proto
```

`your_service.pb.gw.go` 파일이 생성된 것을 확인 할 수 있다.

```shell
ls -al gen/go/your/service/v1/your_service*
-rw-r--r--  1 abc  staff  6475 11 30 20:56 gen/go/your/service/v1/your_service.pb.gw.go
```

**사용자 지정 주석 사용**
.proto 파일에 google.api.http 주석 추가

`your_service.proto`

```diff
syntax = "proto3";
package your.service.v1;
option go_package = "github.com/yourorg/yourprotos/gen/go/your/service/v1";
+
+import "google/api/annotations.proto";
+
message StringMessage {
  string value = 1;
}

service YourService {
-  rpc Echo(StringMessage) returns (StringMessage) {}
+  rpc Echo(StringMessage) returns (StringMessage) {
+    option (google.api.http) = {
+      post: "/v1/example/echo"
+      body: "*"
+    };
+  }
}
```

게이트웨이 동작을 사용자 정의하고 OpenAPI 출력을 생성하기 위해 추가 할 수있는 추가 주석의 예는 [a_bit_of_everything.proto](https://github.com/grpc-ecosystem/grpc-gateway/blob/master/examples/internal/proto/examplepb/a_bit_of_everything.proto)를 참조하자

protoc 실행은 다음과 같습니다.

```shell
protoc -I . --grpc-gateway_out ./gen/go \
  --grpc-gateway_opt logtostderr=true \
  --grpc-gateway_opt paths=source_relative \
  your/service/v1/your_service.proto
```

**외부 gRPC 서비스 구성 파일을 사용**
외부 구성 grpc-gateway와 함께 사용할 proto 파일을 수정하지 않거나 수정할 수없는 경우 외부 [gRPC 서비스 구성 파일](https://cloud.google.com/endpoints/docs/grpc/grpc-service-config)을 사용할 수도 있다. 자세한 내용은 [설명서](?)를 확인하자.

이 옵션이 활성화 된 경우 protoc 실행은 다음과 같다.

```shell
protoc -I . --grpc-gateway_out ./gen/go \
  --grpc-gateway_opt logtostderr=true \
  --grpc-gateway_opt paths=source_relative \
  --grpc-gateway_opt grpc_api_configuration=path/to/config.yaml \
  your/service/v1/your_service.proto
```

### HTTP 리버스 프록시 서버의 진입점 작성
```go
package main

import (
  "context"
  "flag"
  "net/http"

  "github.com/golang/glog"
  "github.com/grpc-ecosystem/grpc-gateway/v2/runtime"
  "google.golang.org/grpc"

  gw "github.com/yourorg/yourrepo/proto/gen/go/your/service/v1/your_service"  // Update
)

var (
  // command-line options:
  // gRPC server endpoint
  grpcServerEndpoint = flag.String("grpc-server-endpoint",  "localhost:9090", "gRPC server endpoint")
)

func run() error {
  ctx := context.Background()
  ctx, cancel := context.WithCancel(ctx)
  defer cancel()

  // Register gRPC server endpoint
  // Note: Make sure the gRPC server is running properly and accessible
  mux := runtime.NewServeMux()
  opts := []grpc.DialOption{grpc.WithInsecure()}
  err := gw.RegisterYourServiceHandlerFromEndpoint(ctx, mux,  *grpcServerEndpoint, opts)
  if err != nil {
    return err
  }

  // Start HTTP server (and proxy calls to gRPC server endpoint)
  return http.ListenAndServe(":8081", mux)
}

func main() {
  flag.Parse()
  defer glog.Flush()

  if err := run(); err != nil {
    glog.Fatal(err)
  }
}
```

### (선택 사항) protoc-gen-openapiv2를 사용하여 OpenAPI 정의 생성
```shell
$ protoc -I . --openapiv2_out ./gen/openapiv2 --openapiv2_opt logtostderr=true your/service/v1/your_service.proto
```

# 참고자료
* [gRPC Gateway](https://github.com/grpc-ecosystem/grpc-gateway)
* [protobuf 사용을 위한 설치](https://hwiyong.tistory.com/164)
* [Autoconf](https://ko.wikipedia.org/wiki/Autoconf)
* [Automake](https://ko.wikipedia.org/wiki/Automake)
* [Go dependency manager](https://johngrib.github.io/wiki/golang-dependency-manager/)
* [protoc-gen-go: program not found or is not executable](https://github.com/golang/protobuf/issues/795)
