---
title: Kubeflow Pipeline - [Sample] Basic - Parallel execution
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
tags:
- Kubeflow
- Pipeline
- Tutorial
toc: true
toc_sticky: true
markdown: kramdown
highlighter: rouge
categories: Kubeflow
---

# 들어가면서
Kubeflow의 파이프라인에 대한 이해를 위해 간단한 예제를 실행시켜봤다.

> 결론부터 이야기하면 파이프라인 실행 시 Pod와 관련한 에러 때문에 Pending 상태가 되는 상황이다..
> `2020-11-11` 해결

# 실습
## Parallel Execution 예제 선택
파이프라인 대시보드를 확인해보면 간단한 파이프라인 예제들이 등록되어 있다. 그중 Parallel Execution 예제를 실행해보려고 했다.
<img width="1685" alt="Pipeline Samples" src="https://user-images.githubusercontent.com/6668548/98733435-d9968200-23e3-11eb-9bbf-51382f2c67e6.png">

## Experiemnt 생성 선택
해당 파이프라인을 선택하면 우측 상단에 `Create Experiment`를 확인할 수 있다. 

<img width="1700" alt="Parallel Exeuction Graph" src="https://user-images.githubusercontent.com/6668548/98733432-d8fdeb80-23e3-11eb-8661-2af81ac5ceac.png">

## Experiment 이름 지정
파이프라인 이름을 지어준다. 여기서는 `Hello Kubeflow Pipeline`이라고 이름을 지정했다.

<img width="660" alt="New Experiment" src="https://user-images.githubusercontent.com/6668548/98733430-d8655500-23e3-11eb-85a8-e23994d26e05.png">

## Experiemnt 상세 파라미터 설정
파이프라인 실행과 관련한 상세 파라미터를 설정할 수 있는데 특별하게 변경한 것은 없다.

<img width="660" alt="Start a run" src="https://user-images.githubusercontent.com/6668548/98733428-d7ccbe80-23e3-11eb-81c6-176a2e9a462d.png">

## Experiment 생성 완료
Experiemnt를 생성하면 해당 파이프라인에 대한 실행이 등록된 것을 확인할 수 있다.

<img width="1920" alt="Registered Experiment" src="https://user-images.githubusercontent.com/6668548/98733425-d7342800-23e3-11eb-8447-66773eba8716.png">

## Experiemnt 진행 상황 확인
공교롭게도 실행 중에 Pod를 초기화 하는 과정에서 에러가 발생한다.

> 에러를 해결할 방법을 찾아보고 있는 중...

<img width="1700" alt="Parallel Execution Error 1" src="https://user-images.githubusercontent.com/6668548/98733420-d69b9180-23e3-11eb-83e6-e6a53e4c6804.png">

<img width="1700" alt="Parallel Exeuciton Error 2" src="https://user-images.githubusercontent.com/6668548/98733408-d3080a80-23e3-11eb-9d3f-a32121684c8d.png">

### 해결
kubeflow 파드의 상태를 확인하고 상세 내용을 확인해봤다.

```
$kubectl -n kubeflow get pods
NAME                                                           READY   STATUS      RESTARTS   AGE
parallel-pipeline-ms59w-1079111783                             0/2     Completed   0          20m
parallel-pipeline-ms59w-151982236                              0/2     Completed   0          17m
parallel-pipeline-ms59w-414352292                              0/2     Completed   0          20m

$kubectl -n kubeflow describe pod parallel-pipeline-ms59w-1079111783
...
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  42m   default-scheduler  Successfully assigned kubeflow/parallel-pipeline-ms59w-1079111783 to minikube
  Normal  Pulled     42m   kubelet, minikube  Container image "gcr.io/arrikto-public/startup-lock-init@sha256:0fbe996a2f6b380d7c566ba16255ec034faec983c2661da778fe09b3e744ad21" already present on machine
  Normal  Created    42m   kubelet, minikube  Created container startup-lock-init-container
  Normal  Started    42m   kubelet, minikube  Started container startup-lock-init-container
  Normal  Pulled     42m   kubelet, minikube  Container image "argoproj/argoexec:v2.3.0" already present on machine
  Normal  Created    42m   kubelet, minikube  Created container wait
  Normal  Started    42m   kubelet, minikube  Started container wait
  Normal  Pulling    42m   kubelet, minikube  Pulling image "google/cloud-sdk:272.0.0"
  Normal  Pulled     39m   kubelet, minikube  Successfully pulled image "google/cloud-sdk:272.0.0"
  Normal  Created    39m   kubelet, minikube  Created container main
  Normal  Started    39m   kubelet, minikube  Started container main

```

이 과정에서 머신에 이미 `Container image "argoproj/argoexec:v2.3.0" already present on machine` 존재한다는데 왜 이미지를 다시 가져올까 생각하던 중에 `Notebook Servers`에 새로운 `Jupyter Notebook` 서버를 추가해 주고 Experiment를 확인해 보니 파이프라인이 정상적으로 실행된 것을 확인할 수 있었다.

![Notebook Servers - Jupyter Notebook](https://user-images.githubusercontent.com/6668548/98795354-75100d00-244d-11eb-8637-89bfe1cf89c3.png)

![Completed Pipeline](https://user-images.githubusercontent.com/6668548/98795373-7c371b00-244d-11eb-99b7-acd52f819c35.png)

> minikf 만의 특성인 것인지.. Kubeflow에서 파이프라인을 실행하기 위해서 최소한 하나의 Juypter Notebook 인스턴스가 필요한 것인지는 확인하지 못했다.

# Reference
* [Pipelines Quickstart: Run a basic pipeline ](https://www.kubeflow.org/docs/pipelines/pipelines-quickstart/#run-a-basic-pipeline)
