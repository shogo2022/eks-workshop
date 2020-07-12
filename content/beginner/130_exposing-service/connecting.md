---
title: "アプリケーションとサービスの接続"
date: 2019-04-09T00:00:00-03:00
weight: 10
draft: false
---

<!--
Before discussing the Kubernetes approach to networking, it is worthwhile to contrast it with the “normal” way networking works with Docker.
-->
Kubernetesのネットワークについて話す前に、Dockerでの"通常の"ネットワークがどのように動くかをみてみましょう。

<!--
By default, Docker uses host-private networking, so containers can talk to other containers only if they are on the same machine. In order for Docker containers to communicate across nodes, there must be allocated ports on the machine’s own IP address, which are then forwarded or proxied to the containers. This obviously means that containers must either coordinate which ports they use very carefully or ports must be allocated dynamically.
-->
デフォルトでDockerはhost-privateなネットワークを使うので、コンテナは同じ機器の中のコンテナとのみ通信ができます。Dockerコンテナがノードを跨いで通信をするには、機器のIPアドレス上にポートを割り振り、転送もしくはプロキシされる必要があります。これのせいで、コンテナはポート番号に気をつける必要があり、ポート番号を動的に割り振らざるをえませんでした。

<!--
Coordinating ports across multiple developers is very difficult to do at scale and exposes users to cluster-level issues outside of their control. Kubernetes assumes that pods can communicate with other pods, regardless of which host they land on. We give every pod its own cluster-private-IP address so you do not need to explicitly create links between pods or map container ports to host ports. This means that containers within a Pod can all reach each other’s ports on localhost, and all pods in a cluster can see each other without NAT.
-->
複数の開発者の間でポート番号を割り振るのは困難を極め、クラスターレベルでは制御することができませんでした。Kubernetesではpodはホストにかかわらず他のpodに通信ができるように想定されています。全てのpodにクラスタープライベートIPアドレスがふられているので、podに明示的なリンクを作成する必要もなく、またコンテナポートとホストポートを紐づける必要もありません。これは、Pod内のコンテナはローカルのポートでそれぞれ通信でき、クラスタ内のpodはそれぞれをNATなしで認識できるということです。

<!--
#### Exposing pods to the cluster
-->
#### podをクラスタに公開する

<!--
If you created a default deny policy in the previous section, delete it by running:
-->
前のセクションでdefault denyポリシーを作っていた場合は、次のように削除します:

```bash
if [ -f ~/environment/calico_resources/default-deny.yaml ]; then
  kubectl delete -f ~/environment/calico_resources/default-deny.yaml
fi
```

<!--
Create a nginx deployment, and note that it has a container port specification:
-->
nginxのdeploymentを作成し、コンテナポート(container port)があることを覚えておきます:

```bash
cat <<EoF > ~/environment/run-my-nginx.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
  namespace: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80
EoF
```

<!--
This makes it accessible from any node in your cluster. Check the nodes the Pod is running on:
-->
これでクラスタ内のどのノードからもアクセスできるようになりました。Podが走っているノードをみてみましょう:

<!--
```bash
# create the namespace
kubectl create ns my-nginx
# create the nginx deployment with 2 replicas
kubectl -n my-nginx apply -f ~/environment/run-my-nginx.yaml

kubectl -n my-nginx get pods -o wide
```
-->
```bash
# 名前空間の作成
kubectl create ns my-nginx
# レプリカ2個でnginx deploymentを作成する
kubectl -n my-nginx apply -f ~/environment/run-my-nginx.yaml

kubectl -n my-nginx get pods -o wide
```

<!--
The output being something like this:
-->
このように表示されます:
{{< output >}}
NAME                        READY     STATUS    RESTARTS   AGE       IP               NODE                                           NOMINATED NODE
my-nginx-756f645cd7-gsl4g   1/1       Running   0          63s       192.168.59.188   ip-192-168-38-150.us-west-2.compute.internal   <none>
my-nginx-756f645cd7-t8b6w   1/1       Running   0          63s       192.168.79.210   ip-192-168-92-222.us-west-2.compute.internal   <none>
{{< /output >}}
<!--
Check your pods’ IPs:
-->
podのIPを確認します:
```bash
kubectl -n my-nginx get pods -o yaml | grep 'podIP:'
```

<!--
Output being like:
-->
出力はこちら:
{{< output >}}
    podIP: 192.168.59.188
    podIP: 192.168.79.210
{{< /output >}}

<!--
#### Creating a Service
-->
#### サービスの作成

<!--
So we have pods running nginx in a flat, cluster wide, address space. In theory, you could talk to these pods directly, but what happens when a node dies? The pods die with it, and the Deployment will create new ones, with different IPs. This is the problem a Service solves.
-->
nginxのpodは今、クラスタ内のフラットなアドレス空間にいます。論理的にはこのpodに直接通信ができますが、ノードが止まったらどうなるでしょう?podも一緒に止まり、Deploymentが新たに作成しますが、IPは異なります。これはServiceで解決できます。

<!--
A Kubernetes Service is an abstraction which defines a logical set of Pods running somewhere in your cluster, that all provide the same functionality. When created, each Service is assigned a unique IP address (also called clusterIP). This address is tied to the lifespan of the Service, and will not change while the Service is alive. Pods can be configured to talk to the Service, and know that communication to the Service will be automatically load-balanced out to some pod that is a member of the Service.
-->
KubernetesのServiceは同じ機能を持ったPodの論理的な集まりを定義し、抽象化したものです。作成されると、一意のIPアドレス(clusterIPとも呼ばれます)が与えられます。このアドレスはServiceが存在する限り存在し、変更されることはありません。PodはSeriviceと通信するようにしておけば、自動的にServiceのメンバーであるpodにロードバランスされます。

<!--
You can create a Service for your 2 nginx replicas with kubectl expose:
-->
kubectl exposeで2つのnginxレプリカのServiceを作成します:

```bash
kubectl -n my-nginx expose deployment/my-nginx
```

Output:
{{< output >}}
service/my-nginx exposed
{{< /output >}}

<!--
This specification will create a Service which targets TCP port 80 on any Pod with the run: my-nginx label, and expose it on an abstracted Service port (targetPort: is the port the container accepts traffic on, port: is the abstracted Service port, which can be any port other pods use to access the Service). View Service API object to see the list of supported fields in service definition. Check your Service:
-->
これはTCPポート80番で待ち受けているrun: my-nginxのラベルがあるPodを抽象化されたServiceのポート(targetPort: コンテナが通信を待ち受けるポート、port: 他のPodがServiceに接続する時に使う抽象化されたServiceのポート)で公開します。Serviceの定義でサポートされている項目のリストはServiceのAPIオブジェクトを参照してください。Serviceを確認します:

```bash
kubectl -n my-nginx get svc my-nginx
```

{{< output >}}
NAME       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
my-nginx   ClusterIP   10.100.225.196   <none>        80/TCP    25s
{{< /output >}}

<!--
As mentioned previously, a Service is backed by a group of Pods. These Pods are exposed through endpoints. The Service’s selector will be evaluated continuously and the results will be POSTed to an Endpoints object also named my-nginx. When a Pod dies, it is automatically removed from the endpoints, and new Pods matching the Service’s selector will automatically get added to the endpoints. Check the endpoints, and note that the IPs are the same as the Pods created in the first step:
-->
前に出たように、ServiceはPodの集まりでできています。これらのPodはエンドポイントを通じて公開されています。Serviceのセレクタは常に評価されており、結果はmy-nginxという名前のエンドポイントオブジェクトとしてPOSTされています。Podが止まると、自動的にエンドポイントから削除され、Serviceのセレクタに合ったPodが自動的にエンドポイントに追加されます。エンドポイントを確認して、IPが最初の手順で作成したPodのものであることを覚えましょう:

```bash
kubectl -n my-nginx describe svc my-nginx
```

{{< output >}}
Name:              my-nginx
Namespace:         my-nginx
Labels:            run=my-nginx
Annotations:       <none>
Selector:          run=my-nginx
Type:              ClusterIP
IP:                10.100.225.196
Port:              <unset>  80/TCP
TargetPort:        80/TCP
Endpoints:         192.168.59.188:80,192.168.79.210:80
Session Affinity:  None
Events:            <none>
{{< /output >}}

<!--
You should now be able to curl the nginx Service on _CLUSTER-IP: PORT_ from any pods in your cluster.
-->
これでクラスタ内のどのpodからも _CLUSTER-IP: PORT_ のnginx Serviceにcurlができるはずです。

<!--
{{% notice note %}}
The Service IP is completely virtual, it never hits the wire.
{{% /notice %}}
-->
{{% notice note %}}
Service IPは仮想上のものなので、実ケーブル上にでることはありません
{{% /notice %}}

<!--
Let's try that by :
-->
これを試してみましょう:

<!--
Setting a variable called _MyClusterIP_ with the my-nginx `Service` IP.
-->
_MyClusterIP_ という変数をmy-nginxの `Service` IPで設定します。

<!--
```bash
# Create a variable set with the my-nginx service IP
export MyClusterIP=$(kubectl -n my-nginx get svc my-nginx -ojsonpath='{.spec.clusterIP}')
```
-->
```bash
# my-nginxのservice IPで変数をセットします
export MyClusterIP=$(kubectl -n my-nginx get svc my-nginx -ojsonpath='{.spec.clusterIP}')
```

<!--
Creating a new deployment called _load-generator_ (with the _MyClusterIP_ variable also set inside the container) and get an interactive shell on a pod + container.
-->
新たに _load-generator_ というdeploymentを作成(コンテナ内で _MyClusterIP_ という変数もセット)し、コンテナのshellに入ります。

<!--
```bash
# Create a new deployment and allocate a TTY for the container in the pod
kubectl -n my-nginx run -i --tty load-generator --env="MyClusterIP=${MyClusterIP}" --image=busybox /bin/sh
```
-->
```bash
# 新たにdeploymentを作成し。pod内のコンテナにTTYを割り振ります
kubectl -n my-nginx run -i --tty load-generator --env="MyClusterIP=${MyClusterIP}" --image=busybox /bin/sh
```

<!--
{{% notice tip %}}
[Click here](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-env-em-) for more information on the _-env_ parameter.
{{% /notice %}}
-->
{{% notice tip %}}
 _-env_ パラメータについての詳しい情報は[こちら](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-env-em-)。
{{% /notice %}}

<!--
Connecting to the nginx welcome page using the ClusterIP.
-->
ClusterIPを使ってnginxのwelcomeページにアクセスします。

```bash
wget -q -O - ${MyClusterIP} | grep '<title>'
```

<!--
The output will be
-->
出力は

{{< output >}}
<title>Welcome to nginx!</title>
{{< /output >}}

<!--
Type exit to log out of the container:
-->
exitと打って、コンテナから抜けます:

```bash
 exit
```
