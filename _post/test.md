---
layout: post
title:  "Kubernetes Component"
date:   2020-03-23 19:26:00
author: tommyhwang
categories: Kubernetes
tags:	Kubernetes, k8s
cover:  "/assets/instacode.png"
---

앞서 소개드린 Kubernetes의 기본 구성을 살펴보도록 하겠습니다.

Kubernetes를 사용하게 되면 여러 Node를 하나의 Cluster를 구성해서 관리하게 됩니다.

 Node(혹은 Worker Node)는 Cluster내에서 컴퓨터나 VM 같이 리소스를 가지고 컨테이너들의 실제로 올라가서 동작하는 Worker Machine을 의미합니다. 여러분의 PC 1대, 안에 있는 VM 1대, AWS의 EC2를 모두 Node 단위로 볼 수 있습니다.

Cluster는 이러한 Node들의 집합을 의미하고, 최소 하나 이상의 Node를 포함합니다. 또한 Cluster에는 이 Node들을 Control하기 위한 구성요소들이 포함된 Master Node가 포함됩니다. 즉 Cluster에는 Master Node와 최소 1개 이상의 Node가 포함됩니다. 

 Master Node를 Node로 동작시키는 것도 가능합니다. 즉 Node 하나만 있어도 Cluster를 구성하는 것이 가능합니다.(물론 그런 상황이면 Kubernetes를 운영할 이유가 있는진 모르겠지만) 여담으로 minikube로 Cluster를 구성하면 자동으로 Master Node를 VM으로 생성하고 init한 위치의 PC를 Node로 만듭니다. Master Node 1개와 Node 1개로 구성된 클러스터를 생성하는 겁니다.



그렇다면 Master Node와 Node를 구분하는 기준은 뭘까요?

Master Node에는 Cluster 전체를 오케스트레이션하기 위한 Control Plane 요소들이 포합됩니다.



쿠버네티스 공식 문서에 아래의 그림을 보시면 이해가 빠르실 수 있습니다.

![Components of Kubernetes](https://d33wubrfki0l68.cloudfront.net/7016517375d10c702489167e704dcb99e570df85/7bb53/images/docs/components-of-kubernetes.png)

contole plane components

kube-controller-manager

kube-api-server

etcd

kube-scheduler

cloud-controller-manager



node components
