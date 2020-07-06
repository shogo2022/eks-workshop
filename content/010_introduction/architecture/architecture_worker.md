---
title: "Data Plane"
date: 2018-10-03T10:18:27-07:00
draft: false
weight: 110
---

{{<mermaid>}}
graph TB
internet((internet))
    subgraph worker1
      kubelet1(kubelet)
      kube-proxy1(kube-proxy)
      subgraph docker1
        subgraph podA
          containerA[container]
        end
        subgraph podB
          containerB[container]
        end
      end
    end

  internet-->kube-proxy1
  api-->kubelet1
  kubelet1-->containerA
  kubelet1-->containerB
  kube-proxy1-->containerA
  kube-proxy1-->containerB

  classDef green fill:#9f6,stroke:#333,stroke-width:4px;
  classDef orange fill:#f96,stroke:#333,stroke-width:4px;
  classDef blue fill:#6495ed,stroke:#333,stroke-width:4px;
  class api blue;
  class internet green;
  class kubectl orange;
{{< /mermaid >}}
<!--
* Made up of worker nodes
-->
* ワーカーノードで構成されています

<!--
* kubelet: Acts as a conduit between the API server and the node
-->
* kubelet: APIサーバとノード間の繋ぎとして動きます

<!--
* kube-proxy: Manages IP translation and routing
-->
* kube-proxy: IPの変換とルーティングを管理します

<!--
Check out [the official Kubernetes documentation](https://kubernetes.io/docs/concepts/overview/components/#node-components) for a more in-depth explanation of data plane components.
-->
データプレーンに関するより詳細な説明は[Kubernetesの公式ドキュメント](https://kubernetes.io/docs/concepts/overview/components/#node-components)を参照してください。