---
title: "CloudwatchのフローログとKubernetesネットワークフローログの連携"
weight: 150
---
<!--
Now that we have policies installed, and traffic being generated in the cluster, we can look at the [CloudWatch](https://aws.amazon.com/cloudwatch/) integration that TSCE provides.
-->
ポリシーがインストールされ、クラスター内で通信が発生しているので、TSCEの[CloudWatch](https://aws.amazon.com/cloudwatch/)連携をみていきましょう。

<!--
We're assuming that you have run through both the Calico section of this tutorial and the first part of the TSCE section.  If you skipped the Calico section, please go back and run through that as well, as we are relying on the synthetic applications used in the Calico examples to generate flowlogs in CloudWatch.
-->
ここからは、このワークショップのCalicoセクションとTSCEの前半部分に沿ってきたと想定して進めます。Calicoセクションを飛ばしてきた場合、戻って手順に沿ってください。CloudWatchのフローログ生成にCalicoで使用したデモアプリケーションを使用します。

<!--
# Selecting out cluster in CloudWatch
-->
# CloudWatchでクラスタを選ぶ

<!--
You need to get the clusterGUID of the EKS cluster.  To do that, run the following command:
-->
EKSクラスタのclusterGUIDを次のコマンドで取得します:

```
kubectl get clusterinformation default -o yaml | grep GUID
```

<!--
In the cloud9 shell, go to the AWS dashboard, and select the Cloudwatch service.
-->
Cloud9のshellでAWSダッシュボードにいき、CloudWatchサービスを選択します。

<!--
# Viewing and graphing metrics
-->
# メトリクスの閲覧およびグラフ

<!--
Go to the metrics section, and select all of the metrics being reported by your clusterID.  See the screenshot below
-->
メトリクスセクションにいき、自身のclusterIDからレポートされているメトリクスを全て選択します。下のスクリーンショットをみてください

![](/images/tsce-cloudwatch-metrics.png)

<!--
Just as in the screenshot, you should now see CloudWatch graphing various statistics such as denied packets, unhealthy nodes, *etc.*
-->
スクリーンショットにあるように、拒否されたパケットや正常でないノード *など* の様々な統計がグラフとして表示されています。

<!--
# Viewing the flow logs
-->
# フローログの閲覧

<!--
Similarly, you can see the actual flow logs by switching from the *Metrics* to the *Logs* view, as shown below
-->
同じように、実際のログも *Metrics* から *Logs* に切り替えることで見ることができます

![](/images/tsce-cloudwatch-logs.png)
