---
title: "CrystalバックエンドAPIのデプロイ"
date: 2018-09-18T17:40:03-05:00
weight: 20
---

<!--
Let’s bring up the Crystal Backend API!
-->
CrystalバックエンドAPIを起動しましょう!

<!--
Copy/Paste the following commands into your Cloud9 workspace:
-->
Cloud9のワークスペースで次のコマンドをコピー、ペーストします:

```
cd ~/environment/ecsdemo-crystal
kubectl apply -f kubernetes/deployment.yaml
kubectl apply -f kubernetes/service.yaml
```

<!--
We can watch the progress by looking at the deployment status:
```
kubectl get deployment ecsdemo-crystal
```
-->
deploymentの状況を確認することで進捗を見ることができます:
```
kubectl get deployment ecsdemo-crystal
```
