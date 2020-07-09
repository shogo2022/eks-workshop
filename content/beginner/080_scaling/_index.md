---
title: "アプリケーションとクラスターのオートスケーリング"
chapter: true
weight: 80
tags:
  - beginner
  - CON205
---

<!--
# Implement AutoScaling with HPA and CA
-->
# HPAとCAでオートスケーリングを導入する

<!--
In this Chapter, we will show patterns for scaling your worker nodes and applications deployments automatically. Automatic scaling in K8s comes in two forms:
-->
このチャプターでは、自動的にワーカーノードやアプリケーションdeploymentをスケールする方法をお見せします。K8sでのオートスケーリングには2種類あります:

<!--
* **Horizontal Pod Autoscaler (HPA)** scales the pods in a deployment or replica set. It is implemented as a K8s API resource and a controller. The controller manager queries the resource utilization against the metrics specified in each HorizontalPodAutoscaler definition. It obtains the metrics from either the resource metrics API (for per-pod resource metrics), or the custom metrics API (for all other metrics).
-->
* **Horizontal Pod Autoscaler (HPA)**はdeploymentもしくはreplica setのpodをスケールします。これはK8sのAPIリソース及びコントローラとして設置されます。コントローラマネージャはそれぞれのHorizontalPodAutoscalerの定義にあるメトリックに対応したリソース使用率を要求し、リソースメトリクスAPI(pod毎のリソースメトリクス)もしくはカスタムメトリクスAPI(それ以外)のいずれかから取得されます。

<!--
* **Cluster Autoscaler (CA)** a component that automatically adjusts the size of a Kubernetes Cluster so that all pods have a place to run and there are no unneeded nodes.
-->
* **Cluster Autoscaler (CA)**は全てのpodが実行でき、かつ不必要なものが出ないように、自動でKubernetesクラスターのサイズを調整します。