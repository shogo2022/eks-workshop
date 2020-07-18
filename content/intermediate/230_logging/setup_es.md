---
title: "Elasticsearchクラスターの設定"
date: 2018-08-07T08:30:11-07:00
weight: 20
---

<!--
This example creates a two instance Amazon Elasticsearch cluster named kubernetes-logs. This cluster is created in the same region as the Kubernetes cluster and CloudWatch log group. 
-->
この例では、kubernetes-logsという2インスタンスのAmazon Elasticsearchクラスタを作成します。このクラスタはKubernetesクラスタやCloudWatchロググループと同じリージョンに作成されます。

<!--
{{% notice warning %}}
Note that this cluster has an open access policy which will need to be locked down in production environments.
{{% /notice %}}
-->
{{% notice warning %}}
このクラスタはアクセスは公開されていますが、商用環境では閉じるようにしてください。
{{% /notice %}}

```
aws es create-elasticsearch-domain \
  --domain-name kubernetes-logs \
  --elasticsearch-version 6.3 \
  --elasticsearch-cluster-config \
  InstanceType=m5.large.elasticsearch,InstanceCount=2 \
  --ebs-options EBSEnabled=true,VolumeType=standard,VolumeSize=100 \
  --access-policies '{"Version":"2012-10-17","Statement":[{"Effect":"Allow","Principal":{"AWS":["*"]},"Action":["es:*"],"Resource":"*"}]}'
```

<!--
It takes a little while for the cluster to be created and arrive at an active state. The AWS Console should show the following status when the cluster is ready. 
-->
クラスタが作成されアクティブになるまでは少し時間がかかります。クラスタが準備出来ると、AWSコンソールが以下のような表示になるはずです。

![Elasticsearch Dashboard](/images/logging_es_dashboard.png)

<!--
You could also check this via AWS CLI:
-->
AWS CLIでも確認できます:
```
aws es describe-elasticsearch-domain --domain-name kubernetes-logs --query 'DomainStatus.Processing'
```
<!--
If the output value is false that means the domain has been processed and is now available to use.
-->
falseが返ってくれば、ドメインが処理され使用可能になっています。

<!--
Feel free to move on to the next section for now.
-->
次のセクションにいきましょう。