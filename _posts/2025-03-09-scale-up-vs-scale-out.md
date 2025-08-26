---
title: "Scale-Up vs Scale-Out"
description: "기존 개발하던 서비스의 서버가 한계에 다다랐을 때 Scale-Up (스케일 업) 혹은 Scale-Out (스케일 아웃) 을 고려한다.스케일 업은 기존의 서버를 더 높은 사양으로 업그레이드하는것이다. 예를들면 AWS EC2 t2.micro에서 t2.small로 업그레이드"
date: 2025-03-08T20:21:11.630Z
tags: ["Java","Spring","백엔드"]
slug: "Scale-Up-vs-Scale-Out"
thumbnail: "/assets/posts/5e5877c427fe0ca3040dd570d05f00b8c98015db8c60ac4318efde3c75ae0cfc.png"
categories: 공부
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:02:14.869Z
  hash: "b172012ece2b8e9bda324479d53498c4b31327141080e840c20553b7bc7f2ec1"
---

기존 개발하던 서비스의 서버가 한계에 다다랐을 때 **Scale-Up (스케일 업)** 혹은 **Scale-Out (스케일 아웃)** 을 고려한다.

### Scale-Up
스케일 업은 기존의 **서버를 더 높은 사양으로 업그레이드**하는것이다. 예를들면 AWS EC2 t2.micro에서 t2.small로 업그레이드 하는 방식이다. 
스케일 업은 상대적으로 간단히 서버의 성능을 향상시킬 수 있다는 장점이 있다. 하지만, 특정 서버를 무한정 업그레이드 할 수 없으며, 장애에 대한 자동복구 (failover) 나 다중화(re-dudancy)방안을 제시하지 않는다. 또한 스케일 업 전략을 선택하는 경우에는 향후 사용량을 미리 추정하여 미리 고사양의 서버를 확보하는 경우가 있다. 이러한 경우 실제 필요한 서버의 사양보다 과한 사양의 장비를 확보할 수 있기 때문에 비용적인 손실이 존재할 수 있다.

### Scale-Out
스케일 아웃은 **비슷한 사양의 장비를 추가**하여 수평으로 확장하는 방식이다. 서버로 들어오는 많은 요청을 비슷한 사양의 서버 n대로 분산시켜 성능을 향상시킨다. 스케일 아웃은 그때그때 필요한 만큼 서버를 추가할 수 있으므로 상대적으로 스케일 업 방식보다 비용 효율적일 수 있다. 또한, 특정 서버의 장애 발생 상황에서도 스케일업 방식보다 가용성이 높다. 하지만 스케일 아웃 방식은 n대의 서버를 관리해야 하므로 관리 포인트가 늘어나며, 각 서버에 부하를 분산하기 위한 로드 밸런싱에 대한 고민이 추가로 필요하다는 단점이 있다. 

![](/assets/posts/7fdfd01ead9596f269960f4145227e7ef2b91f98a9ae0de89c8b424064648743.png)

### References
- [scale-up vs scale-out](https://www.youtube.com/watch?v=6wPr2jgdDxM)