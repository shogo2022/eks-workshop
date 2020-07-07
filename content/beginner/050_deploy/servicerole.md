---
title: "ELBサービスロールがあることを確認する"
date: 2018-09-18T17:40:09-05:00
weight: 29
---

<!--
In AWS accounts that have never created a load balancer before, it's possible
that the service role for ELB might not exist yet.
-->
使っているAWSアカウントでロードバランサーを作ったことがない場合、ELB用のサービスロールがないかもしれません。

<!--
We can check for the role, and create it if it's missing.
-->
ロールを確認して、なければ作成しましょう。

<!--
Copy/Paste the following commands into your Cloud9 workspace:
-->
Cloud9のワークスペースに次のコマンドをコピー、ペーストします:

```
aws iam get-role --role-name "AWSServiceRoleForElasticLoadBalancing" || aws iam create-service-linked-role --aws-service-name "elasticloadbalancing.amazonaws.com"
```
