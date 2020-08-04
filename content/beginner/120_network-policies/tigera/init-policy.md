---
title: "nえっとワークポリシーの初期化"
weight: 200
draft: true
---

<!--
Now that we've seen that all the traffic is being allowed in the cluster, let's start tightening the policies, and restrict some of the traffic.  To do that, we will first create a _default deny_ policy that will block all inbound traffic in our default namespace unless it is specifically allowed.  To do that, we create a null policy that matches everything in the default namespace.
-->
現在はクラスタ内の全通信が許可されているので、ポリシーを厳しくしていくつかの通信を制限してみましょう。これをするためには、まず  _default deny_ ポリシーを作成し、明示的に許可された通信以外は全ての入力通信を拒否するようにします。これをするためには、デフォルトの名前空間で全てに合致する空のポリシーを作成します。

<!--
The YAML fragment that defines such a policy can be seen below
-->
それを実現するポリシーのYAMLは次の通りです

{{< output >}}
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: default-deny
spec:
  podSelector:
    matchLabels: {}
{{< /output >}}

<!--
Now create a file called _default-deny.yaml_ with the above contents and install it in your cluster using kubectl.
-->
この内容で _default-deny.yaml_ というファイルを作成し、kubectlであなたのクラスタに適用してください。

```
$ kubectl apply -f default-deny.yaml
```
