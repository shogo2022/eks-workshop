---
title: "フロントエンドサービスのデプロイ"
date: 2018-09-18T17:40:09-05:00
weight: 30
---

<!--
#### Challenge:
**Let’s bring up the Ruby Frontend!**
-->
#### チャレンジ:
**Rubyフロントエンドを起動しましょう!**

<!--
{{%expand "Expand here to see the solution" %}}

Copy/Paste the following commands into your Cloud9 workspace:

```
cd ~/environment/ecsdemo-frontend
kubectl apply -f kubernetes/deployment.yaml
kubectl apply -f kubernetes/service.yaml
```

We can watch the progress by looking at the deployment status:
```
kubectl get deployment ecsdemo-frontend
```
{{% /expand %}}
-->
{{%expand "答えはこちら" %}}

Cloud9のワークスペースに次のコマンドをコピー、ペーストします:

```
cd ~/environment/ecsdemo-frontend
kubectl apply -f kubernetes/deployment.yaml
kubectl apply -f kubernetes/service.yaml
```

deploymentの状況を確認することで進捗を見ることができます:
```
kubectl get deployment ecsdemo-frontend
```
{{% /expand %}}
