---
title: "HPAでアプリケーションをスケールする"
date: 2018-08-07T08:30:11-07:00
weight: 20
---

<!--
## Deploy a Sample App
-->
## サンプルアプリをデプロイする

<!--
We will deploy an application and expose as a service on TCP port 80. The application is a custom-built image based on the php-apache image. The index.php page performs calculations to generate CPU load. More information can be found [here](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/#run-expose-php-apache-server)
-->
アプリケーションをデプロイして、TCPポート80番でサービスを公開します。アプリケーションはphp-apacheイメージを基にしたオリジナルのイメージです。index.phpページはCPUに負荷を与えるように演算が走っています。詳しい情報は[こちら](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/#run-expose-php-apache-server)です。

The application is a custom-built image based on the php-apache image. The index.php page performs calculations to generate CPU load. More information can be found [here](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/#run-expose-php-apache-server)

```bash
kubectl create deployment php-apache --image=us.gcr.io/k8s-artifacts-prod/hpa-example
kubectl set resources deploy php-apache --requests=cpu=200m
kubectl expose deploy php-apache --port 80

kubectl get pod -l app=php-apache
```

<!--
## Create an HPA resource
-->
## HPAリソースの作成

<!--
This HPA scales up when CPU exceeds 50% of the allocated container resource.
-->
このHPAはコンテナに割り当てられたCPUリソースが50%を超えるとスケールアップします。

```bash
kubectl autoscale deployment php-apache `#The target average CPU utilization` \
    --cpu-percent=50 \
    --min=1 `#The lower limit for the number of pods that can be set by the autoscaler` \
    --max=10 `#The upper limit for the number of pods that can be set by the autoscaler`
```

<!--
View the HPA using kubectl. You probably will see `<unknown>/50%` for 1-2 minutes and then you should be able to see `0%/50%`
-->
kubectlでHPAをみましょう。1-2分は `<unknown>/50%` という表示かもしれませんが、いずれ `0%/50%` という表示に変わります。

<!--
```
kubectl get hpa
```
## Generate load to trigger scaling
-->
```
kubectl get hpa
```
## 負荷をかけスケーリングを起こす

<!--
**Open a new terminal** in the Cloud9 Environment and run the following command to drop into a shell on a new container
-->
Cloud9で **新しいターミナル** を開いて次のコマンドを実行し、新しいコンテナのshellに入ります

```bash
kubectl --generator=run-pod/v1 run -i --tty load-generator --image=busybox /bin/sh
```

<!--
Execute a while loop to continue getting http:///php-apache
-->
```
kubectl run -i --tty load-generator --image=busybox /bin/sh
```
whileループでhttp:///php-apacheの取得を繰り返します

```bash
while true; do wget -q -O - http://php-apache; done
```

<!--
In the previous tab, watch the HPA with the following command
-->
前のタブで次のコマンドを実行し、HPAを注視します

<!--
```bash
kubectl get hpa -w
```

You will see HPA scale the pods from 1 up to our configured maximum (10) until the CPU average is below our target (50%)
-->
```bash
kubectl get hpa -w
```
ひとつだったpodが、HPAによりCPU平均が目標値(50%)になるまで、設定した最大値(10)にスケールしていくのが見えます。

![Scale Up](/images/scaling-hpa-results.png)

<!--
You can now stop (_Ctrl + C_) load test that was running in the other terminal. You will notice that HPA will slowly bring the replica count to min number based on its configuration. You should also get out of load testing application by pressing _Ctrl + D_
-->
別ターミナルで走っている負荷テストを止めて( _Ctrl + C_ )ください。HPAが設定に従ってレプリカの数を徐々に減らしていくのが見えます。負荷テストのアプリケーションは _Ctrl + D_ で抜けましょう。
