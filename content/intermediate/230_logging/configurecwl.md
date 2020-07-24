---
title: "CloudWatchログとKibanaの設定"
date: 2018-08-07T08:30:11-07:00
weight: 40
---

<!--
### Configure CloudWatch Logs Subscription
-->
### CloudWatchログのサブスクリプションの設定

<!--
CloudWatch Logs can be delivered to other services such as Amazon Elasticsearch for custom processing. This can be achieved by subscribing to a real-time feed of log events. A subscription filter defines the filter pattern to use for filtering which log events gets delivered to Elasticsearch, as well as information about where to send matching log events to.
-->
CloudWatchログをAmazon Elasticsearchなどの他のサービスに送り、カスタム処理をすることができます。これはログイベントのリアルタイムフィードにサブスクライブすることで実現できます。Elasticsearchに送るイベントのフィルタパターンや送信先などを、サブスクリプションフィルターで定義します。

<!--
In this section, we’ll subscribe to the CloudWatch log events from the fluent-cloudwatch stream from the eks/eksworkshop-eksctl log group. This feed will be streamed to the Elasticsearch cluster.
-->
このセクションでは、eks/eksworkshop-eksctlロググループからのfluent-cloudwatchストリームのログイベントにサブスクライブします。このフィードはElasticsearchクラスタに配信されます。

<!--
Original instructions for this are available at:
-->
元のガイドは下のリンクから参照できます:

http://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_ES_Stream.html

```
mkdir ~/environment/iam_policy/
cat <<EoF > ~/environment/iam_policy/lambda.json
{
   "Version": "2012-10-17",
   "Statement": [
   {
     "Effect": "Allow",
     "Principal": {
        "Service": "lambda.amazonaws.com"
     },
   "Action": "sts:AssumeRole"
   }
 ]
}
EoF

aws iam create-role --role-name lambda_basic_execution --assume-role-policy-document file://~/environment/iam_policy/lambda.json

aws iam attach-role-policy --role-name lambda_basic_execution --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole
```

<!--
Go to the [CloudWatch Logs console](https://console.aws.amazon.com/cloudwatch/home?#logs:)
-->
[CloudWatchログコンソール](https://console.aws.amazon.com/cloudwatch/home?#logs:)を開きます

<!--
Select the log group `/eks/eksworkshop-eksctl/containers`. Click on `Actions` and select `Stream to Amazon ElasticSearch Service`.
-->
`/eks/eksworkshop-eksctl/containers` ロググループを選択します。 `Action` をクリックし、 `Stream to Amazon ElasticSearch Service` を選択します。
![Stream to ElasticSearch](/images/logging_cwl_es.png)

<!--
Select the ElasticSearch Cluster `kubernetes-logs` and IAM role `lambda_basic_execution`
-->
ElasticSearchクラスタには `kubernetes-logs` 、IAMロールは `lambda_basic_execution` を選択します。

![Subscribing to logs](/images/logging-cloudwatch-es-subscribe-iam.png)

<!--
Click `Next`
-->
`Next` をクリックします

<!--
Select `Common Log Format` and click `Next`
-->
`Common Log Format` を選択し、 `Next` をクリックします

![ES Log Format](/images/logging-cloudwatch-es-subscribe-log-format.png)

<!--
Review the configuration. Click `Next` and then `Start Streaming`
-->
設定を確認します。 `Next` 、そして `Start Streaming` をクリックします

![Review ES Subscription](/images/logging-cloudwatch-es-subscribe-confirmation.png)

<!--
Cloudwatch page is refreshed to show that the filter was successfully created
-->
Cloudwatchページが更新され、フィルタが作成されたことが表示されます

<!--
### Configure Kibana
-->
### Kibanaの設定

<!--
In Amazon Elasticsearch console, select the [kubernetes-logs under My domains](https://console.aws.amazon.com/es/home?#domain:resource=kubernetes-logs;action=dashboard)
-->
Amazon Elasticsearchコンソールで、[My domainsの下のkubernetes-logs](https://console.aws.amazon.com/es/home?#domain:resource=kubernetes-logs;action=dashboard)を選択します

![ElasticSearch Details](/images/logging_es_details.png)

<!--
Open the Kibana dashboard from the link. After a few minutes, records will begin to be indexed by ElasticSearch. You'll need to configure an index patterns in Kibana.
-->
リンクからKibanaのダッシュボードを開きます。数分のうちに、ElasticSearchがレコーッドのインデックスをはじめます。インデックスパターンをKibanaで設定しましょう。

<!--
Set `Index Pattern` as **cwl-\*** and click `Next`
-->
`Index Pattern` を **cwl-\*** にセットし、 `Next` をクリックします

![Index Pattern](/images/logging_index_pattern.png)

<!--
Select `@timestamp` from the dropdown list and select `Create index pattern`
-->
ドロップダウンから `@timestamp` を選択し、 `Create index pattern` を選びます

![Index Pattern](/images/logging_time_filter.png)

![Kibana Summary](/images/logging_kibana.png)

<!--
Click on `Discover` and explore your logs
-->
`Discover` をクリックしてログを確認します

![Kibana Dashboard](/images/logging_kibana_dashboard.png)
