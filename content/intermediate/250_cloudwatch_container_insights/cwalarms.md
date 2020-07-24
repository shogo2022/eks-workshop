---
title: "CloudWatch Alarmsを使う"
chapter: false
weight: 11
---

<!--
You can use the CloudWatch metrics to generate various alarms for your EKS Cluster based on assigned metrics.
-->
EKSクラスタに関する様々なアラームを出すためにCLoudWatchを使うことができます。

<!--
In _CloudWatch Container Insights_ we’re going to drill down to create an alarm using CloudWatch for CPU Utilization of the Wordpress service.
-->
_CloudWatch Container Insights_ から、WordpressサービスのCPU使用率のCloudWatchを使い、アラームを作成します。

<!--
To do so:
-->
次を実行します:

<!--
* Click on the three vertical dots in the upper right of the CPU Utilization box.
* Select View in Metrics.
-->
* CPU使用率の右上にある縦3つの点をクリック
* Metricsでビューを選択

![View Metrics](/images/ekscwci/viewinmetrics.png)

<!--
This will isolate us to a single pane view of CPU Utilization for the eksworkshop-eksctl cluster.
-->
これはeksworkshop-eksctlクラスタのCPU使用率の一覧を標示します。

![Metrics View](/images/ekscwci/metricsview.png)

<!--
From this window we can create alarms for the understood-zebu-wordpress service so we know when it’s under heavy load.
-->
このウインドウからunderstood-zebu-wordpressサービスが大きい負荷に晒された時に亜アラームが生成されるようにアラームを作成できます。

<!--
{{% notice info %}}
For this lab we’re going to set the threshold low so we can guarantee to set it off with the load test.
{{% /notice %}}
-->
{{% notice info %}}
このハンズオンでは閾値を低く設定し、負荷試験で確実に発報されるようにします。
{{% /notice %}}

<!--
To create an alarm, click on the small bell icon in line with the Wordpress service.
This will take you to the metrics alarm configuration screen.
-->
アラームを作成するには、Wordpressサービスと同じラインにある小さなベルアイコンをクリックします。
これでメトリクスアラームの設定ページが開きます。

![Alarm Config](/images/ekscwci/alarmconfig.png)

<!--
As we can see from the screen we peaked CPU at over 6 % so we’re going to set our metric to 3% to assure it sets off an alarm. Set your alarm to 50% of whatever you max was during the load test on the graph.
-->
表示から見えるようにCPUのピークは6%なので、確実にアラームが出るようにメトリクスは3%に設定します。グラフに表示されている値であらアームの設定は変えてください。

<!--
Click next on the bottom and continue to _Configure Actions_.
-->
最下部のnextを押し、 _Configure Actions_ に進みます。

<!--
We’re going to create a configuration to send an SNS alert to your email address when CPU gets above your threshold.
-->
CPUが閾値を超えた時にあなたのEメールアドレスにSNSアラートが送られるように設定します。

<!--
On the Configure Action screen:
-->
Configure Action画面で次を実施します:

<!--
* Leave default of _in Alarm_.
* Select _Create new topic_ under Select and SNS Topic.
* In _Create new topic..._ name it wordpress-CPU-alert.
* In Email Endpoints enter your email address.
* Click create topic.
-->
* デフォルトの _in Alarm_ のまま
* Select an SNS Topicで _Create new topic_ を選択
* _Create new topic..._ にwordpress-CPU-alertと入力
* Email Endpointsに自身のEメールアドレスを入力
* create topicをクリック

![Config Actions](/images/ekscwci/configactions.png)

<!--
Once those items are set, you can click Next at the bottom of the screen.
-->
これらを入力したら、スクリーン最下部のNextをクリックします。

<!--
On the next screen we’ll add a unique name for our alert, and press Next.
-->
次の画面でアラートに任意の名前をつけ、Nextを押します。

![Alert Description](/images/ekscwci/alertdescription.png)

<!--
The next screen will show your metric and the conditions. Make sure to click create alarm. 
-->
次の画面ではメトリックとコンディションが表示されます。create alarmを押し忘れないでください。

![Create Alarm](/images/ekscwci/createalarm.png)

<!--
After creating your new SNS topic you will need to verify your subscription in your email.
-->
新しいSNSトピックを作成したら、emailのサブスクリプションを確認する必要があります。
![SNS Email](/images/ekscwci/snsemail.png)

<!--
#### Testing your alarm
-->
#### アラームのテスト

<!--
For the last step of this lab, we’re going to run one more load test on our site to verify our alarm triggers.  Go back to your Cloud9 terminal and run the same commands we can previously to load up our Wordpress site.
-->
ハンズオンの最後の手順として、もう一度負荷テストをサイトに流し、アラームが出るのを確認します。Cloud9のターミナルで、先の手順と同じくWordpressサイトに負荷をかけてください。

i.e.

```bash
export WP_ELB=$(kubectl -n wordpress-cwi get svc understood-zebu-wordpress -o jsonpath="{.status.loadBalancer.ingress[].hostname}")

siege -q -t 15S -c 200 -i ${WP_ELB}
```

<!--
In a minute or two, you should receive and email about your CPU being in alert. If you don’t verify your SNS topic configuration and that you’ve accepted the subscription to the topic.
-->
1、2分でCPUでアラートが出たEメールを受け取るはずです。受け取っていな合い場合は、SNSトピックの設定と、トピックへのサブスクリプションを承認したことを確認してください。

![Sample SNS Email](/images/ekscwci/sampleemail.png)
