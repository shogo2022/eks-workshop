---
title: "Serviceの公開"
date: 2019-04-09T00:00:00-03:00
weight: 12
draft: false
---
<!--
#### Exposing the Service
-->
#### Serviceの公開

<!--
For some parts of your applications you may want to expose a Service onto an external IP address. Kubernetes supports two ways of doing this: `NodePort` and `LoadBalancer`.
-->
時にはアプリケーションのServiceを外部のIPアドレスで公開したい時があるかもしれません。Kubernetesはこれに対して2通りの方法雨を持っています: `NodePort` と `LoadBalancer` です。

```bash
kubectl -n my-nginx get svc my-nginx
```

Output
{{< output >}}
NAME       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
my-nginx   ClusterIP   10.100.225.196   <none>        80/TCP    33m
{{< /output >}}

<!--
Currently the Service does not have an External IP, so let’s now patch the Service to use a cloud load balancer, by updating the type of the my-nginx Service from `ClusterIP` to `LoadBalancer`:
-->
現在はServiceはExternal IPがありません。my-nginxのServiceを `ClusterIP` から `LoadBalancer` へアップデートして、クラウドロードバランサーを使うようにpatchしましょう。:

```bash
kubectl -n my-nginx patch svc my-nginx -p '{"spec": {"type": "LoadBalancer"}}'
```

<!--
We can check for the changes:
-->
変更を確認できます:

```bash
kubectl -n my-nginx get svc my-nginx
```

Output
{{< output >}}
NAME       TYPE           CLUSTER-IP       EXTERNAL-IP                                                             PORT(S)        AGE
my-nginx   LoadBalancer   10.100.225.196   aca434079a4cb0a9961170c1-23367063.us-west-2.elb.amazonaws.com           80:30470/TCP   39m
{{< /output >}}

<!--
{{% notice info %}}
The Load Balancer can take a couple of minutes in being available on the DNS.
{{% /notice %}}
-->
{{% notice info %}}
ロードバランサーがDNSで利用可能になるのには数分かかります。
{{% /notice %}}

<!--
Now, let's try if it's accessible.
-->
では、アクセスできるか試してみましょう。

```bash
export loadbalancer=$(kubectl -n my-nginx get svc my-nginx -o jsonpath={'.status'.loadBalancer.ingress[*].hostname})

curl -k -s http://${loadbalancer} | grep title
```

Output
{{< output >}}
<title>Welcome to nginx!</title>
{{< /output >}}

<!--
If the Load Balancer name is too long to fit in the standard kubectl get svc output, you’ll need to do kubectl describe service my-nginx to see it. You’ll see something like this:
-->
ロー度バランサーの名前が長すぎてkubectl get svcの表示に収まりきらない場合は、kubectl describe service my-nginxを使ってください。このような表示になります:

```bash
kubectl -n my-nginx describe service my-nginx | grep Ingress
```

Output
{{< output >}}
LoadBalancer Ingress:   a320587ffd19711e5a37606cf4a74574-1142138393.us-east-1.elb.amazonaws.com
{{< /output >}}
