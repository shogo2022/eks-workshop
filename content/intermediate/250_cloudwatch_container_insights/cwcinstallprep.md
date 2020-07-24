---
title: "Container Insightsのインストール準備"
chapter: false
weight: 4
---

<!--
{{% notice info %}}
The full documentation for CloudWatch Container Insights can be found [here](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/deploy-container-insights-EKS.html).
{{% /notice %}}
-->
{{% notice info %}}
CloudWatch Container Insightsのドキュメントは[こちら](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/deploy-container-insights-EKS.html)。
{{% /notice %}}

<!--
#### Add the necessary policy to the IAM role for your worker nodes
-->
#### ワーカーノードのIAMロールに必要なポリシーを追加

<!--
In order for `CloudWatch` to get the necessary monitoring info, we need to install the CloudWatch Agent to our EKS Cluster.
-->
`CloudWatch` が必要な監視情報を取得するためには、EKSクラスタに必要なCloudWatchエージェントをインストールする必要があります。

<!--
First, we will need to ensure the Role Name our workers use is set in our environment:
-->
まずは、ワーカーに使われているロール名が環境にセットされているか確認します:

```bash
test -n "$ROLE_NAME" && echo ROLE_NAME is "$ROLE_NAME" || echo ROLE_NAME is not set
```

<!--
{{% notice warning %}}
If `ROLE_NAME` is not set, please review the [test the cluster section](/030_eksctl/test/).
{{% /notice %}}
-->
{{% notice warning %}}
`ROLE_NAME` がセットされていない場合は、[クラスタのテスト](/030_eksctl/test/)セクションを参照してください。
{{% /notice %}}

<!--
We will attach the policy to the nodes IAM Role:
-->
IAMロールにポリシーをアタッチします:

```bash
aws iam attach-role-policy \
  --role-name $ROLE_NAME \
  --policy-arn arn:aws:iam::aws:policy/CloudWatchAgentServerPolicy
```

<!--
Finally, let's verify that the policy has been attached to the IAM ROLE:
-->
最後に、ポリシーがIAMロールにアタッチされたことを確認しましょう:

```bash
aws iam list-attached-role-policies --role-name $ROLE_NAME | grep CloudWatchAgentServerPolicy || echo 'Policy not found'
```

Output
{{< output >}}
"PolicyName": "CloudWatchAgentServerPolicy",
"PolicyArn": "arn:aws:iam::aws:policy/CloudWatchAgentServerPolicy"
{{< /output >}}

<!--
Now we can proceed to the actual install of the CloudWatch Insights.
-->
これでCloudWatch Insightsのインストールに進めます。