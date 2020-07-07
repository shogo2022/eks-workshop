---
title: "NodeJSバックエンドAPIのデプロイ"
date: 2018-09-18T17:39:30-05:00
weight: 10
---

<!--
Let’s bring up the NodeJS Backend API!
-->
NodeJSバックエンドAPIを起動しましょう!

<!--
Copy/Paste the following commands into your Cloud9 workspace:
-->
Cloud9のワークスペースに次のコマンドをコピー、ペーストします:

```
cd ~/environment/ecsdemo-nodejs
kubectl apply -f kubernetes/deployment.yaml
kubectl apply -f kubernetes/service.yaml
```

<!--
We can watch the progress by looking at the deployment status:
```
kubectl get deployment ecsdemo-nodejs
```
-->
deploymentの状況を確認することで進捗を見ることができます:
```
kubectl get deployment ecsdemo-nodejs
```
