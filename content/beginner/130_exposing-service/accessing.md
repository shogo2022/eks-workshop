---
title: "Serviceにアクセス"
date: 2019-04-09T00:00:00-03:00
weight: 11
draft: false
---

<!--
#### Accessing the Service
-->
#### Serviceにアクセス

<!--
Kubernetes supports 2 primary modes of finding a Service: environment variables and DNS.
-->
KubernetesのServiceの見つけ方には2種類のモードがあります: 環境変数とDNSです。

<!--
The former works out of the box while the latter requires the CoreDNS cluster add-on (automatically installed when creating the EKS cluster).
-->
前者は標準機能ですが、後者はCoreDNSクラスタアドオンが必要(EKSクラスタでは構築時に自動的にインストールされています)です。

<!--
#### Environment Variables
-->
#### 環境変数

<!--
When a Pod runs on a Node, the `kubelet` adds a set of environment variables for each active Service. This introduces an ordering problem. To see why, inspect the environment of your running nginx Pods (your Pod name will be different):
Let's view the pods again:
-->
Podがノード上で動く時、`kubelet`はそれぞれのServiceに対応した環境変数をセットします。これは順序が問題になります。理由を知るために、実行しているnginxのpodの環境を調査してみます:
podを再び確認してみましょう:

```bash
kubectl -n my-nginx get pods -o wide
```

Output:
{{< output >}}
NAME                        READY     STATUS    RESTARTS   AGE       IP               NODE                                           NOMINATED NODE
my-nginx-756f645cd7-gsl4g   1/1       Running   0          22m       192.168.59.188   ip-192-168-38-150.us-west-2.compute.internal   <none>
my-nginx-756f645cd7-t8b6w   1/1       Running   0          22m       192.168.79.210   ip-192-168-92-222.us-west-2.compute.internal   <none>
{{< /output >}}

<!--
Now let's inspect the environment of one of your running nginx Pods:
-->
では、動いているnginxのpodの一つで環境変数をみてみましょう:

```bash
export mypod=$(kubectl -n my-nginx get pods  -o jsonpath='{.items[0].metadata.name}')

kubectl -n my-nginx exec ${mypod} -- printenv | grep SERVICE
```

{{< output >}}
KUBERNETES_SERVICE_HOST=10.100.0.1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
{{< /output >}}

<!--
Note there’s no mention of your Service. This is because you created the replicas before the Service.
-->
Serviceの記述がありません。Service以前にレプリカを作成したのが理由です。

<!--
Another disadvantage of doing this is that the scheduler might put both Pods on the same machine, which will take your entire Service down if it dies. We can do this the right way by killing the 2 Pods and waiting for the Deployment to recreate them. This time around the Service exists before the replicas. This will give you scheduler-level Service spreading of your Pods (provided all your nodes have equal capacity), as well as the right environment variables:
-->
もう一つの欠点は、もしスケジューラが両方のpodを同じ機器に配置した場合、機器が止まった場合にServiceが止まってしまうことです。これを正すために、Podを2つとも止めて、Deploymentがpodを再作成するのを待ちます。今回はレプリカの前にServiceが存在しています。これで、スケジューラが均等にPodを配置(全てのノードが均等な性能を持っているとすれば)し、環境変数も正しく設定されます:

```bash
kubectl -n my-nginx rollout restart deployment my-nginx
```

```bash
kubectl -n my-nginx get pods -o wide
```

Output just in the moment of change:
{{< output >}}
NAME                        READY     STATUS        RESTARTS   AGE       IP               NODE                                           NOMINATED NODE
my-nginx-756f645cd7-9tgkw   1/1       Running       0          6s        192.168.14.67    ip-192-168-15-64.us-west-2.compute.internal    <none>
my-nginx-756f645cd7-gsl4g   0/1       Terminating   0          25m       192.168.59.188   ip-192-168-38-150.us-west-2.compute.internal   <none>
my-nginx-756f645cd7-ljjgq   1/1       Running       0          6s        192.168.63.80    ip-192-168-38-150.us-west-2.compute.internal   <none>
my-nginx-756f645cd7-t8b6w   0/1       Terminating   0          25m       192.168.79.210   ip-192-168-92-222.us-west-2.compute.internal   <none>
{{< /output >}}

<!--
{{% notice note %}}
You may notice that the pods have different names, since they are destroyed and recreated.
{{% /notice %}}
-->
{{% notice note %}}
podは一度壊されて再作成されているので、名前が異なっています。
{{% /notice %}}

<!--
Now let’s inspect the environment of one of your running nginx Pods one more time:
-->
ではもう一度、実行中のnginx podの環境変数を調べてみましょう:

```bash
export mypod=$(kubectl -n my-nginx get pods  -o jsonpath='{.items[0].metadata.name}')

kubectl -n my-nginx exec ${mypod} -- printenv | grep SERVICE
```

{{< output >}}
KUBERNETES_SERVICE_HOST=10.100.0.1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
MY_NGINX_SERVICE_HOST=10.100.225.196
MY_NGINX_SERVICE_PORT=80
{{< /output >}}

<!--
We now have an environment variable referencing the nginx Service IP called _MY_NGINX_SERVICE_HOST_.
-->
_MY_NGINX_SERVICE_HOST_　という名前でnginxのService IPが環境変数としてセットされているのが確認できます。

#### DNS

<!--
Kubernetes offers a DNS cluster add-on Service that automatically assigns dns names to other Services. You can check if it’s running on your cluster:
-->
KubernetesはDNSアドオンサービスを提供しており、他のServiceに自動的にdns名を割り当てます。クラスタで有効になっているかを確認します:

<!--
To check if your cluster is already running CoreDNS, use the following command.
-->
クラスタでCoreDNSが動いているか確認するには次のおコマンドを使います。

```bash
kubectl get pod -n kube-system -l k8s-app=kube-dns
```

<!--
{{% notice note %}}
The service for CoreDNS is still called `kube-dns` for backward compatibility.
{{% /notice %}}
-->
{{% notice note %}}
CoreDNSのServiceは後方互換のためにまだ `kube-dns` と呼ばれています。
{{% /notice %}}

<!--
{{< output >}}
NAME       TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)         AGE
kube-dns   ClusterIP   10.0.0.10    <none>        53/UDP,53/TCP   8m
{{< /output >}}
If it isn’t running, you can enable it. The rest of this section will assume you have a Service with a long lived IP (my-nginx), and a DNS server that has assigned a name to that IP (the CoreDNS cluster addon), so you can talk to the Service from any pod in your cluster using standard methods (e.g. gethostbyname). Let’s run another curl application to test this:
-->
{{< output >}}
NAME       TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)         AGE
kube-dns   ClusterIP   10.0.0.10    <none>        53/UDP,53/TCP   8m
{{< /output >}}
実行されていない場合は、有効にできます。このセクションはmy-nginxのServiceにIPが割り当てられていることと、そのIPにDNS server(CoreDNSクラスタアドオン)が名前を割り当てていること、よってクラスタ内の全てのpodは通常の方法(例 gethostbyname)でServiceと通信できることを想定しています。curlを使ってこれをテストしてみましょう:

```bash
kubectl -n my-nginx run curl --image=radial/busyboxplus:curl -i --tty
```

<!--
Then, hit enter and run.
-->
そして、実行します。

```bash
nslookup my-nginx
```

Output:
{{< output >}}
Server:    10.100.0.10
Address 1: 10.100.0.10 kube-dns.kube-system.svc.cluster.local

Name:      my-nginx
Address 1: 10.100.225.196 my-nginx.my-nginx.svc.cluster.local
{{< /output >}}

<!--
Type exit to log out of the container.
-->
exitと打って、コンテナからログアウトします。

```bash
 exit
```
