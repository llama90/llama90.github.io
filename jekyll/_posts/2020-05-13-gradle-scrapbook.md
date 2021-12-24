---
title: Gradle Scrapbook
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
tags:
- Gradle
- Scrapbook
toc: true
toc_sticky: true
markdown: kramdown
highlighter: rouge
categories:
- Scrapbook
---

# gradle-wrapper.properties 생성
이미 생성되어 있는 `gradle-wrapper.properties`을 머신의 Gradle 버전에 맞도록 재생성

```shell
$ gradle wrapper
```

# Gradle 빌드할때 바이너리 파일 다운로드 timeout 발생
```shell
Downloading https://services.gradle.org/distributions/gradle-6.5-bin.zip 
~ timeout... 
```

환경에서 필요로 하는 `gralde-${VERISION}-bin.zip` 파일을 다운로드 후, `gradle-wrapper.properties` 파일 내의 `distributionUrl` 설정 값을 file로 확인하도록 수정

```diff
--distributionUrl=https\://services.gradle.org/distributions/gradle-6.5-all.zip
++distributionUrl=file\:${DIRECTORY_PATH}/gradle-6.5-all.zip
```

