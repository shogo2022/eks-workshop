---
title: "X-Rayコンソール"
date: 2018-11-177T08:30:11-07:00
weight: 13
draft: false
---

<!--
We now have the example microservices deployed, so we are going to investigate our [Service Graph](https://docs.aws.amazon.com/xray/latest/devguide/xray-concepts.html#xray-concepts-servicegraph) and [Traces](https://docs.aws.amazon.com/xray/latest/devguide/xray-concepts.html#xray-concepts-traces) in [X-Ray section of the AWS Management Console](https://console.aws.amazon.com/xray/home#/service-map).
-->
サンプルのマイクロサービスがデプロイされたので、[AWS管理コンソールのX-Rayセクション](https://console.aws.amazon.com/xray/home#/service-map)で[Service Graph](https://docs.aws.amazon.com/xray/latest/devguide/xray-concepts.html#xray-concepts-servicegraph)と[Traces](https://docs.aws.amazon.com/xray/latest/devguide/xray-concepts.html#xray-concepts-traces)を確認します。

<!--
The [Service map in the console](https://console.aws.amazon.com/xray/home#/service-map) provides a visual representation of the steps identified by X-Ray for a particular trace. Each resource that sends data to X-Ray within the same context appears as a service in the graph. In the example below, we can see that the **x-ray-sample-front-k8s** service is processing 39 transactions per minute with an average latency of 0.99ms per operation. Additionally, the **x-ray-sample-back-k8s** is showing an average latency of 0.08ms per transaction.
-->
[コンソールのService map](https://console.aws.amazon.com/xray/home#/service-map)は、あるトレースにおいてX-Rayが識別した項目を資格的に表現しています。同じコンテキストでX-Rayにデータを送っているそれぞれのリソースはサービスとしてグラフに表示されます。下の例では **x-ray-sample-front-k8s** は毎分39のトランザクションを処理しており、各オペレーションの平均遅延は0.99msであることがわかります。また、 **x-ray-sample-back-k8s** は各トランザクションの平均遅延が0.08msとなっています。

![GitHub Edit](/images/x-ray/service_map.png)

<!--
Next, go to the [traces section in the AWS Management Console](https://console.aws.amazon.com/xray/home#/traces) to view the execution times for the segments in the requests. At the top of the page, we can see the URL for the ELB endpoint and the corresponding traces below.
-->
次に[AWS管理コンソールのtraces](https://console.aws.amazon.com/xray/home#/traces)を開き、リクエストの実行時間を確認します。ページの最上部にはELBエンドポイントがあり、その下に関係したトレースが表示されています。

![GitHub Edit](/images/x-ray/traces.png)

<!--
If you click on the link on the left in the **Trace list** section you will see the overall execution time for the request (0.5ms for the **x-ray-sample-front-k8s** which wraps other segments and subsegments), as well as a breakdown of the individual [segments](https://docs.aws.amazon.com/xray/latest/devguide/xray-concepts.html#xray-concepts-segments) in the request. In this visualization, you can see the front-end and back-end segments and a [subsegment](https://docs.aws.amazon.com/xray/latest/devguide/xray-concepts.html#xray-concepts-subsegments) named **x-ray-sample-back-k8s-gen** In the [back-end service source code](https://github.com/aws-samples/eks-workshop/blob/main/content/intermediate/245_x-ray/sample-back.files/main.go#L35), we instrumented a subsegment that surrounds a random number generator.
-->
 **Trace list** の左にあるリンクをクリックすると、リクエストの全体実行時間(他のセグメントをラップしている **x-ray-sample-front-k8s** は0.5ms), そしてリクエスト内の各[セグメント](https://docs.aws.amazon.com/xray/latest/devguide/xray-concepts.html#xray-concepts-segments)の実行時間も見えます。この表示では、フロントエンドとバックエンドセグメント、そして **x-ray-sample-back-k8s-gen** という[サブセグメント](https://docs.aws.amazon.com/xray/latest/devguide/xray-concepts.html#xray-concepts-subsegments)があります。[バックエンドサービスのソースコード](https://github.com/aws-samples/eks-workshop/blob/main/content/intermediate/245_x-ray/sample-back.files/main.go#L35)で、ランダムな数を生成するジェネレータを含むサブセグメントを入れています。

<!--
In the Go example, [the main segment](https://github.com/aws-samples/eks-workshop/blob/main/content/intermediate/245_x-ray/sample-back.files/main.go#L26) is initialized in the xray.Handler helper, which in turn sets all necessary information in the http.Request context struct, so that it can be used when initializing the subsegment.
-->
Goの例では、[メインのセグメント](https://github.com/aws-samples/eks-workshop/blob/main/content/intermediate/245_x-ray/sample-back.files/main.go#L26)はxray.Handlerヘルパーで初期化されていて、それは次にhttp.Requestのコンテキストに必要な情報を設定し、サブセグメントを初期化されるのに使われます。

![GitHub Edit](/images/x-ray/trace.png)

<!--
{{% notice tip %}}
Click on the image to zoom
{{% /notice %}}
-->
{{% notice tip %}}
イメージをクリックするとズームできます
{{% /notice %}}

