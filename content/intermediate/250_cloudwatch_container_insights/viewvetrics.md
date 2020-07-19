---
title: "収集したメトリクスの閲覧"
chapter: false
weight: 9
---

<!--
Now let's navigate back to CloudWatch Container Insights browser tab to view the data we've generated.
-->
では、生成したデータを見るためにCloudWatch Container Insightsのタブに戻ります。

<!--
From here you can choose a number of different views. We’re going to narrow down our timelines to a custom time range of just 30 minute so we can zoom into our recently collected insights.
-->
ここでは様々なビューを選べます。タイムラインを30分までに縮めて収集したインサイトが見やすいようにします。

<!--
To do so go to the Time Range option at the top right of The CloudWatch Container Insights windows and selecting 30 minutes.
-->
これをするためには、CloudWatch Container Insightsのウインドウの右上にあるTime Rangeのオプションで30分を選択します。

![Metric Time](/images/ekscwci/metrictime.png)

<!--
Once zoomed in on the time frame we can see the large spike in resource usage for the load we just generated to the Wordpress service in our EKS Cluster.
-->
拡大すると、先ほど実施した負荷による大きなスパイクが見えます。

![alt text](/images/ekscwci/metriceksservice.png "Metric Service")
<!--
As mentioned previous you can view some different metrics based on the Dropdown menu options. Let's take a quick look at some of those items.
-->
先に出てきた通り、ドロップダウンのオプションで異なったメトリクスを見ることができます。そのいくつかを見てみましょう。

![alt text](/images/ekscwci/switches.gif "Switching Metrics")
