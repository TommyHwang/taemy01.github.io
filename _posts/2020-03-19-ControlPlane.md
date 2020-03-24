---
layout: post
title:  "Kubernetes Component"
date:   2020-03-23 12:26:00 -0700
author: tommhwang
categories: Kubernetes
tags: Kubernetes
cover:  "/assets/instacode.png"
---

## Master Node와 Worker Node

이번 포스팅에서는 앞서 소개드린 Kubernetes의 기본 구성을 살펴보도록 하겠습니다.

Kubernetes를 사용하게 되면 여러 Node를 하나의 Cluster를 구성해서 관리하게 됩니다.

Node(혹은 Worker Node)는 Cluster내에서 컴퓨터나 VM 같이 리소스를 가지고 컨테이너들의 실제로 올라가서 동작하는 Worker Machine을 의미합니다. 여러분의 PC 1대, 안에 있는 VM 1대, AWS의 EC2를 모두 Node 단위로 볼 수 있습니다.

Cluster는 이러한 Node들의 집합을 의미하고, 최소 하나 이상의 Node를 포함합니다. 또한 Cluster에는 이 Node들을 Control하기 위한 구성요소들이 포함된 Master Node가 포함됩니다. 즉 Cluster에는 Master Node와 최소 1개 이상의 Node가 포함됩니다.

Master Node를 Node로 동작시키는 것도 가능합니다. 즉 Node 하나만 있어도 Cluster를 구성하는 것이 가능합니다.(물론 그런 상황이면 Kubernetes를 운영할 이유가 있는진 모르겠지만) 여담으로 minikube로 Cluster를 구성하면 자동으로 Master Node를 VM으로 생성하고 init한 위치의 PC를 Node로 만듭니다. Master Node 1개와 Node 1개로 구성된 클러스터를 생성하는 겁니다.

그렇다면 Master Node와 Node를 구분하는 기준은 뭘까요?

Master Node에는 Cluster 전체를 오케스트레이션하기 위한 Control Plane 요소들이 포합됩니다.

쿠버네티스 공식 문서에 아래의 그림을 보시면 이해가 빠르실 수 있습니다.



[![Components of Kubernetes](https://camo.githubusercontent.com/d085319f24b565a8b0e89f65281e9fa76b00e048/68747470733a2f2f64333377756272666b69306c36382e636c6f756466726f6e742e6e65742f373031363531373337356431306337303234383931363765373034646362393965353730646638352f37626235332f696d616765732f646f63732f636f6d706f6e656e74732d6f662d6b756265726e657465732e706e67)](https://camo.githubusercontent.com/d085319f24b565a8b0e89f65281e9fa76b00e048/68747470733a2f2f64333377756272666b69306c36382e636c6f756466726f6e742e6e65742f373031363531373337356431306337303234383931363765373034646362393965353730646638352f37626235332f696d616765732f646f63732f636f6d706f6e656e74732d6f662d6b756265726e657465732e706e67)

[그림] Kubernetes Cluster 구성



쿠버네티스는 위와 같이 Control Plane이 포함된 Master Node와 실제로 Container가 동작하는 여러 Node(Worker Node)들이 Master Node의 kube-api-server로 통신하는 방식으로 구성됩니다. 각 구성요소들이 하는 역할은 아래와 같습니다,



## Control plane components

### kube-controller-manager

 Controller 프로세스가 동작하는 구성요소입니다.

 Controller라는 것은 쿠버네티스의 각 오브젝트(흔히 들어보셨을 Pod, Service 같은 것들을 말합니다)들을 제어하는 역할을 하는 요소를 나타내는 말입니다. 각 오브젝트 별로 Node Controller, Endpoints Controller같은 것들이 따로따로(개념적으로) 존재하고, 실제로는 복잡성을 줄이기위해 단일 프로세스로 동작합니다.



### kube-api-server

 Worker Node 및 내부 구성요소들과 통신하기 위해 존재하는 구성요소입니다. 쿠버네티스를 이미 써보신 분들은 kubectl 명령을 많이 사용해보셨을텐데 kubectl 에 대한 응답을 전달해주는 것이 바로 이 kube-api-server입니다. 

 Micro Service Architecture개념에 흔히 나오는 그 API server입니다. kube-api-server와의 통신은 REST api로 이루어지고, kubectl 명령도 REST api형태의 요청을 kube-api-server에 보내주는 역할을 합니다.

 

### etcd

 클러스터에 대한 정보를 저장하고 있는 키값 저장소입니다. kube-api-server와의 통신으로 정보를 저장하고 반환하는데, 클러스터 정보를 저장하고 있다는 말은 반대로 etcd가 해킹당하면 클러스터에 대한 정보를 전부 알 수 있다는 말이기도 합니다. 

 때문에 etcd를 암호화하고 접근을 제한할 수 있는 여러 솔루션이 나오기도 합니다.



### kube-scheduler

 새로운 Pod를 생성할 때 어떤 Node에 올라갈지 정하는 역할을 하는 구성요소입니다. 어떤 Node에 올라갈지는 리소스 상태나 사용자의 설정에 따라 달라집니다. 쿠버네티스 Pod를 생성했을 때 Pod가 Pending 상태로 대기한다면, kube-scheduler가 판단했을 때 Pod가 올라갈 수 있는 Node가 없다는 뜻입니다.



### cloud-controller-manager

 Kubernetes의 Service 타입 중 LoadBalancer나 Persistent Volume 같은 기능들은 그냥 PC나 VM에 쿠버네티스를 올린다고 해서 쓸 수 있는 기능들이 아닙니다. 해당 기능들을 사용하려고 YAML를 만들고 kubectl apply하게 되면 무한히 Pending 상태로 대기하는 걸 볼 수 있습니다. 실제로 로드밸런서나 볼륨이 연결되지 않기 때문인데요. 

 cloud-controller-manager가 하는 일은 위의 오브젝트들을 apply했을 때 Cloud Provider(AWS, GCP, Azure 등)의 인프라에 알려서 실제 자원을 할당하는 역할을 합니다. 클라우드의 쿠버네티스 엔진을 사용하시는 분들이라면 이미 해당 매니저를 쿠버네티스 엔진이 사용하고 있다고 보시면 됩니다. 

 기본적으로 Disable이기 때문에 On-premise환경에서 사용할 때는 cloud-controller-manager가 따로 동작하지 않습니다. 또한 그럴 경우는 거의 없겠지만 kubeadm같은 것들을 사용해서 AWS의 EC2나 GCP의 GCE같이 VM을 직접 쿠버네티스 환경으로 만들어주게될 경우 cloud provider를 설정해줘야 cloud-controller-manager가 정상동작합니다.

cloud-controller-manager는 처음부터 manager로써 분리되어 나온 기능은 아니고, 현재 v1.17 기준 beta 버전이니 참고하시기 바랍니다.



## Node components

### kubelet

 Master Node가 각 Node들에게 Pod 생성 요청을 전달할 때 이를 전달아 Pod내의 Container들이 동작하도록 만드는 역할을 합니다. kubelet은 Pod Spec를 받아 그대로 Spec대로 Container를 실행하도록 만드는 역할을 합니다. 때문에 Container가 정상적으로 동작하는지에 대한 HealthCheck도 이루어집니다.

Node별로 1개씩 존재합니다.



### kube-proxy

 Network Proxy의 역할을 하며 Node별로 하나씩 동작합니다. kube-proxy는 Node상의 Network Rule을 설정한대로 동작하도록 관리하는 일을 합니다.

예전에는 메시지들이 도착했을 때 어느 Pod로 동작할지까지 Service Proxy의 역할을 직접 수행했지만(userspace 모드) 성능상의 문제로 현재 default mode는 iptables를 설정하는 iptables모드로 동작합니다. 이마저도 성능상의 문제로 ipvs mode로 동작하도록 개발되고 있습니다.

 

### container runtime

위에서 설명드린 kubelet이 직접 컨테이너를 생성하지는 않습니다. Container runtime은 kubelet의 요청을 받아서 실제로 Container를 실행시키는 역할을 합니다. 

우리가 흔히 듣고 사용하는 Docker가 바로 이 Container runtime의 역할을 하는 것입니다.