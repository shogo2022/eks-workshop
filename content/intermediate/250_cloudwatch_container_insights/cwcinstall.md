---
title: "Container Insightsのインストール"
chapter: false
weight: 5
---

<!--
To complete the setup of Container Insights, you can follow the quick start instructions in this section.
-->
Container Insightsの設定を完了するには、このセクションのクイックスタートにしたがってください。

<!--
From your Cloud9 Terminal you will just need to run the following command.
-->
Cloud9のターミナルから次を実行します。

```bash
curl -s https://raw.githubusercontent.com/aws-samples/amazon-cloudwatch-container-insights/latest/k8s-deployment-manifest-templates/deployment-mode/daemonset/container-insights-monitoring/quickstart/cwagent-fluentd-quickstart.yaml | sed "s/{{cluster_name}}/eksworkshop-eksctl/;s/{{region_name}}/${AWS_REGION}/" | kubectl apply -f -
```

<!--
The command above will:
-->
上のコマンドは次を実行します:

<!--
* Create the `Namespace` amazon-cloudwatch.
* Create all the necessary security objects for both DaemonSet:
  * `SecurityAccount`.
  * `ClusterRole`.
  * `ClusterRoleBinding`.
* Deploy Cloudwatch-Agent (responsible for sending the **metrics** to CloudWatch) as a `DaemonSet`.
* Deploy fluentd (responsible for sending the **logs** to Cloudwatch) as a `DaemonSet`.
* Deploy `ConfigMap` configurations for both DaemonSets.
-->
* amazon-cloudwatch `Namespace` の作成
* 両方のDaemonSetに必要なセキュリティオブジェクトの作成:
  * `SecurityAccount`
  * `ClusterRole`
  * `ClusterRoleBinding`
* Cloudwatch-Agent ( **メトリクス** をCloudWatchに送信する) を `DaemonSet` としてデプロイ
* fluentd ( **ログ** をCloudwatchに送信する) を `DaemonSet` としてデプロイ
* `ConfigMap` を両方のDaemonSetにデプロイ

<!--
{{% notice info %}}
You can find the full information and manual install steps [here](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-setup-EKS-quickstart.html).
{{% /notice %}}
-->
{{% notice info %}}
詳細情報と手動でのインストール手順は[こちら](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-setup-EKS-quickstart.html)
{{% /notice %}}

<!--
You can verify all the `DaemonSets` have been deployed by running the following command.
-->
すべての `DaemonSets` がデプロイされて稼働しているかを次のコマンドで確認します。

```bash
kubectl -n amazon-cloudwatch get daemonsets
```

<!--
Output
-->
出力

{{< output >}}
NAME                 DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
cloudwatch-agent     3         3         3       3            3           <none>          2m43s
fluentd-cloudwatch   3         3         3       3            3           <none>          2m43s
{{< /output >}}

<!--
That's it. It's that simple to install the agent and get it up and running. You can follow the manual steps in the full documentation, but with the Quickstart the deployment of the Daemon is easy and quick!
-->
これで終わりです。エージェントをインストールして動かすのはこんなに簡単です。ドキュメントの手順でマニュアルインストールができますが、デーモンを使ったクイックスタートでは簡単に早くデプロイできます!

<!--
### Now onto verifying the data is being collected!
-->
### ではデータが収集されているか見てみましょう!