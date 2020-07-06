---
title: "K8s オブジェクト詳細 (2/2)"
date: 2018-10-03T10:15:55-07:00
draft: false
weight: 70
---

<!--
### [ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)
* Ensures a defined number of pods are always running
-->
### [ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)
* 決められた数のpodが常に実行されているようにする

<!--
### [Job](https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/)
* Ensures a pod properly runs to completion
-->
### [Job](https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/)
* podが適切に終了するようにする

<!--
### [Service](https://kubernetes.io/docs/concepts/services-networking/service/)
* Maps a fixed IP address to a logical group of pods
-->
### [Service](https://kubernetes.io/docs/concepts/services-networking/service/)
* 決められたIPアドレスをpodのグループに割り当てる

<!--
### [Label](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)
* Key/Value pairs used for association and filtering
-->
### [Label](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)
* 割り当てとフィルターに使われるキー・バリューペア