---
title: "Horizontal Pod AutoScaler (HPA)の設定"
date: 2018-08-07T08:30:11-07:00
weight: 10
---

<!--
### Deploy the Metrics Server
Metrics Server is a cluster-wide aggregator of resource usage data. These metrics will drive the scaling behavior of the [deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/). We will deploy the metrics server using `Helm` configured in a previous [module](/beginner/060_helm/helm_intro/install/index.html)
-->
### メトリクスサーバのデプロイ
メトリクスサーバはクラスター全体のリソース使用量データをまとめます。これらのメトリクスは[deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)のスケーリングに影響を及ぼします。以前の[モジュール](/beginner/060_helm/helm_intro/install/index.html)で設定した`Helm`を使ってメトリクスサーバをでデプロイします。

<!--
```sh
# create the metrics-service namespace first
kubectl create namespace metrics

helm install metrics-server \
    stable/metrics-server \
    --version 2.9.0 \
    --namespace metrics
```
-->
```sh
# まずはメトリクスサービスの名前空間を作ります
kubectl create namespace metrics

helm install metrics-server \
    stable/metrics-server \
    --version 2.9.0 \
    --namespace metrics
```

<!--
2020-04-22: some versions of EKS may get the following error once you [create the HPA in the next step](https://eksworkshop.com/scaling/test_hpa/) 
-->
2020-04-22: 特定のEKSバージョンでは[次の手順でHPAを作成すると](https://eksworkshop.com/scaling/test_hpa/) 次のエラーが出るかもしれません。

```
kubectl describe hpa
... failed to get cpu utilization: unable to get metrics for resource cpu
```

<!--
To fix, set the following values for the metric-server helm release ([as described here](https://dev.to/setevoy/kubernetes-running-metrics-server-in-aws-eks-for-a-kubernetes-pod-autoscaler-4m9)):
-->
これを直すには、[ここにあるように](https://dev.to/setevoy/kubernetes-running-metrics-server-in-aws-eks-for-a-kubernetes-pod-autoscaler-4m9)metric-server helmリリースに次の値を設定します:

```
args:
  - --kubelet-preferred-address-types=InternalIP
```

<!--
### Confirm the Metrics API is available.
-->
### メトリクスAPIの確認

<!--
Return to the terminal in the Cloud9 Environment
```
kubectl get apiservice v1beta1.metrics.k8s.io -o yaml
```
If all is well, you should see a status message similar to the one below in the response
{{< output >}}
status:
  conditions:
  - lastTransitionTime: "2020-02-18T21:33:26Z"
    message: all checks passed
    reason: Passed
    status: "True"
    type: Available
{{< /output >}}
-->
Cloud9のターミナルに戻ります。
```
kubectl get apiservice v1beta1.metrics.k8s.io -o yaml
```
全てがうまくいっていれば、下のようなステータスメッセージが見えるはずです
{{< output >}}
status:
  conditions:
  - lastTransitionTime: "2020-02-18T21:33:26Z"
    message: all checks passed
    reason: Passed
    status: "True"
    type: Available
{{< /output >}}

<!--
#### We are now ready to scale a deployed application
-->
#### デプロイされたアプリケーションをスケールする準備ができました