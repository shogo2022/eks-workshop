---
title: "X-Ray DaemonSetのデプロイ"
date: 2018-11-177T08:30:11-07:00
weight: 11
draft: false
---

<!--
Now that we have created a service account for X-Ray, we are going to deploy the X-Ray [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) to the EKS cluster. The X-Ray daemon will be deployed to each worker node in the EKS cluster. For reference, see the [example implementation](https://github.com/aws-samples/eks-workshop/tree/main/content/intermediate/245_x-ray/daemonset.files) used in this module.
-->
X-Rayのサービスアカウントが作成されたので、EKSクラスタにX-Rayの[DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)をデプロイしていきます。X-RayデーモンはEKSクラスタのそれぞれのワーカーノードにデプロイされます。このモジュールで使う[構築例](https://github.com/aws-samples/eks-workshop/tree/main/content/intermediate/245_x-ray/daemonset.files)を参照してください。

<!--
The [AWS X-Ray SDKs](https://docs.aws.amazon.com/xray/index.html#lang/en_us) are used to instrument your microservices. When using the DaemonSet in the [example implementation](https://github.com/aws-samples/eks-workshop/tree/main/content/intermediate/245_x-ray/daemonset.files), you need to configure it to point to **xray-service.default:2000**.
-->
[AWS X-Ray SDKs](https://docs.aws.amazon.com/xray/index.html#lang/en_us)がマイクロサービスの中に入っている必要があります。[導入例](https://github.com/aws-samples/eks-workshop/tree/main/content/intermediate/245_x-ray/daemonset.files)のDaemonSetを使う時には、 **xray-service.default:2000** に向けてください。

<!--
The following showcases how to configure the [X-Ray SDK for Go](https://docs.aws.amazon.com/xray/latest/devguide/xray-sdk-go.html). This is merely an example and not a required step in the workshop.
-->
次は[X-Ray SDK for Go](https://docs.aws.amazon.com/xray/latest/devguide/xray-sdk-go.html)を設定する方法をお見せしています。これは一例で、ワークショップで実施する必要はありません。

```
func init() {
	xray.Configure(xray.Config{
		DaemonAddr:     "xray-service.default:2000",
		LogLevel:       "info",
	})
}
```

<!--
To deploy the X-Ray DaemonSet:
-->
X-Ray Daemonsetをデプロイするには次を実行します:

```
kubectl create -f https://eksworkshop.com/intermediate/245_x-ray/daemonset.files/xray-k8s-daemonset.yaml
```

<!--
To see the status of the X-Ray DaemonSet:
-->
X-Ray Daemoonsetのステータスを確認します:

```
kubectl describe daemonset xray-daemon
```

<!--
The folllowing is an example of the command output:
-->
このような表示が返ってきます:

![GitHub Edit](/images/x-ray/daemon_status.png)

<!--
{{% notice tip %}}
To view the logs for all of the X-Ray daemon pods run the following
{{% /notice %}}
-->
{{% notice tip %}}
すべてのX-Rayデーモンpodのログを見るには次を実行します
{{% /notice %}}

```
kubectl logs -l app=xray-daemon
```


