---
title: "監視と可視化"
date: 2018-11-13T22:42:01+09:00
weight: 60
draft: false
---

<!--
## Collecting new telemetry data
-->
## テレメトリデータの収集

```bash
kubectl apply -f ${HOME}/environment/istio-${ISTIO_VERSION}/samples/bookinfo/telemetry/metrics.yaml
```

<!--
Make sure Prometheus and Grafana are running
-->
PrometheusとGrafanaが動いていることを確認します

```bash
kubectl -n istio-system get svc prometheus grafana
```

{{< output >}}
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
prometheus   ClusterIP   10.100.3.219     <none>        9090/TCP   14h
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
grafana      ClusterIP   10.100.211.214   <none>        3000/TCP   14h
{{< /output >}}

<!--
Open a new terminal tab and setup port-forwarding for Grafana by executing the following command
-->
新しいターミナルタブを開き、次のコマンドでGrafana用のポートフォワーディングを設定します

```bash
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 8080:3000
```

<!--
Open the Istio Dashboard via the Grafana UI
-->
Grafana UIからIstioダッシュボードを開く

<!--
* In your Cloud9 environment, click **Preview / Preview Running Application**
* Scroll to **the end of the URL** and append:
-->
* Cloud9で、 **Preview / Preview Running Application** をクリックします
* **URLの最後** に次を加えます:

```bash
dashboard/db/istio-mesh-dashboard
```

<!--
* Click the 'Pop Out Into New Window' button
-->
* 'Pop Out Into New Window' ボタンをクリックします

![Grafana FullScreen](/images/istio/istio_grafana_fullscreen.png)

<!--
Open a new terminal tab and use these commands to send a traffic to the mesh
-->
新しいターミナルタブを開き、次のコマンドでメッシュにトラフィックを送ります

```bash
export GATEWAY_URL=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].hostname}')

watch --interval 1 curl -s -I -XGET "http://${GATEWAY_URL}/productpage"
```

<!--
You will see that the traffic is evenly spread between <span style="color:orange">reviews:v1</span> and <span style="color:blue">reviews:v3</span>
-->
トラフィックが<span style="color:orange">reviews:v1</span>と<span style="color:blue">reviews:v3</span>に均等に分配されていることが見えます。

![Grafana Dashboard](/images/istio/istio_grafana1.png)

<!--
We encourage you to explore other Istio dashboards that are available by clicking the **Istio Mesh Dashboard** menu on top left of the page, and selecting a different dashboard.
-->
ページの左上の **Istio Mesh Dashboard** から他のダッシュボードを選んで、他の Istioダッシュボードを見てみてください。