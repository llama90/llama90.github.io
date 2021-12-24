---
title: Kubeflow Tutorial: An end-to-end ML pipeline on-prem
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
tags:
- Kubeflow
toc: true
toc_sticky: true
markdown: kramdown
highlighter: rouge
categories: Kubeflow
---

# 실습
## Notebook 생성 (Create a Notebook Server)
<img width="954" alt="1  Create Notebook Server config 1" src="https://user-images.githubusercontent.com/6668548/98878574-a2e56800-24c6-11eb-8fa5-0bef52d67dc4.png">

<img width="960" alt="2  Create Notebook Server config 2" src="https://user-images.githubusercontent.com/6668548/98878575-a37dfe80-24c6-11eb-863f-4afce6fe28e5.png">

<img width="940" alt="4  Data volume config" src="https://user-images.githubusercontent.com/6668548/98878576-a37dfe80-24c6-11eb-935f-81e976832a23.png">

<img width="940" alt="3  Image config" src="https://user-images.githubusercontent.com/6668548/98878577-a4169500-24c6-11eb-8e16-70209dafcf62.png">

## 파이프라인 코드 & 데이터 가져오기 (Bring in the Pipelines code and data)
노트북에 접속하면 아래와 같은 화면을 볼 수 있는데 `Other -> Terminal`을 선택하고 다음 명령어를 실행한다. 
```
wget https://raw.githubusercontent.com/arrikto/kubeflow-examples/kubecon-demo/taxi-cab-on-prem/taxi-cab-pipeline-snap.ipynb
```

<img width="1920" alt="5  Notebook" src="https://user-images.githubusercontent.com/6668548/98878573-a2e56800-24c6-11eb-9dca-1582f32c31f3.png">

<img width="1470" alt="6  Notebook terminal" src="https://user-images.githubusercontent.com/6668548/98878572-a24cd180-24c6-11eb-9d3b-c35ad4985975.png">

명령어 수행을 통해 `taxi-cab-pipeline-snap.ipynb` 파일을 다운 받을 수 있다. 해당 파일을 열면 다음과 같은 python  스크립트를 확인할 수 있다.

<img width="1920" alt="7  Notebook taxi cab interactive python" src="https://user-images.githubusercontent.com/6668548/98878571-a24cd180-24c6-11eb-95c0-3dcc8026625c.png">

해당 python 스크립트를 실행하면 실습에서 진행할 데이터 및 파이프라인 코드를 받을 수 있고, `dsl-compiler`를 통해 파이프라인 코드를 컴파일한다.

<img width="1470" alt="8  Notebook taxi cab interactive python executed" src="https://user-images.githubusercontent.com/6668548/98878570-a1b43b00-24c6-11eb-8e4b-4ce375984d77.png">

## 데이터 볼륨 스냅샷 만들기 (Snapshot the Data Volume)
Rok은 `Data Management`를 담당하는데, 스냅샷을 이용해서 각 절차별로 매끄러운 진행에 도움을 준다.

<img width="1920" alt="9  Rok" src="https://user-images.githubusercontent.com/6668548/98878569-a1b43b00-24c6-11eb-849b-62735be68a74.png">

<img width="980" alt="10  Rok Create new bucket" src="https://user-images.githubusercontent.com/6668548/98878566-a11ba480-24c6-11eb-94ff-1486170b41ee.png">

<img width="1380" alt="11  Rok Created new bucket" src="https://user-images.githubusercontent.com/6668548/98878564-a11ba480-24c6-11eb-8c3c-cdb1fe499fec.png">

<img width="1370" alt="12  Rok empty snapshot" src="https://user-images.githubusercontent.com/6668548/98878562-a0830e00-24c6-11eb-99c0-b4550ff093d3.png">

<img width="1750" alt="13  Rok Create new snapshot" src="https://user-images.githubusercontent.com/6668548/98878561-9fea7780-24c6-11eb-8a36-93e64659accf.png">

<img width="1750" alt="14  Rok Snapshot config" src="https://user-images.githubusercontent.com/6668548/98878560-9fea7780-24c6-11eb-93dc-e559ec4cda3e.png">

<img width="1005" alt="15  Rok jupyter config" src="https://user-images.githubusercontent.com/6668548/98878559-9f51e100-24c6-11eb-8db5-acdcb34ed37e.png">

<img width="975" alt="16  Rok commit config" src="https://user-images.githubusercontent.com/6668548/98878558-9f51e100-24c6-11eb-95b8-007ca15bbe9f.png">

<img width="1370" alt="17  Rok Created Snapshot" src="https://user-images.githubusercontent.com/6668548/98878556-9eb94a80-24c6-11eb-98aa-346b07a70216.png">

## KFP에 파이프라인 업로드 (Upload the Pipeline to KFP)
Jupyter Notebook에서 생성한 파이프라인을 다운받아 Kubeflow 파이프라인에 업로드한다. 

<img width="460" alt="18  Download pipeline" src="https://user-images.githubusercontent.com/6668548/98878555-9eb94a80-24c6-11eb-9fed-6de6468b4e2e.png">

<img width="1920" alt="19  Pipeline Main" src="https://user-images.githubusercontent.com/6668548/98878554-9e20b400-24c6-11eb-9a85-50a2548db871.png">

<img width="650" alt="20  Pipeline Upload pipeline" src="https://user-images.githubusercontent.com/6668548/98878553-9d881d80-24c6-11eb-96b5-43dad824442c.png">

<img width="1700" alt="21  Pipeline Upload pipeline preview" src="https://user-images.githubusercontent.com/6668548/98878551-9d881d80-24c6-11eb-9bd0-6318cfc0a1df.png">

## 새로운 실험 생성 (Createa a new Experiment Run)
<img width="660" alt="22  Pipeline upload config 1" src="https://user-images.githubusercontent.com/6668548/98878550-9cef8700-24c6-11eb-843b-f88e06a59d36.png">

<img width="635" alt="23  Pipeline upload config 2" src="https://user-images.githubusercontent.com/6668548/98878547-9cef8700-24c6-11eb-977f-3cfda6c6ee15.png">

<img width="635" alt="24  Pipeline run paramter" src="https://user-images.githubusercontent.com/6668548/98878546-9c56f080-24c6-11eb-8a4c-566501952ef8.png">

## 노트북의 데이터 볼륨으로 파이프라인 구축 (Seed the Pipeline with the Notebook's Data Volume)
<img width="620" alt="25  Pipeline Rok url config 1" src="https://user-images.githubusercontent.com/6668548/98878545-9c56f080-24c6-11eb-8487-22c5957c917b.png">

<img width="625" alt="26  Pipeline Rok url config 2" src="https://user-images.githubusercontent.com/6668548/98878544-9bbe5a00-24c6-11eb-8dde-3feeac7ac8f9.png">

<img width="820" alt="27  Pipeline Rok url config 3" src="https://user-images.githubusercontent.com/6668548/98878543-9b25c380-24c6-11eb-9739-5613cf1ca7df.png">

<img width="620" alt="28  Pipeline Rok url config 4" src="https://user-images.githubusercontent.com/6668548/98878542-9b25c380-24c6-11eb-9234-22074a91feec.png">

## 파이프 라인 스냅샷을 저장한 버킷 선택 (Select a bucket to store the pipeline snapshot)
<img width="975" alt="29  Pipeline Rok register url config 1" src="https://user-images.githubusercontent.com/6668548/98878539-9a8d2d00-24c6-11eb-90c6-8387d6faa2ba.png">

<img width="1075" alt="30  Pipeline Rok register url config 2" src="https://user-images.githubusercontent.com/6668548/98878537-99f49680-24c6-11eb-9243-f37746bc32b2.png">

<img width="620" alt="31  Pipeline Rok config" src="https://user-images.githubusercontent.com/6668548/98878535-995c0000-24c6-11eb-88d7-d7bae4c2767d.png">

## 파이프라인 실행 (Run Pipeline)

## Rok을 이용한 파이프라인 스냅샷 (Pipeline snapshot with Rok)

## 노트북 내에서 파이프라인 결과 탐색 (Explore pipeline results inside a Notebook)

# 문제
## ErrImageNeverPull
> `Run the Pipeline` 절차 진행하는데 `This step is in Pending state with this message: ErrImageNeverPull: Container image "argoproj/argoexec:v2.3.0" is not present with pull policy of Never` 메시지 발생하면서 Pipeline 실행이  멈춘 상태가 된다.
 
![errImageNeverPull](https://user-images.githubusercontent.com/6668548/98464945-7e109c80-2209-11eb-8268-4442ae6a3eee.png)

> `Parallel Execution` 파이프라인과 유사한 문제로 해결

## No such file or directory
> `Run the Pipeline` 절차 진행하는데 에러 발생한 절차에 대한 로그를 확인해보니 `~ No such file or directory`를 확인할 수 있었다. 데이터 볼륨 스냅샷 내의 파일 위치가 잘못되어 있는 것으로 보이는 상황이다.

<img width="1700" alt="32  Run Experiment Error" src="https://user-images.githubusercontent.com/6668548/98878530-952fe280-24c6-11eb-82d0-42e71edd223a.png">
# Reference
* [An end-to-end ML pipeline on-prem:
Notebooks & Kubeflow Pipelines on the new MiniKF](https://medium.com/kubeflow/an-end-to-end-ml-pipeline-on-prem-notebooks-kubeflow-pipelines-on-the-new-minikf-33b7d8e9a836)
