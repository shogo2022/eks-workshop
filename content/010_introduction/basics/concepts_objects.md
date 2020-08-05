---
title: "K8s オブジェクト概要"
date: 2018-10-03T10:15:55-07:00
draft: false
weight: 50
---

<!--
Kubernetes objects are entities that are used to represent the state of the cluster.  
-->
Kubernetesのオブジェクトはクラスタの状態を表す要素です。

<!--
An object is a “record of intent” – once created, the cluster does its best to ensure it exists as defined.  This is known as the cluster’s “desired state.”
-->
オブジェクトは”目指すべきレコード”です - クラスタはできる限りそこに定義されたことを守るように努め、その状態を"desired state(望ましい状態)"としています。

<!--
Kubernetes is always working to make an object’s “current state” equal to the object’s “desired state.”  A desired state can describe:
-->
Kubernetesは常にオブジェクトの"現在の状態"を、オブジェクトの"望ましい状態"と同じにするように動いています。望ましい状態は以下のように定義されます:

<!--
* What pods (containers) are running, and on which nodes
* IP endpoints that map to a logical group of containers
* How many replicas of a container are running
* And much more...
-->
* どのpod(コンテナ)がどのノードで実行されているか
* コンテナの論理的なグループに割り当てられるIPエンドポイント
* 実行されるコンテナのレプリカの数
* その他いろいろ...

<!--
Let’s explain these k8s objects in a bit more detail...
-->
これらのk8sオブジェクトについてもう少し説明しましょう...