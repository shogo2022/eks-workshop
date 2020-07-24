---
title: "後片付け"
date: 2018-10-087T08:30:11-07:00
weight: 14
draft: false
---

<!--
Congratulations on completing the Tracing with X-Ray module.
-->
X-Rayでのトレーシングのモジュールが完了しました。

<!--
The content for this module was based on the [Application Tracing on Kubernetes with AWS X-Ray](https://aws.amazon.com/blogs/compute/application-tracing-on-kubernetes-with-aws-x-ray/) blog post.
-->
このモジュールのコンテンツは[Application Tracing on Kubernetes with AWS X-Ray](https://aws.amazon.com/blogs/compute/application-tracing-on-kubernetes-with-aws-x-ray/)のblogポストに基づいています。

<!--
This module is not used in subsequent steps, so you can remove the resources now or at the end of the workshop.
-->
このモジュールは以降の手順で使われることはないので、莉ソースを削除します。

<!--
Delete the Kubernetes example microservices deployed:
-->
デプロイされたサンプルマイクロサービスの削除

```
kubectl delete deployments x-ray-sample-front-k8s x-ray-sample-back-k8s

kubectl delete services x-ray-sample-front-k8s x-ray-sample-back-k8s
```

<!--
Delete the X-Ray DaemonSet:
-->
X-Ray Daemonsetの削除

```
kubectl delete -f https://eksworkshop.com/intermediate/245_x-ray/daemonset.files/xray-k8s-daemonset.yaml
```

<!--
Delete the IAM Service Account:
-->
IAMサービスアカウントの削除
```
eksctl delete iamserviceaccount --name xray-daemon --cluster eksworkshop-eksctl
```
