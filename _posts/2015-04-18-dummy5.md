---
layout: post
title:  "Kubernetes Service Account(쿠버네티스 서비스 어카운트)"
date:   2019-11-10T19:11:00
author: Taemy
categories: Kubernetes
tags: Serviceaccount
---

쿠버네티스의 컨트롤 플레인 중 API Server는 두 가지 클라이언트를 가정한다. 하나는 유저이고, 하나는 파드(Pod)다. 유저는 시스템 인증 플러그인에 의해 인증되지만 파드에 대한 인증은 어떻게 이루어지는 것일까? 이 과정을 위해 필요한 것이 바로 서비스 어카운트(Service Account, SA)다.

