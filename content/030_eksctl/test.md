---
title: "クラスターのテスト"
date: 2018-08-07T13:36:57-07:00
weight: 30
---
<!--
#### Test the cluster:
Confirm your nodes:
-->
#### クラスターのテスト:
ノードの確認:

<!--
```bash
kubectl get nodes # if we see our 3 nodes, we know we have authenticated correctly
```
-->
```bash
kubectl get nodes # ノードが3つあれば、認証は成功です
```

<!--
#### Export the Worker Role Name for use throughout the workshop:
-->
#### ワークショップを通じて使うワーカーロールネームをエクスポートします:

```bash
STACK_NAME=$(eksctl get nodegroup --cluster eksworkshop-eksctl -o json | jq -r '.[].StackName')
ROLE_NAME=$(aws cloudformation describe-stack-resources --stack-name $STACK_NAME | jq -r '.StackResources[] | select(.ResourceType=="AWS::IAM::Role") | .PhysicalResourceId')
echo "export ROLE_NAME=${ROLE_NAME}" | tee -a ~/.bash_profile
```

<!--
#### Congratulations!
-->
#### おめでとうございます!!

<!--
You now have a fully working Amazon EKS Cluster that is ready to use!
-->
これで完全に動く使用可能なAmazon EKS クラスターができました!