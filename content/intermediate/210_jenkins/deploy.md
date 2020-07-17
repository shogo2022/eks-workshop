---
title: "Jenkinsのデプロイ"
date: 2018-08-07T08:30:11-07:00
weight: 20
draft: false
---

<!--
With our Storage Class configured we then need to create our `jenkins` setup. To
do this we'll just use the `helm` cli with a couple flags.
-->
Storage Classが設定されていれば、 `jenkins` の作成ができます。 `helm` cliをいくつかのフラグをつけて実行します。

<!--
{{% notice info %}}
In a production system you should be using a `values.yaml` file so that you can
manage the drift as you need to update releases
{{% /notice %}}
-->
{{% notice info %}}
商用環境では、リリースのアップデートにしたがって管理するために `values.yaml` を使ってください
{{% /notice %}}

<!--
#### Install Jenkins
-->
#### Jenkinsのインストール
```
helm install cicd stable/jenkins --set rbac.create=true,master.servicePort=80,master.serviceType=LoadBalancer
```

<!--
The output of this command will give you some additional information such as the
`admin` password and the way to get the host name of the ELB that was
provisioned.
-->
このコマンドを実行すると、 `admin` パスワードや作成されたELBのホスト名の取得方法などが出力されます。

<!--
Let's give this some time to provision and while we do let's watch for pods
to boot.
-->
作成には時間がかかるので、待つ間にpodが起動するのをwatchしましょう。

```
kubectl get pods -w
```

<!--
You should see the pods in `init`, `pending` or `running` state.
-->
podが `init`、 `pending` もしくは `running` になっているのが見えるはずです。

<!--
Once this changes to `running` we can get the `load balancer` address.
-->
これが `running` になったら、 `load balancer` アドレスを取得します。

```
export SERVICE_IP=$(kubectl get svc --namespace default cicd-jenkins --template "{{ range (index .status.loadBalancer.ingress 0) }}{{ . }}{{ end }}")
echo http://$SERVICE_IP/login
```

<!--
{{% notice info %}}
This service was configured with a [LoadBalancer](https://kubernetes.io/docs/tasks/access-application-cluster/create-external-load-balancer/) so,
an [AWS Elastic Load Balancer](https://aws.amazon.com/elasticloadbalancing/) (ELB) is launched by Kubernetes for the service.
The EXTERNAL-IP column contains a value that ends with "elb.amazonaws.com" - the full value is the DNS address.
{{% /notice %}}
-->
{{% notice info %}}
このサービスは[LoadBalancer](https://kubernetes.io/docs/tasks/access-application-cluster/create-external-load-balancer/)で設定されているので、Kubernetesによって[AWS Elastic Load Balancer](https://aws.amazon.com/elasticloadbalancing/) (ELB) が起動されます。
 "elb.amazonaws.com" で終わる値があるEXTERNAL-IP列がDNS名を表しています。
{{% /notice %}}

<!--
{{% notice tip %}}
When the front-end service is first deployed, it can take up to several minutes for the ELB to be created and DNS updated. During this time the link above may display a "site unreachable" message. To check if the instances are in service, follow this [deep link](https://console.aws.amazon.com/ec2/v2/home?#LoadBalancers:tag:kubernetes.io/service-name=default/cicd-jenkins;sort=loadBalancerName) to the load balancer console. On the load balancer select the instances tab and ensure that the instance status is listed as "InService" before proceeding to the jenkins login page. 
{{% /notice %}}
-->
{{% notice tip %}}
最初にフロントエンドがデプロイされたときは、ELBがデプロイされDNSが更新されるので数分かかります。この期間は、リンクが到達不可(site unreachable)が表示されるかもしれません。インスタンスが稼働しているかは、この[リンク](https://console.aws.amazon.com/ec2/v2/home?#LoadBalancers:tag:kubernetes.io/service-name=default/cicd-jenkins;sort=loadBalancerName)のロードバランサーコンソールで確認してください。ロードバランサーのインスタンスタブを選択し、インスタンスステータスが"InService"になっていることを確認したら、jenkinsのログインページに進みます。
{{% /notice %}}
