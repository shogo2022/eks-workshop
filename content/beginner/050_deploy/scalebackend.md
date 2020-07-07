---
title: "バックエンドサービスをスケールする"
date: 2018-09-18T17:40:09-05:00
weight: 50
---

<!--
When we launched our services, we only launched one container of each. We can
confirm this by viewing the running pods:
```
kubectl get deployments
```
-->
サービスを起動したときはそれぞれ一つずつのコンテナでした。これは実行中の
podを見れば確認できます:
```
kubectl get deployments
```

<!--
Now let's scale up the backend services:
```
kubectl scale deployment ecsdemo-nodejs --replicas=3
kubectl scale deployment ecsdemo-crystal --replicas=3
```
Confirm by looking at deployments again:
```
kubectl get deployments
```
-->
ではバックエンドサービスをスケールアップしましょう:
```
kubectl scale deployment ecsdemo-nodejs --replicas=3
kubectl scale deployment ecsdemo-crystal --replicas=3
```
deploymentsを見て確認しましょう:
```
kubectl get deployments
```

<!--
Also, check the browser tab where we can see our application running. You should
now see traffic flowing to multiple backend services.
-->
アプリケーションが動いているところをブラウザで確認してください。
トラフィックが複数のバックエンドサービスに流れていることが見えるはずです。