---
title: Docker Troubleshooting List
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
tags:
- Kubeflow
- MiniKF
- Vagrant
- minikube
toc: true
toc_sticky: true
markdown: kramdown
highlighter: rouge
categories:
- Docker
---

# Docker daemon이 정상적으로 동작하지 않음
최신 업데이트(`2.4.2.0(48975)`) 버전이 나왔다고 하여 업데이트를 했는데, 이후 데몬이 starting -> running 상태로 변하지 않는 증상 발생

`Troubleshoot` -> `Clean / Purge data` 수행 후하여 running 상태로 실행되도록 복구됐으나, 테스트용으로 생성했던 데이터베이스 컨테이터들이 모두 제거된 상태였다. 데이터들도 모두 삭제됐을 꺼라 생각하여 다시 세팅하려고 docker-compose 했던 `yml` 파일을 다시 실행했는데, 기존에 설정된 volumn에 대한 데이터는 삭제되지 않았기 때문에,  `docker-compose up -d` 실행 시 `MySQL`의 경우 해당 volumn 기준으로 실행되어 데이터베이스에 세팅한 사용자, 데이터베이스 및 테이블이 존재하여 처음부터 재구성하지 않아도 됐다.
