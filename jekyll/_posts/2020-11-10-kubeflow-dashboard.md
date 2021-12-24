---
title: Kubeflow Dashboard
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
tags:
- Kubeflow
- Dashboard
- UI
toc: true
toc_sticky: true
markdown: kramdown
highlighter: rouge
categories: Kubeflow
---

# Kubeflow Dashboard
Kubeflow를 설치하면 Kubeflow 컴포넌트들을 사용할 수 있는 중앙 대시 보드를 확인할 수 있다. 대시 보드는 다음과 같은 기능을 제공한다.
* 특정 작업에 대한 바로 가기, 최근 파이프라인 및 노트북 목록, 하나의 화면에서 작업 및 클러스터에 대한 개요를 제공
* 파이프라인, Katib, 노트북 등을 포함하여 클러스터에서 실행되는 컴포넌트의 UI를 제공

## Kubeflow UI 개요
* Kubeflow 컴포넌트 간 탐색을 위한 중앙 대시 보드 `Home`
* Kubeflow 파이프라인 대시보드를 위한 `Pipelines`
* Jupyter 노트북을 위한 `Notebook Servers`
*  초매개변수 조정을 위한 `Katib`
* 아티팩트 메타 데이터 추적을 위한 `Artifact Store`
* Kubeflow deployment의 네임 스페이스에서 사용자 액세스를 공유하기 위한 `Manage Contributors`

아래는 Kubeflow에서 각각 화면을 캡쳐한 스크린샷이다.

### Home
![Home](https://user-images.githubusercontent.com/6668548/98672273-e9d53f80-2398-11eb-8af2-563e27307c9f.png)

### Pipelines
![Pipelines](https://user-images.githubusercontent.com/6668548/98672284-ee99f380-2398-11eb-85bd-f616f1e60cd2.png)

### Notebook Servers
![Notebook Servers](https://user-images.githubusercontent.com/6668548/98672296-f2c61100-2398-11eb-80cc-c92606291333.png)

### Katib
![Katib](https://user-images.githubusercontent.com/6668548/98672312-f78ac500-2398-11eb-889f-73a03a7ebdc5.png)

### Artifact Store
![Artifact Store](https://user-images.githubusercontent.com/6668548/98672329-fe193c80-2398-11eb-8ac1-332f6b75fcd9.png)

### Volumes
![Volumes](https://user-images.githubusercontent.com/6668548/98672347-040f1d80-2399-11eb-82ea-87e3b1fa789a.png)

### Snapshot Store
![Snapshot Store](https://user-images.githubusercontent.com/6668548/98672364-08d3d180-2399-11eb-81f5-5401cdfc0d33.png)

# Reference
* [Central Dashboard](https://www.kubeflow.org/docs/components/central-dash/overview/)
