---
title: "Kubernetesノード"
date: 2018-10-03T10:15:55-07:00
draft: false
weight: 40
---

<!--
The machines that make up a Kubernetes cluster are called **nodes**.
-->
Kubernetesクラスタを構成している機器を**ノード**と呼びます。

<!--
Nodes in a Kubernetes cluster may be physical, or virtual.  
-->
Kubernetesクラスタのノードは物理も仮想でもありえます。

<!--
There are two types of nodes:
-->
ノードには2種類あります：

<!--
* A Control-plane-node type, which makes up the [Control Plane](../../architecture/architecture_control), acts as the “brains” of the cluster.
-->
* [コントロールプレーン](../../architecture/architecture_control)を構成するコントロールプレーンノードは、クラスタの"脳"として動きます。

<!--
* A Worker-node type, which makes up the [Data Plane](../../architecture/architecture_worker), runs the actual container images (via pods).
-->
* [データプレーン](../../architecture/architecture_worker)を構成するワーカーノードは、(pod経由で)実際のコンテナイメージを実行します。

<!--
We’ll dive deeper into how nodes interact with each other later in the presentation.
-->
それぞれのノードの関わりは後の資料で深く説明します。