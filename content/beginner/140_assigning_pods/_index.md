---
title: "ノードへのPod配置"
chapter: true
weight: 140
tags:
  - intermediate
  - CON203
---

<!--
# Assigning Pods to Nodes
### Introduction
-->
# ノードへのPod配置
### 紹介

<!--
In this Chapter, we will review how the strategy of assigning Pods works, alternatives and recommended approaches.
-->
この章では、Podの配置がどのように行われているのか、代替方法と推奨されている方法をみていきます。

<!--
You can constrain a pod to only be able to run on particular nodes or to prefer to run on particular nodes.
-->
podは特定のノードで動かすように制限することもできますし、特定のノードを優先的に選ぶようにすることもできます。

<!--
Generally such constraints are unnecessary, as the scheduler will automatically do a reasonable placement (e.g. spread your pods across nodes, not place the pod on a node with insufficient free resources, etc.) but there are some circumstances where you may want more
control on a node where a pod lands, e.g. to ensure that a pod ends up on a machine with an SSD attached to it, or to co-locate pods from two different services that communicate a lot into the same availability zone.
-->
通常はスケジューラが合理的に配置(例 ノード全体にpodを配置したり、空きリソースが不足しているノードに位は配置しないなど)を自動でしてくれるのでこのような考慮は必要ないですが、SSDのノードに配置したい場合や、頻繁にやりとりをする2つの異なるサービスのpodを同じアベイラビリティゾーンの中で動かしたい場合など、制御したい場合があるかもしれません。
