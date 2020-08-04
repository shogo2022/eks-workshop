---
title: "Fluent Bitのデプロイ"
date: 2018-08-07T08:30:11-07:00
weight: 30
---

<!--
Let's start by downloading the _fluentbit.yaml_ deployment file and replace some variables.
-->
では、 _fluentbit.yaml_ のdeploymentファイルをダウンロードして、いくつかの変数を置き換えましょう。

```bash
cd ~/environment/logging

# get the Elasticsearch Endpoint
export ES_ENDPOINT=$(aws es describe-elasticsearch-domain --domain-name ${ES_DOMAIN_NAME} --output text --query "DomainStatus.Endpoint")

curl -Ss https://www.eksworkshop.com/intermediate/230_logging/deploy.files/fluentbit.yaml \
    | envsubst > ~/environment/logging/fluentbit.yaml
```

<!--
Explore the file to see what will be deployed. The fluent bit log agent configuration is located in the Kubernetes `ConfigMap` and will be deployed as a `DaemonSet`, i.e. one pod per worker node. In our case, a 3 node cluster is used and so 3 pods will be shown in the output when we deploy.
-->
ファイルの中身を確認して何がデプロイされるかみてみましょう。fluent bitログエージェントの設定はKubernetesの `ConfigMap` にあり、 `DaemonSet` 、書くワーカーノードに1podずつデプロイされます。私たちの場合は、3ノードのクラスタなので、podが3つ出てきます。

```bash
kubectl apply -f ~/environment/logging/fluentbit.yaml
```

<!--
Wait for all of the pods to change to running status
-->
全てのpodがrunningになるのを待ちます

```bash
kubectl --namespace=logging get pods
```

Output

{{< output >}}
NAME               READY   STATUS    RESTARTS   AGE
fluent-bit-2wrs4   1/1     Running   0          6s
fluent-bit-9lkkm   1/1     Running   0          6s
fluent-bit-x545p   1/1     Running   0          6s
{{< /output >}}
