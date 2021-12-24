---
title: Kubeflow 환경구성 (MacOS)
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
- Kubeflow
---

* `20-12-26` 오랜만에 다시 minikube를 이용해서 Kubeflow를 설치해보려고 보니, `Kubeflow 1.2`가 최신 버전으로 릴리즈되었다. 해당 버전을 기준으로 설치를 진행해보고 내용을 정리했다. 

# 들어가면서
`Kubeflow`의 `pipeline`에 대해 알기 위해서 환경 구성을 진행했다. <del>원래는 `minikube` 이용해서 진행했는데 1.0과 1.1 둘 다 순조롭게 진행되지 않아서</del> 최종적으로 `Vagrant`를 이용해서 환경을 구성했다. 설치 당시 기준으로 `Kubeflow 1.1`이 최신인 것으로 알고 있는데 `Kubeflow 1.0`이 설치된 것을 확인했으며, `pipeline` 개념 및 기능을 파악하는 것이 목적이기 때문에 버전은 큰 영향이 없을 것으로 판단한다.

# 설치

## MiniKF

### Vagrant 설치
```shell
brew cask install virtualbox
brew cask install vagrant
brew cask install vagrant-manager  
```

### MiniKF 설치
```shell
vagrant init arrikto/minikf
vagrant up
```

### MiniKF 업그레이드
```shell
vagrant box update
vagrant box list
vagrant plugin update vagrant-persistent-storage  
vagrant destroy
-- 모든 로컬 상태를 삭제하는 명령은 수행하지 않음  
vagrant up
```

## 실행
`vagrant up`이 완료되면 `http://10.10.10.10/`에 접속하여 진행하면 최종적으로 아래와 같은 화면을 확인할 수 있다. 

![http://10.10.10.10/](https://user-images.githubusercontent.com/6668548/98359276-4e2b9280-206b-11eb-929c-d25eab46d1f7.png)

`Connect to Kubeflow` 버튼을 클릭하면 로그인 화면을 확인할 수 있고 `Credentials`에 명시된 계정과 비밀번호로 접속하면 `Kubeflow`의 대시보드를 확인할 수 있다.

![Kubeflow Dashboard](https://user-images.githubusercontent.com/6668548/98359743-06593b00-206c-11eb-9b39-8f94ee698734.png)

## minikube

### minikube 설치
brew를 이용해 minikube를 설치한다.

```shell
brew install minikube
```

### minikube 실행
minikube를 실행한다.

```shell
$ minikube start --cpus 6 --memory 16192 --disk-size=120g

😄  Darwin 11.0.1 위의 minikube v1.9.2
✨  자동적으로 hyperkit 드라이버가 선택되었습니다. 다른 드라이버 목록: docker, virtualbox
👍  Starting control plane node m01 in cluster minikube
🔥  hyperkit VM (CPUs=6, Memory=16192MB, Disk=122880MB) 를 생성하는 중 ...
🐳  쿠버네티스 v1.18.0 을 Docker 19.03.8 런타임으로 설치하는 중
🌟  애드온을 활성화하는 중: default-storageclass, storage-provisioner
🏄  끝났습니다! 이제 kubectl 이 "minikube" 를 사용할 수 있도록 설정되었습니다
```

### Kubeflow 설치
설치 경로를 `/Users/${USER}/workspace/kubeflow/`로 진행했다. 여기서 ${USER}는 현재 접속중인 사용자 계정으로, 만약 계정명이 abc라면 경로는 `/Users/abc/workspace/kubeflow/`가 된다.  다음 진행할 작업은 설치 경로를 기준으로 진행한다.

설치에 사용한 버전은 다음과 같다. 공식문서에는 Kubeflow 1.2.0과 kfctl_k8s_istio 1.1.0을 기준으로 명시되어 있는데, 안되서 kfctl_k8s_istio 1.0.2로 진행하니까 된다...

name | version 
 --- | ---
minikube | 1.9.2
kubernetes | 1.18.0
kubeflow | 1.2.0
kfctl_k8s_istio | 1.0.2

1. [Kubeflow releases page](https://github.com/kubeflow/kfctl/releases/)에서 kfctl v1.2.0를 다운받는다. 
2. 다운받은 파일의 압축을 해제한다. (`{KFCTL_TAR_GZ_FILE}`는 자신의 환경에 맞게 지정해준다.)
```shell
tar -xvf {KFCTL_TAR_GZ_FILE}
```
3. Kubeflow 및 kfctl에 대한 환경변수를 설정한다.
```shell
export PATH=$PATH:/Users/${USER}/workspace/kubeflow
export KF_NAME=kubeflow-v1.2
export BASE_DIR=/Users/${USER}/workspace/kubeflow
export KF_DIR=${BASE_DIR}/${KF_NAME}
export CONFIG_URI="https://raw.githubusercontent.com/kubeflow/manifests/v1.1-branch/kfdef/kfctl_k8s_istio.v1.0.2.yaml" 
```
4. Kubeflow를 배포한다.
```shell
mkdir -p ${KF_DIR}
cd ${KF_DIR}
kfctl apply -V -f ${CONFIG_URI}
...
INFO[0200] Successfully applied application seldon-core-operator  filename="kustomize/kustomize.go:291"
INFO[0200] Applied the configuration Successfully!       filename="cmd/apply.go:75"
```
5. 설치 후 Kubeflow 관련 POD 들이 모두  `Running` 상태가 되기까지 기다린다. (**시간이 오래걸린다**)
```shell
kubectl get pod -n kubeflow
```
6. 대시보드에 접속해본다. (두 가지 방법이 있다.)
* istio에 포트포워딩 설정 후 `localhost:8080`로 접속
```shell
kubectl port-forward -n istio-system svc/istio-ingressgateway 8080:80
```
* ingress host 및 port 번호를 확인해서 `http://<INGRESS_HOST>:<INGRESS_PORT>`로 접속, 환경변수에 할당되는 값이 무엇일지 궁금해서 확인해보니, minikube가 실행중인 ip와 istio의 ingressgateway 포트번호를 찾아주는 명령을 이용해 환경변수에 해당 ip 및 port를 할당해주는 것으로 보였다.
```shell
export INGRESS_HOST=$(minikube ip)
export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}')
#$ echo $(minikube ip)
#192.168.64.33
#$ echo $(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}')
#31380
```

설치가 문제 없이 진행되었다면, `kubectl get pod -n kubeflow` 명령을 통해서 모든 POD가 Running 상태 또는 Completed 상태인 것을 확인 할 수 있다. 내 경우 Completed 상태로 나타난 POD는 `spark-operatorcrd-cleanup-njvl5`뿐이었다. 그리고 최종적으로 대시보드에 접속하면 다음과 같은 화면이 나타나면서 namespace를 설정한 다음, miniKF로 환경 구성을 했을 때처럼 Kubeflow 대시보드에 접속할 수 있다.

![minikube를 이용해 Kubeflow 설치하여 대시보드를 접속하면 나타나는 화면](https://user-images.githubusercontent.com/6668548/103151636-26cb8900-47c3-11eb-872e-42988e3a8db5.png)

# 참고자료
* [Vagrant](https://sourabhbajaj.com/mac-setup/Vagrant/README.html)
* [MiniKF](https://www.kubeflow.org/docs/started/workstation/getting-started-minikf/)
* [Deploying with minikube on a single node](https://www.kubeflow.org/docs/started/workstation/minikube-linux/)
* [[OSS] Mac에서 Kubeflow 설치하고 테스트하기](https://magoker.tistory.com/93)
