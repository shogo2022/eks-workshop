---
title: "Grafanaのデプロイ"
date: 2018-10-14T20:54:13-04:00
weight: 15
draft: false
---

<!--
We are now going to install Grafana. For this example, we are primarily using the Grafana defaults,
but we are overriding several parameters. As with Prometheus, we are setting the storage class
to gp2, admin password, configuring the datasource to point to Prometheus and creating an
[external load](https://kubernetes.io/docs/tasks/access-application-cluster/create-external-load-balancer/)
balancer for the service.
-->
Grafanaをインストールしていきます。この例では、ほぼGrafanaのデフォルトを使いますが、いくつかのパラメータを変更します。Prometheusと同じようにstorage classをgp2に設定し、adminパスワードの変更、データソースをPrometheusに設定、そして[外部ロードバランサー](https://kubernetes.io/docs/tasks/access-application-cluster/create-external-load-balancer/)を作成します。

<!--
Create YAML file called grafana.yaml with following values:
-->
次の内容でgrafana.yamlというYAAMLファイルを作成します:

```
datasources:
  datasources.yaml:
    apiVersion: 1
    datasources:
    - name: Prometheus
      type: prometheus
      url: http://prometheus-server.prometheus.svc.cluster.local
      access: proxy
      isDefault: true
```

```bash
kubectl create namespace grafana
helm install grafana stable/grafana \
    --namespace grafana \
    --set persistence.storageClassName="gp2" \
    --set persistence.enabled=true \
    --set adminPassword='EKS!sAWSome' \
    --values grafana.yaml \
    --set service.type=LoadBalancer
```
<!--
Run the following command to check if Grafana is deployed properly:
-->
次を実行し、Grafanaがデプロイされたか確認します:

```bash
kubectl get all -n grafana
```

<!--
You should see similar results. They should all be Ready and Available
-->
これと同じような結果が見えるはずです。全てがReadyでAvailableなはずです

```text
NAME                          READY     STATUS    RESTARTS   AGE
pod/grafana-b9697f8b5-t9w4j   1/1       Running   0          2m

NAME              TYPE           CLUSTER-IP       EXTERNAL-IP                                                               PORT(S)        AGE
service/grafana   LoadBalancer   10.100.49.172   abe57f85de73111e899cf0289f6dc4a4-1343235144.us-west-2.elb.amazonaws.com   80:31570/TCP   3m


NAME                      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/grafana   1         1         1            1           2m

NAME                                DESIRED   CURRENT   READY     AGE
replicaset.apps/grafana-b9697f8b5   1         1         1         2m
```

<!--
You can get Grafana ELB URL using this command. Copy & Paste the value into browser to access Grafana web UI.
-->
このコマンドでGrafanaのELB URLを見つけることができます。出てきた値をブラウザに貼り付けてGrafanaのウェブUIにアクセスします。

```bash
export ELB=$(kubectl get svc -n grafana grafana -o jsonpath='{.status.loadBalancer.ingress[0].hostname}')

echo "http://$ELB"
```

<!--
When logging in, use the username **admin** and get the password hash by running the following:
-->
ログインするには、ユーザ名 **admin** と、次のコマンドで出てくるパスワードを使います:

```bash
kubectl get secret --namespace grafana grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

<!--
{{% notice tip %}}
It can take several minutes before the ELB is up, DNS is propagated and the nodes are registered.
{{% /notice %}}
-->
{{% notice tip %}}
ELBが起動し、ノードが登録されDNSが行き渡るまで数分かかります。
{{% /notice %}}