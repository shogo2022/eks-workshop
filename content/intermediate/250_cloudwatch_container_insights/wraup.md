---
title: "まとめ"
chapter: false
weight: 12
---

<!--
#### Wrapping Up
-->
#### まとめ

<!--
As you can see it’s fairly easy to get CloudWatch Container Insights to work, and set alarms for CPU and other metrics.
-->
CloudWatch Container Insightsを入れ、CPUや他のメトリクスに基づくアラームを設定するのは簡単です。

<!--
With CloudWatch Container Insights we remove the need to manage and update your own monitoring infrastructure and allow you to use native AWS solutions that you don’t have to manage the platform for.
-->
CloudWatch Container Insightsを使えば、自身の監視環境の管理やアップデートが必要なくなり、管理の必要がないネイティブのAWSソリューションを使うことができます。

***

<!--
#### Cleanup your Environment
-->
#### 環境の後片付け

<!--
Let's clean up Wordpress so it's not running in your cluster any longer.
-->
Wordpressをクラスタから削除しましょう。

```bash
helm -n wordpress-cwi uninstall understood-zebu

kubectl delete namespace wordpress-cwi
```

<!--
Run the following command to delete Container Insights from your cluster.
-->
次を実行してContainer Insightsをクラスタから削除します。

```bash
curl -s https://raw.githubusercontent.com/aws-samples/amazon-cloudwatch-container-insights/latest/k8s-deployment-manifest-templates/deployment-mode/daemonset/container-insights-monitoring/quickstart/cwagent-fluentd-quickstart.yaml | sed "s/{{cluster_name}}/eksworkshop-eksctl/;s/{{region_name}}/${AWS_REGION}/" | kubectl delete -f -
```

<!--
Delete the SNS topic and the subscription.
-->
SNSトピックとサブスクリプションを削除します。

<!--
```bash
# Delete the SNS Topic
aws sns delete-topic \
  --topic-arn arn:aws:sns:${AWS_REGION}:${ACCOUNT_ID}:wordpress-CPU-Alert

# Delete the subscription
aws sns unsubscribe \
  --subscription-arn $(aws sns list-subscriptions | jq -r '.Subscriptions[].SubscriptionArn')
```
-->
```bash
# SNSトピックの削除
aws sns delete-topic \
  --topic-arn arn:aws:sns:${AWS_REGION}:${ACCOUNT_ID}:wordpress-CPU-Alert

# サブスクリプションの削除
aws sns unsubscribe \
  --subscription-arn $(aws sns list-subscriptions | jq -r '.Subscriptions[].SubscriptionArn')
```

<!--
Finally we will remove the _CloudWatchAgentServerPolicy_ policy from the Nodes IAM Role
-->
最後にノードのIAMロールから _CloudWatchAgentServerPolicy_ を削除します

```bash
aws iam detach-role-policy \
  --policy-arn arn:aws:iam::aws:policy/CloudWatchAgentServerPolicy \
  --role-name ${ROLE_NAME}
```

<!--
## Thank you for using CloudWatch Container Insights!
-->
## CloudWatch Container Insightsのご利用ありがとうございました!