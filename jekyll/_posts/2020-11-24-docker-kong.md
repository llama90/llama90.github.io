---
title: Docker 환경에서 Kong 설치
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
tags:
- Kong
- API Gateway
- Docker
toc: true
toc_sticky: true
markdown: kramdown
highlighter: rouge
categories:
- Practice
---

> 2020-11-25 데이터베이스를 함께 구성하는 경우 `볼륨 생성` 및 `설정 파일 작성`을 할 필요 없다. 데이터베이스를 사용하지 않기 때문에 미리 정의된 설정을 기반으로 구성하기 위해서 필요로 하는 절차로 이해했다.

> 2021-01-03 gRPC를 지원하면서 동시에 HTTP 1.x 요청을 게이트웨이 하기 위해, Kong Container 실행 시 `KONG_PROXY_LISTEN` 옵션 및 Port 설정을 수정했다.

# 들어가면서
gRPC와 관련하여 API 게이트웨이 기능을 하는 Kong을 사용해볼 필요가 생겼다.

## Kong Gateway
Kong Gateway는 마이크로 서비스에 최적화된 오픈 소스 경량 API 게이트웨이로 탁월한 지연 시간, 성능 및 확장성을 제공한다.

기본적으로 구성된 프록시 포트 8000 및 8443에서 트래픽을 수신하는데, 들어오는 클라이언트 API 요청을 확인하여 적절한 백엔드 API로 라우팅한다. 요청을 라우팅하고 응답을 제공하는 동안 필요에 따라 플러그인을 이용해 정책을 적용 할 수 있다.

예를 들어 요청을 라우팅하기 전에 클라이언트에 인증이 필요할 수 있는데, 이러한 기능은 몇 가지 이점을 제공한다. 
* Kong Gateway가 인증을 처리하기 때문에 서비스에는 자체 인증 로직이 필요하지 않음
* 서비스는 유효한 요청만 수신하므로 잘못된 요청을 처리하는 데 주기가 낭비되지 않음
* 트래픽의 중앙 가시성을 위해 모든 요청이 기록됨

![Traffic flow in Kong gateway](https://docs.konghq.com/assets/images/docs/getting-started-guide/gateway-traffic.png)

API 게이트웨이 기능에 충실한 다양한 기능들을 제공 하고 있으며, 엔터프라이즈 환경에서 유용할것 같은 기능들은 제한이 있어 보인다.

# Docker 환경에서 Kong 설치

## 네트워크 생성
```shell
$ docker network create kong-net
```

## 데이터베이스 컨테이너 생성
처음에는 데이터베이스를 제외하고 환경 구성을 했는데, 이러한 경우 Kong의 플러그인 사용에 제약이 있고, 테스트 해봐야 하는 gRPC 플러그인도 등록할 수 없어서 데이터베이스도 컨테이너로 구성해줬다. 

카산드라 또는 데이터베이스를 사용할 수 있는데 익숙한 데이터베이스로 환경 구성을 진행했다.

```shell
$ docker run -d --name kong-database \
    --network=kong-net \
    -p 5432:5432 \
    -e "POSTGRES_USER=kong" \
    -e "POSTGRES_DB=kong" \
    -e "POSTGRES_PASSWORD=kong" \
    -e "POSTGRES_HOST_AUTH_METHOD=trust" \
    postgres:latest
```

## Kong 데이터베이스 구성
데이터베이스 컨테이너를 생성하고 해당 컨테이너에 Kong과 관련하여 필요한 구성을 해주기 위해 아래 명령을 수행해야 한다.

```shell
$ docker run --rm \
    --network=kong-net \
    --link kong-database:kong-database \
    -e "KONG_DATABASE=postgres" \
    -e "KONG_PG_HOST=kong-database" \
    -e "KONG_CASSANDRA_CONTACT_POINTS=kong-database" \
    kong kong migrations bootstrap
```

## Kong 컨테이너 생성
Kong 컨테이너를 생성한다.

```shell
docker run -d --name kong \
    --network=kong-net \
    --link kong-database:kong-database \
    -e "KONG_DATABASE=postgres" \
    -e "KONG_PG_HOST=kong-database" \
    -e "KONG_CASSANDRA_CONTACT_POINTS=kong-database" \
    -e "KONG_PROXY_ACCESS_LOG=/dev/stdout" \
    -e "KONG_ADMIN_ACCESS_LOG=/dev/stdout" \
    -e "KONG_PROXY_ERROR_LOG=/dev/stderr" \
    -e "KONG_ADMIN_ERROR_LOG=/dev/stderr" \
    -e "KONG_ADMIN_LISTEN=0.0.0.0:8001, 0.0.0.0:8444 ssl" \
    -e "KONG_PROXY_LISTEN=0.0.0.0:8000, 0.0.0.0:8443 http2 ssl, 0.0.0.0:9080 http2, 0.0.0.0:9081 http2 ssl" \
    -p 8000:8000 \
    -p 8443:8443 \
    -p 8001:8001 \
    -p 8444:8444 \
    -p 9080:9080 \
    -p 9081:9081 \
    kong
```

## Konga 컨테이너 생성
Kong에 대한 대시보드 기능을 하는 Konga 컨테이너를 생성한다. 

```shell
$ docker run -d -p 1337:1337 --name konga \
    --network=kong-net \
    -v /var/data/kongadata:/app/kongadata \
    -e "NODE_ENV=production" \
    pantsel/konga
```

## Konga 설정
Konga 까지 설치된 후 `http://localhost:1337/`로 접근하면 관리자 계정을 생성하는 화면이 뜨고, 연결을 위한 KONG에 대한 접근 정보를 입력하는 화면이 나타난다. 

이름은 `local-kong` Kong의 Admin URL은 `http://kong:8001`로 했는데 여기서 `kong`은 컨테이너 이름이다. 

![Setting Kong Connection](https://user-images.githubusercontent.com/6668548/100094839-f7142300-2e9c-11eb-950d-7095b966a87e.png)

Kong과 연결을 해주면 다음과 같은 화면을 확인할 수 있다.

![Konga Dashboard](https://user-images.githubusercontent.com/6668548/100094868-01ceb800-2e9d-11eb-9323-8f1fa2a2b5f5.png)

# Reference
* [오픈소스 API 게이트웨이 Kong](https://bcho.tistory.com/1361)
* dockerhub - [kong](https://hub.docker.com/_/kong)
* kong - [Docker Installation](https://docs.konghq.com/install/docker/?_ga=2.130503753.753085870.1606287461-171006562.1606287461)
* kong - [Getting Started Guide](https://docs.konghq.com/getting-started-guide/2.2.x/overview/)
