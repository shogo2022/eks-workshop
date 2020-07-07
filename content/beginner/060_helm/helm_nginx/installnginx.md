---
title: "bitnami/nginxのインストール"
date: 2018-08-07T08:30:11-07:00
weight: 400
---

<!--
Installing the Bitnami standalone nginx web server Chart involves us using the
[helm install](https://helm.sh/docs/helm/helm_install/) command.
-->
Bitnamiのスタンドアローンnginx weサーバChartをインストールするには、
[helm install](https://helm.sh/docs/helm/helm_install/)コマンドを使います。

<!--
A Helm Chart can be installed multiple times inside a Kubernetes cluster. This
is because each installation of a Chart can be customized to suit a different
purpose.
-->
Helm ChartはKubernetesクラスターに何回でもインストールすることができます。
これは異なる用途に合わせて、Chartのインストールをカスタマイズできるからです。

<!--
For this reason, you must supply a unique name for the installation, or ask Helm
to generate a name for you.
-->
この理由から、インストール時にはユニークな名前をつける必要があります、もしくはHelmに自動で名付けてもらうこともできます。

<!--
#### Challenge:
**How can you use Helm to deploy the bitnami/nginx chart?**
-->
#### チャレンジ:
**Helmをつかってどのようにbitnami/nginx chartをデプロイするのでしょうか?**

<!--
**HINT:** Use the `helm` utility to `install` the `bitnami/nginx` chart
and specify the name `mywebserver` for the Kubernetes deployment. Consult the
[helm install](https://helm.sh/docs/intro/quickstart/#install-an-example-chart)
documentation or run the `helm install --help` command to figure out the
syntax.
-->
**ヒント:** `helm`ユーティリティの`install`で`bitnami/nginx` chartをインストールし、
`mywebserver`という名前でKubernetes deploymentを名付けてください。
[helm install](https://helm.sh/docs/intro/quickstart/#install-an-example-chart)
のドキュメントを参照するか、`helm install --help`コマンドで書き方を確認してください。

<!--
{{%expand "Expand here to see the solution" %}}
```sh
helm install mywebserver bitnami/nginx
```
{{% /expand %}}
-->
{{%expand "答えはこちら" %}}
```sh
helm install mywebserver bitnami/nginx
```
{{% /expand %}}

<!--
Once you run this command, the output will contain the information about the deployment status, revision, namespace, etc, similar to:
-->
このコマンドを実効すると、次のようなdeploymentの状況と、リビジョン番号、ネームスペースなどの情報が出力されます:

{{< output >}}
NAME: mywebserver
LAST DEPLOYED: Tue Feb 18 22:02:13 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Get the NGINX URL:

  NOTE: It may take a few minutes for the LoadBalancer IP to be available.
        Watch the status with: 'kubectl get svc --namespace default -w mywebserver-nginx'

  export SERVICE_IP=$(kubectl get svc --namespace default mywebserver-nginx --template "{{ range (index .status.loadBalancer.ingress 0) }}{{.}}{{ end }}")
  echo "NGINX URL: http://$SERVICE_IP/"
{{< /output >}}

<!--
In order to review the underlying Kubernetes services, pods and deployments, run:
```sh
kubectl get svc,po,deploy
```
-->
実際のKubernetesサービスやpod、deploymentを確認するには次のコマンドを実行します:
```sh
kubectl get svc,po,deploy
```

<!--
{{% notice info %}}
In the following `kubectl` command examples, it may take a minute or two for
each of these objects' `DESIRED` and `CURRENT` values to match; if they don't
match on the first try, wait a few seconds, and run the command again to check
the status.
{{% /notice %}}
-->
{{% notice info %}}
次の`kubectl`コマンドの例では、これらのオブジェクトの状態の`DESIRED`と`CURRENT`が一致するのに1、2分かかるかもしれません; 最初に実行して一致しなかった場合、数秒待ってから再度コマンドを実行して結果を確認してください・
{{% /notice %}}

<!--
The first object shown in this output is a
[Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/).
A Deployment object manages rollouts (and rollbacks) of different versions of an
application.
-->
この出力の最初のオブジェクトは
[Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)です。
Deploymentオブジェクトは異なるバージョンのアプリケーションのロールアウト(とロールバック)を管理しています。

<!--
You can inspect this Deployment object in more detail by running the following
command:
-->
 Deploymentオブジェクトの詳細を確認するには、次のコマンドを実行します:

```
kubectl describe deployment mywebserver
```

<!--
The next object shown created by the Chart is a
[Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/).  A Pod is a
group of one or more containers.
-->
Chartが作成した次のオブジェクトは
[Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/)です。Podは一つ以上のコンテナの集まりです。

<!--
To verify the Pod object was successfully deployed, we can run the following
command:
-->
Podオブジェクトのデプロイが成功したかは、次のコマンドで確認できます:

<!--
```
kubectl get pods -l app.kubernetes.io/name=nginx
```
And you should see output similar to:
-->
```
kubectl get pods -l app.kubernetes.io/name=nginx
```
次のような出力があるはずです:


{{< output >}}
NAME                                 READY     STATUS    RESTARTS   AGE
mywebserver-nginx-85985c8466-tczst   1/1       Running   0          10s
{{< /output >}}

<!--
The third object that this Chart creates for us is a
[Service](https://kubernetes.io/docs/concepts/services-networking/service/). A
Service enables us to contact this nginx web server from the Internet, via an
Elastic Load Balancer (ELB).
-->
Chartが作成した3つ目のオブジェクトは
[Service](https://kubernetes.io/docs/concepts/services-networking/service/)です。
ServiceはElastic Load Balancer (ELB)経由でインターネットからこのnginxサーバへの接続を可能にしています。

<!--
To get the complete URL of this Service, run:
-->
このサービスの完全なURLを取得するには、次のコマンドを実行します:

```
kubectl get service mywebserver-nginx -o wide
```

<!--
That should output something similar to:
-->
次のような出力があるはずです:

{{< output >}}
NAME                TYPE           CLUSTER-IP      EXTERNAL-IP
mywebserver-nginx   LoadBalancer   10.100.223.99   abc123.amazonaws.com
{{< /output >}}

<!--
Copy the value for `EXTERNAL-IP`, open a new tab in your web browser, and
paste it in.
-->

<!--
{{% notice info %}}
It may take a couple minutes for the ELB and its associated DNS name to become
available; if you get an error, wait one minute, and hit reload.
{{% /notice %}}
-->
{{% notice info %}}
ELBとそのDNS名が有効になるまでに数分かかるかもしれません;
エラーが出た場合は、1分待ってからリロードしてください。
{{% /notice %}}

<!--
When the Service does come online, you should see a welcome message similar to:
-->
Serviceがオンラインになると、次のようなウェルカムメッセージがあらわれるはずです:

![Helm Logo](/images/helm-nginx/welcome_to_nginx.png)

<!--
Congratulations!  You've now successfully deployed the nginx standalone web
server to your EKS cluster!
-->
おめでとうございます!無事、EKSクラスターにnginxのスタンドアローンサーバーをデプロイしました!