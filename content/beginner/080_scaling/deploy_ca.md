---
title: "Cluster Autoscaler (CA)の設定"
date: 2018-08-07T08:30:11-07:00
weight: 30
---
<!--
Cluster Autoscaler for AWS provides integration with Auto Scaling groups. It enables users to choose from four different options of deployment:
-->
AWSのCluster Autoscalerはオートスケーリンググループと統合されています。導入には4つの方法があります:

<!--
* One Auto Scaling group
* Multiple Auto Scaling groups
* Auto-Discovery
* Control-plane Node setup
-->
* オートスケーリンググループがひとつ
* オートスケーリンググループが複数
* オートディスカバリ
* コントロールプレーンノード設定

<!--
### Configure the Cluster Autoscaler (CA)
We have provided a manifest file to deploy the CA. Copy the commands below into your Cloud9 Terminal.
-->
### Cluster Autoscaler (CA)の設定
CAをデプロイするためのマニフェストファイルを用意してあります。次のコマンドをCloud9のターミナルにコピーしてください。

<!--
Auto-Discovery is the preferred method to configure Cluster Autoscaler. Click [here](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler/cloudprovider/aws) for more information.
-->
クラスタのAutoscalerでは、オートディスカバリが推奨されています。詳細は[こちら](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler/cloudprovider/aws)を参照してください。

<!--
Cluster Autoscaler will attempt to determine the CPU, memory, and GPU resources provided by an Auto Scaling Group based on the instance type specified in its Launch Configuration or Launch Template.
-->
クラスタAutoscalerは起動設定や起動テンプレートに指定されているインスタンスタイプに基づいて、オートスケーリンググループで提供されるCPUやメモリ、GPUのリソースを決定します。

<!--
Populate the manifest file with the most-up-to-date Cluster Autoscaler image version for the actual Kubernetes version in EKS.
-->
マニフェストファイルを実際のEKSのKubernetesバージョンで最新のCluster Autoscalerイメージバージョンに書き換えます。

<!--
## Configure the ASG
-->
## ASGの設定

<!--
You configure the size of your Auto Scaling group by setting the minimum, maximum, and desired capacity. When we created the cluster we set these settings to 3.
-->


```bash
aws autoscaling \
    describe-auto-scaling-groups \
    --query "AutoScalingGroups[? Tags[? (Key=='eks:cluster-name') && Value=='eksworkshop-eksctl']].[AutoScalingGroupName, MinSize, MaxSize,DesiredCapacity]" \
    --output table
```

{{< output >}}
-------------------------------------------------------------
|                 DescribeAutoScalingGroups                 |
+-------------------------------------------+----+----+-----+
|  eks-1eb9b447-f3c1-0456-af77-af0bbd65bc9f |  3 |  3 |  3  |
+-------------------------------------------+----+----+-----+
{{< /output >}}

Now, increase the maximum capacity to 4 instances

```bash
# we need the ASG name
export ASG_NAME=$(aws autoscaling describe-auto-scaling-groups --query "AutoScalingGroups[? Tags[? (Key=='eks:cluster-name') && Value=='eksworkshop-eksctl']].AutoScalingGroupName" --output text)

# increase max capacity up to 4
aws autoscaling \
    update-auto-scaling-group \
    --auto-scaling-group-name ${ASG_NAME} \
    --min-size 3 \
    --desired-capacity 3 \
    --max-size 4

# Check new values
aws autoscaling \
    describe-auto-scaling-groups \
    --query "AutoScalingGroups[? Tags[? (Key=='eks:cluster-name') && Value=='eksworkshop-eksctl']].[AutoScalingGroupName, MinSize, MaxSize,DesiredCapacity]" \
    --output table
```

## IAM roles for service accounts

<!--
{{% notice note %}}
[Click here](/beginner/110_irsa/) if you are not familiar wit IAM Roles for Service Accounts (IRSA).
{{% /notice %}}
-->
{{% notice note %}}
IAM Roles for Service Accounts (IRSA)についての説明が必要な時は[ここをクリック](/beginner/110_irsa/)してください。
{{% /notice %}}

<!--
With IAM roles for service accounts on Amazon EKS clusters, you can associate an IAM role with a [Kubernetes service account](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/). This service account can then provide AWS permissions to the containers in any pod that uses that service account. With this feature, you no longer need to provide extended permissions to the node IAM role so that pods on that node can call AWS APIs.
-->
Amazon EKSクラスタでIAM roles for service accountsを使うことで、KubernetesのサービスアカウントにIAMロールを紐づけることができます。これでpod内のコンテナにAWS権限を与えることができるようになります。これにより、ノード内のpodがAWS APIを呼び出すためにノードのIAMロールに権限をつける必要がなくなります。

<!--
Enabling IAM roles for service accounts on your cluster
-->
クラスタのサービスアカウントのIAMロール有効化

```bash
eksctl utils associate-iam-oidc-provider \
    --cluster eksworkshop-eksctl \
    --approve
```

<!--
Creating an IAM policy for your service account that will allow your CA pod to interact with the autoscaling groups.
-->
CA podがオートスケーリンググループとやりとりができるようにサービスアカウント用のIAMポリシーを作成します。

```bash
mkdir ~/environment/cluster-autoscaler

cat <<EoF > ~/environment/cluster-autoscaler/k8s-asg-policy.json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "autoscaling:DescribeAutoScalingGroups",
                "autoscaling:DescribeAutoScalingInstances",
                "autoscaling:DescribeLaunchConfigurations",
                "autoscaling:DescribeTags",
                "autoscaling:SetDesiredCapacity",
                "autoscaling:TerminateInstanceInAutoScalingGroup",
                "ec2:DescribeLaunchTemplateVersions"
            ],
            "Resource": "*",
            "Effect": "Allow"
        }
    ]
}
EoF

aws iam create-policy   \
  --policy-name k8s-asg-policy \
  --policy-document file://~/environment/cluster-autoscaler/k8s-asg-policy.json
```

<!--
Finally, create an IAM role for the cluster-autoscaler Service Account in the kube-system namespace.
-->
最後に、cluster-autoscalerサービスアカウントのIAMロールをkube-system名前空間で作成します。

```bash
eksctl create iamserviceaccount \
    --name cluster-autoscaler \
    --namespace kube-system \
    --cluster eksworkshop-eksctl \
    --attach-policy-arn "arn:aws:iam::${ACCOUNT_ID}:policy/k8s-asg-policy" \
    --approve \
    --override-existing-serviceaccounts
```

<!--
Make sure your service account with the ARN of the IAM role is annotated
-->
サービスアカウントがIAMロールのARNでannotateされていることを確認してください

```bash
kubectl -n kube-system describe sa cluster-autoscaler
```

Output

{{< output >}}
Name:                cluster-autoscaler
Namespace:           kube-system
Labels:              <none>
Annotations:         eks.amazonaws.com/role-arn: arn:aws:iam::197520326489:role/eksctl-eksworkshop-eksctl-addon-iamserviceac-Role1-12LNPCGBD6IPZ
Image pull secrets:  <none>
Mountable secrets:   cluster-autoscaler-token-vfk8n
Tokens:              cluster-autoscaler-token-vfk8n
Events:              <none>
{{< /output >}}

<!--
## Deploy the Cluster Autoscaler (CA)
-->
## Cluster Autoscaler (CA)のデプロイ

<!--
Deploy the Cluster Autoscaler to your cluster with the following command.
-->
次のコマンドでCluster Autoscalerをクラスタにデプロイします。

```bash
kubectl apply -f https://www.eksworkshop.com/beginner/080_scaling/deploy_ca.files/cluster-autoscaler-autodiscover.yaml
```

<!--
To prevent CA from removing nodes where its own pod is running, we will add the `cluster-autoscaler.kubernetes.io/safe-to-evict` annotation to its deployment with the following command
-->
CAが自身のpodがあるノードを削除するのを防ぐため、 `cluster-autoscaler.kubernetes.io/safe-to-evict` のannotationをdeploymentに含めます

```bash
kubectl -n kube-system \
    annotate deployment.apps/cluster-autoscaler \
    cluster-autoscaler.kubernetes.io/safe-to-evict="false"
```

<!--
Finally let's update the autoscaler image
-->
最後に、autoscalerのイメージをアップデートします

```bash
# we need to retrieve the latest docker image available for our EKS version
export K8S_VERSION=$(kubectl version --short | grep 'Server Version:' | sed 's/[^0-9.]*\([0-9.]*\).*/\1/' | cut -d. -f1,2)
export AUTOSCALER_VERSION=$(curl -s "https://api.github.com/repos/kubernetes/autoscaler/releases" | grep '"tag_name":' | sed -s 's/.*-\([0-9][0-9\.]*\).*/\1/' | grep -m1 ${K8S_VERSION})

kubectl -n kube-system \
    set image deployment.apps/cluster-autoscaler \
    cluster-autoscaler=us.gcr.io/k8s-artifacts-prod/autoscaling/cluster-autoscaler:v${AUTOSCALER_VERSION}
```

<!--
Watch the logs

```bash
kubectl -n kube-system logs -f deployment/cluster-autoscaler
```
-->
ログの確認
```
kubectl logs -f deployment/cluster-autoscaler -n kube-system
```

<!--
**We are now ready to scale our cluster**
-->
**これでクラスタをスケールする準備が整いました**

{{%attachments title="Related files" pattern=".yaml"/%}}
