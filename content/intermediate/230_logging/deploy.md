---
title: "Fluentdのデプロイ"
date: 2018-08-07T08:30:11-07:00
weight: 30
---

```
mkdir ~/environment/fluentd
cd ~/environment/fluentd
wget https://eksworkshop.com/intermediate/230_logging/deploy.files/fluentd.yml
```
<!--
Explore the fluentd.yml to see what is being deployed. There is a link at the bottom of this page. The Fluentd log agent configuration is located in the Kubernetes ConfigMap. Fluentd will be deployed as a DaemonSet, i.e. one pod per worker node. In our case, a 3 node cluster is used and so 3 pods will be shown in the output when we deploy.
-->
fluentd.ymlを確認して、何がデプロイされるか見てください。リンクはこのページの最後にあります。

<!--
{{% notice warning %}}
Update REGION and CLUSTER_NAME environment variables in fluentd.yml to the ones for your values. Currently, they are set to us-west-2 and eksworkshop-eksctl by default. Adjust this change in the 'env' section of the fluentd.yml file:
-->
{{% notice warning %}}
fluentd.ymlの中のREGIONとCLUSTER_NAMEの環境変数を自身の値に変えてください。 デフォルトではus-west-2およびeksworkshop-eksctlに設定されています。該当箇所はfluentd.ymlの'env'セクションです:

        env:
          - name: REGION
            value: us-west-2
          - name: CLUSTER_NAME
            value: eksworkshop-eksctl
            
{{% /notice %}}

```
sed -e "s/us-west-2/$AWS_REGION/" -i ~/environment/fluentd/fluentd.yml
kubectl apply -f ~/environment/fluentd/fluentd.yml
```

<!--
Watch for all of the pods to change to running status
-->
すべてのpodがrunningになるのを待ちます

```
kubectl get pods -w --namespace=kube-system
```

<!--
We are now ready to check that logs are arriving in [CloudWatch Logs](https://console.aws.amazon.com/cloudwatch/home?#logStream:group=/eks/eksworkshop-eksctl/containers)
-->
[CloudWatchログ](https://console.aws.amazon.com/cloudwatch/home?#logStream:group=/eks/eksworkshop-eksctl/containers)にログが集約されているか確認する準備ができました。

<!--
Select the region that is mentioned in fluentd.yml to browse the Cloudwatch Log Group if required.
-->
必要であれば、fluentd.ymlで指定されたリージョンを指定して、Cloudwatchロググループを確認してください。

{{%attachments title="Related files" pattern=".yml"/%}}
