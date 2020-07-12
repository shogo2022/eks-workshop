---
title: "Ingress Controller"
date: 2019-04-09T00:00:00-03:00
weight: 14
draft: false
---

### Ingress Controllers

<!--
In order for the Ingress resource to work, the cluster must have an ingress controller running.
-->
Ingressリソースが動くためには、クラスタ内でingress controllerが動いている必要があります。

<!--
Unlike other types of controllers which run as part of the `kube-controller-manager` binary, Ingress controllers are not started automatically with a cluster.
-->
`kube-controller-manager` の一部として動く他のコントローラと違い、Ingress controllerはクラスタと同時に起動しません。

### AWS ALB Ingress Controller

<!--
[AWS Elastic Load Balancing Application Load Balancer (ALB)](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html) is a popular AWS service that load balances incoming traffic at the application layer (layer 7) across multiple targets, such as Amazon EC2 instances, in multiple Availability Zones. ALB supports multiple features including host or path based routing, TLS (Transport Layer Security) termination, WebSockets, HTTP/2, AWS WAF (Web Application Firewall) integration, integrated access logs, and health checks.
-->
[AWS Elastic Load Balancing Application Load Balancer (ALB)](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html)はAWSの人気あるサービスで、入力トラフィックをアプリケーション層(レイヤー7)で複数の宛先、Amazon　EC2インスタンスなどに複数のアベイラビリティゾーンに跨ってロードバランスします。ALBは複数の機能をサポートしており、hostやパスに基づいたルーティング、TLS(Transport Layer Security)終端、Webソケット、HTTP/2、AWS WAF (Web Application Firewall)統合、統合されたアクセスログ、ヘルスチェックなどがあります。

<!--
The open source [AWS ALB Ingress controller](https://github.com/kubernetes-sigs/aws-alb-ingress-controller) triggers the creation of an ALB and the necessary supporting AWS resources whenever a Kubernetes user declares an Ingress resource in the cluster. The Ingress resource uses the ALB to route HTTP(S) traffic to different endpoints within the cluster. The AWS ALB Ingress controller works on any Kubernetes cluster including Amazon Elastic Kubernetes Service (Amazon EKS).
-->
オープンソースの[AWS ALB Ingress controller](https://github.com/kubernetes-sigs/aws-alb-ingress-controller)は、KubernetesユーザがIngressリソースをクラスタ内で宣言した時にALBと必要なAWSリソースの作成をします。IngressリソースはHTTP(S)通信をクラスタ内の異なるエンドポイントに向けるためにALBを使います。AWS ALB Ingress controllerはAmazon Elastic Kubernetes Service (Amazon EKS)を含むどのKubernetesクラスタでも動きます。

<!--
### Deploy AWS ALB Ingress controller
-->
### AWS ALB Ingress controllerのデプロイ

<!--
First, create an IAM OIDC provider and associate it with your cluster:
-->
まずは、IAM OIDCプロバイダを作成し、クラスタに紐づけます:

```bash
eksctl utils associate-iam-oidc-provider --cluster=eksworkshop-eksctl --approve
```

<!--
{{% notice info %}}
Learn more about [IAM Roles for Service Accounts](https://docs.aws.amazon.com/eks/latest/userguide/iam-roles-for-service-accounts.html) in the Amazon EKS documentation.
{{% /notice %}}
-->
{{% notice info %}}
[Service Accounts用のIAM Roles](https://docs.aws.amazon.com/eks/latest/userguide/iam-roles-for-service-accounts.html) はAmazon EKSドキュメントを参照してください。
{{% /notice %}}

<!--
Next, deploy the relevant RBAC roles and role bindings as required by the AWS ALB Ingress controller:
-->
次に、AWS ALB controllerに必要なRBAC roleとrole bindingを設定します:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/${ALB_INGRESS_VERSION}/docs/examples/rbac-role.yaml
```

<!--
Next, create an IAM policy named `ALBIngressControllerIAMPolicy` to allow the ALB Ingress controller to make AWS API calls on your behalf and save the `Policy.Arn` into a new variable called PolicyARN:
-->
次に、ALB Ingress controllerがAWS APIコールができるように `ALBIngressControllerIAMPolicy` というIAMポリシーを作成し、 `Policy.Arn` をPolicyARNという変数に設定します。

<!--
```bash
#create the policy
aws iam create-policy \
  --policy-name ALBIngressControllerIAMPolicy \
  --policy-document https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/${ALB_INGRESS_VERSION}/docs/examples/iam-policy.json

#get the policy ARN
export PolicyARN=$(aws iam list-policies --query 'Policies[?PolicyName==`ALBIngressControllerIAMPolicy`].Arn' --output text)
```
-->
```bash
#ポリシーの作成
aws iam create-policy \
  --policy-name ALBIngressControllerIAMPolicy \
  --policy-document https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/${ALB_INGRESS_VERSION}/docs/examples/iam-policy.json

#ポリシーARNの取得
export PolicyARN=$(aws iam list-policies --query 'Policies[?PolicyName==`ALBIngressControllerIAMPolicy`].Arn' --output text)
```

<!--
Next, create a Kubernetes service account and an IAM role (for the pod running the AWS ALB Ingress controller):
-->
次に、KubernetesのサービスアカウントとIAMロール(AWS ALB Ingress controllerを実行するpod用)を作成します:

```bash
eksctl create iamserviceaccount \
        --cluster=eksworkshop-eksctl \
        --namespace=kube-system \
        --name=alb-ingress-controller \
        --attach-policy-arn=$PolicyARN \
        --override-existing-serviceaccounts \
        --approve
```

<!--
Then, deploy AWS ALB Ingress controller
-->
そして、AWS ALB Ingress controllerをデプロイします

<!--
```bash
# We dynamically replace the cluster-name by the name of our cluster before applying the YAML file
curl -sS "https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/${ALB_INGRESS_VERSION}/docs/examples/alb-ingress-controller.yaml" \
    | sed 's/# - --cluster-name=devCluster/- --cluster-name=eksworkshop-eksctl/g' \
    | kubectl apply -f -
```
-->
```bash
# YAMLファイルを適用する前に、cluster-nameを私たちのクラスタの名前に変更しています
curl -sS "https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/${ALB_INGRESS_VERSION}/docs/examples/alb-ingress-controller.yaml" \
    | sed 's/# - --cluster-name=devCluster/- --cluster-name=eksworkshop-eksctl/g' \
    | kubectl apply -f -
```

<!--
Verify that the deployment was successful and the controller started:
-->
deploymentが成功したことと、conotrollerが起動したことを確認します:

```bash
kubectl logs -n kube-system $(kubectl get po -n kube-system | egrep -o alb-ingress[a-zA-Z0-9-]+)
```

<!--
Finally, verify that the deployment was successful and the controller started:
-->
最後に、deploymentが成功したことと、conotrollerが起動したことを確認します:


{{< output >}}
-------------------------------------------------------------------------------
AWS ALB Ingress controller
  Release:    v1.1.8
  Build:      git-ec387ad1
  Repository: https://github.com/kubernetes-sigs/aws-alb-ingress-controller.git
-------------------------------------------------------------------------------
{{< /output >}}

<!--
#### Deploy Sample Application
-->
#### サンプルアプリケーションのデプロイ

<!--
Now let’s deploy a sample [2048 game](https://gabrielecirulli.github.io/2048/) into our Kubernetes cluster and use the Ingress resource to expose it to traffic:
-->
では、サンプルの[2048 game](https://gabrielecirulli.github.io/2048/)を私たちのKubernetesクラスタにデプロイし、Ingressリソースを使って外部に公開しましょう:

<!--
Deploy 2048 game resources:
-->
2048 gameリソースのデプロイ:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/${ALB_INGRESS_VERSION}/docs/examples/2048/2048-namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/${ALB_INGRESS_VERSION}/docs/examples/2048/2048-deployment.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/${ALB_INGRESS_VERSION}/docs/examples/2048/2048-service.yaml
```

<!--
Deploy an Ingress resource for the 2048 game:
-->
2048 game用のIngressリソースのデプロイ:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/${ALB_INGRESS_VERSION}/docs/examples/2048/2048-ingress.yaml
```

<!--
After few seconds, verify that the Ingress resource is enabled:
-->
数秒経ったら、Ingressリソースが有効になったか確認します:

```bash
kubectl get ingress/2048-ingress -n 2048-game
```

<!--
You should be able to see the following output:
{{< output >}}
NAME           HOSTS   ADDRESS                PORTS   AGE
2048-ingress   *       DNS-Name-Of-Your-ALB   80      3m
{{< /output >}}
-->
次の表示が見えるはずです:
{{< output >}}
NAME           HOSTS   ADDRESS                PORTS   AGE
2048-ingress   *       DNS-Name-Of-Your-ALB   80      3m
{{< /output >}}

<!--
{{% notice warning %}}
It could take 2 or 3 minutes for the ALB to be ready.
{{% /notice %}}
-->
{{% notice warning %}}
ALBが準備できるまでに2、3分かかるかもしれません。
{{% /notice %}}

<!--
Open a browser and copy-paste your `DNS-Name-Of-Your-ALB` and you should be able to access your newly deployed 2048 game – have fun!
-->
ブラウザを開き、 `DNS-Name-Of-Your-ALB` をコピー、ペーストすれば、デプロイしたばかりの2048 gameにアクセスできるはずです。楽しんでください!