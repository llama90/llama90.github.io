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

# 들어가면서
Kong API Gateay의 플러그인에서 Service 또는 Route에 등록된 API를 기준으로, 해당 API가 호출된 수 등과 같은 정보를 조회할 수 있는 방법이 필요하다. Kong Admin API에서 `/status/`라는 [Health Routes](https://docs.konghq.com/2.0.x/admin-api/#health-routes) API를 지원하는 것을 확인했으나, Kong Gateway가 실행되고 있는 nginx에 대한 정보로 보인다. 플러그인 중에 목적으로 하는 기능을 제공하는 대상이 있는지 알아보는 차에 지원하는 플러그인들에 대해서 먼저 리스트업을 했다.

# Kong Plugin List
Kong Gateway에서 추가할 수 있는 Plugin에 대한 정리
* Overall: 대시보드에 보이는 Plugin을 선택했을 때 선택할 수 있는 경우
* Service: Service를 선택해서 Plugin을 선택했을 때 선택할 수 있는 경우
* Route: Route를 선택해서 Plugin을 선택했을 때 선택할 수 있는 경우

## Authentication
Protect your services with an authentication layer

Plugin | Description | Overall | Service | Route
--- | --- | --- | --- | ---
Basic Auth | Add Basic Authentication to your APIs | O | O | O 
Key Auth | Add a key authentication to your APIs | O | O | O 
Oauth2 | Add an OAuth 2.0 authentication to your APIs | O | O | O 
Hmac Auth | Add HMAC Authentication to your APIs | O | O | O 
Jwt | Verify and authenticate JSON Web Tokens | O | O | O 
Ldap Auth | Integrate Kong with a LDAP server | O | O | O 
Session | Support sessions for Kong Authentication Plugins | O | O | O 

## Security
Protect your services with additional security layers

Plugin | Description | Overall | Service | Route
--- | --- | --- | --- | ---
Acl | Control which consumers can access APIs | O | O | O 
Cors | Allow developers to make requests from the browser | O | O | O 
Ip Restriction | Whitelist or blacklist IPs that can make requests | O | O | O 
Bot Detection | Detects and blocks bots or custom clients | O | O | O 
Acme | Let's Encrypt and ACMEv2 integration with Kong | O | O | O 

## Traffic Control
Manage, throttle and restrict inbound and outbound API traffic

Plugin | Description | Overall | Service | Route
--- | --- | --- | --- | ---
Rate Limiting | Rate-limit how many HTTP requests a developer can make | O | O | O 
Response Ratelimiting | Rate-Limiting based on a custom response header value | O | O | O 
Request Size Limiting | Block requests with bodies greater than a specific size | O | O | O 
Request Termination| This plugin terminates incoming requests with a specified status code and message. <br>This allows to (temporarily) block an API or Consumer. | O | O | O 
Proxy Cache | Cache and serve commonly requested responses in Kong | O | O | O 

## Serverless
Invoke serverless functions in combination with other plugins

Plugin | Description | Overall | Service | Route
--- | --- | --- | --- | ---
Aws Lambda | Invoke an AWS Lambda function from Kong. <br> It can be used in combination with other request plugins to secure, manage or extend the function | O | O | O 
Pre Function | Dynamically run Lua code from Kong during access phase | O | O | O 
Post Function | Dynamically run Lua code from Kong during access phase | O | O | O 
Azure Functions | This plugin invokes Azure Functions. <br> It can be used in combination with other request plugins to secure, manage or extend the function | O | O | O 

## Analytics & Monitoring
Visualize, inspect and monitor APIs and microservices traffic

Plugin | Description | Overall | Service | Route
--- | --- | --- | --- | ---
Datadog | Visualize API metrics on Datadog | O | O | O 
Prometheus | Expose metrics related to Kong and proxied upstream services in Prometheus exposition format | O | O | O 
Zipkin | Propagate Zipkin distributed tracing spans, and report spans to a Zipkin server | O | O | O 
Galileo | Business Intelligence Platform for APIs | X | O | O 
Runscope | API Performance Testing and Monitoring | X | O | O 

## Transformations
Transform request and responses on the fly on Kong

Plugin | Description | Overall | Service | Route
--- | --- | --- | --- | ---
Request Transformer | Modify the request before hitting the upstream server | O | O | O 
Response Transformer | Modify the upstream response before returning it to the client | O | O | O 
Correlation Id | Correlate requests and responses using a unique ID | O | O | O 

## Logging
Log requests and response data using the best transport for your infrastructure

Plugin | Description | Overall | Service | Route
--- | --- | --- | --- | ---
Tcp Log | Send request and response logs to a TCP server | O | O | O 
Udp Log | Send request and response logs to an UDP server | O | O | O 
Http Log | Send request and response logs to an HTTP server | O | O | O 
File Log | Append request and response data to a log file on disk | O | O | O 
Syslog | Send request and response logs to Syslog | O | O | O 
Statsd | Send request and response logs to StatsD | O | O | O 
Loggly | Send request and response logs to Loggly | O | O | O 

## Other
Other Plugins

Plugin | Description | Overall | Service | Route
--- | --- | --- | --- | ---
Grpc Web | no description available... | O | O | O 
Grpc Gateway | no description available... | O | O | O 

# Reference
* KONGA 0.14.9에 Kong Admin 2.2.0를 연결해서 나타나는 Plugin을 기준으로 확인
* [Admin API](https://docs.konghq.com/2.0.x/admin-api/)
