---
title: "コントロールプレーン"
date: 2018-10-03T10:18:27-07:00
draft: false
weight: 100
---

{{<mermaid>}}
graph TB
kubectl{kubectl}
  subgraph ControlPlane
    api(API Server)
    controller(Controller Manager)
    scheduler(Scheduler)
    etcd(etcd)
  end

  kubectl-->api
  controller-->api
  scheduler-->api
  api-->kubelet
  api-->etcd

  classDef green fill:#9f6,stroke:#333,stroke-width:4px;
  classDef orange fill:#f96,stroke:#333,stroke-width:4px;
  classDef blue fill:#6495ed,stroke:#333,stroke-width:4px;
  class api blue;
  class internet green;
  class kubectl orange;
{{< /mermaid >}}

<!--
* One or More API Servers: Entry point for REST / kubectl
-->
* APIサーバがひとつ以上: RESTとKubectlのエントリーポイント

<!--
* etcd: Distributed key/value store
-->
* etcd: 分散型のキー・バリューストア

<!--
* Controller-manager: Always evaluating current vs desired state
-->
* コントローラマネージャ: 常に現状とあるべき状態を比較する

<!--
* Scheduler: Schedules pods to worker nodes
-->
* スケジューラ: ワーカーノードにpodをスケジュールする

<!--
Check out [the official Kubernetes documentation](https://kubernetes.io/docs/concepts/overview/components/#master-components) for a more in-depth explanation of control plane components.
-->
コントロールプレーンに関するより詳細な説明は[Kubernetesの公式ドキュメント](https://kubernetes.io/docs/concepts/overview/components/#master-components)を参照してください。