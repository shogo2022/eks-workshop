---
title: "レディネスプローブの設定"
chapter: false
weight: 10
---

<!--
#### Configure the Probe
-->
#### プローブの設定

<!--
Run the following code block to populate **~/environment/healthchecks/readiness-deployment.yaml**. The readinessProbe definition explains how a linux command can be configured as healthcheck. We create an empty file **/tmp/healthy** to configure readiness probe and use the same to understand how kubelet helps to update a deployment with only healthy pods. 
-->
次のコマンドを実行して、**~/environment/healthchecks/readiness-deployment.yaml**を作成します。レディネスプローブはヘルスチェックに使われるlinuxコマンドで定義されます。ここでは、空のファイル**/tmp/healthy**をレディネスプローブ用に作成し、kubeletがどのようにdeploymentをアップデートするのかを理解します。

```
cat <<EoF > ~/environment/healthchecks/readiness-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: readiness-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: readiness-deployment
  template:
    metadata:
      labels:
        app: readiness-deployment
    spec:
      containers:
      - name: readiness-deployment
        image: alpine
        command: ["sh", "-c", "touch /tmp/healthy && sleep 86400"]
        readinessProbe:
          exec:
            command:
            - cat
            - /tmp/healthy
          initialDelaySeconds: 5
          periodSeconds: 3
EoF
```

<!--
We will now create a deployment to test readiness probe:
-->
レディネスプローブをテストするためのdeploymentを作成します:

```
kubectl apply -f ~/environment/healthchecks/readiness-deployment.yaml
```

<!--
The above command creates a deployment with 3 replicas and readiness probe as described in the beginning.
-->
このコマンドは冒頭にあったように、3つのレプリカとレディネスプローブからなるdeploymentを作成します。

```
kubectl get pods -l app=readiness-deployment
```

<!--
The output looks similar to below:
-->
表示は下の通りです:

{{< output >}}

NAME                                    READY     STATUS    RESTARTS   AGE
readiness-deployment-7869b5d679-922mx   1/1       Running   0          31s
readiness-deployment-7869b5d679-vd55d   1/1       Running   0          31s
readiness-deployment-7869b5d679-vxb6g   1/1       Running   0          31s
{{< /output >}}

<!--
Let us also confirm that all the replicas are available to serve traffic when a service is pointed to this deployment.
-->
serviceがこのdeploymentに向いた時には全てのレプリカでトラフィックを受け入れらる状態になっていたことを確認しましょう。

```
kubectl describe deployment readiness-deployment | grep Replicas:
```

<!--
The output looks like below:
-->
表示は次の通りです:

{{< output >}}
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
{{< /output >}}

<!--
#### Introduce a Failure
Pick one of the pods from above 3 and issue a command as below to delete the **/tmp/healthy** file which makes the readiness probe fail.
-->
#### 障害の投入
上の3つからpoddを一つ選び、次のコマンドを実行してください。**/tmp/heatthy**を削除することで、レディネスプローブが失敗します。

```
kubectl exec -it <YOUR-READINESS-POD-NAME> -- rm /tmp/healthy
```

<!--
**readiness-deployment-7869b5d679-922mx** was picked in our example cluster. The **/tmp/healthy** file was deleted. This file must be present for the readiness check to pass. Below is the status after issuing the command.
-->
私たちの場合は**readiness-deployment-7869b5d679-922mx**を選んでいます。**/tmp/healthy**ファイルは削除されました。レディネスチェックが成功するためにはこのファイルが必要です。次はこのコマンドが実行されたあとのステータスです。

```
kubectl get pods -l app=readiness-deployment
```

<!--
The output looks similar to below:
{{< output >}}
NAME                                    READY     STATUS    RESTARTS   AGE
readiness-deployment-7869b5d679-922mx   0/1       Running   0          4m
readiness-deployment-7869b5d679-vd55d   1/1       Running   0          4m
readiness-deployment-7869b5d679-vxb6g   1/1       Running   0          4m
{{< /output >}}
Traffic will not be routed to the first pod in the above deployment. The ready column confirms that the readiness probe for this pod did not pass and hence was marked as not ready. 
-->
下のように表示されるはずです:
{{< output >}}
NAME                                    READY     STATUS    RESTARTS   AGE
readiness-deployment-7869b5d679-922mx   0/1       Running   0          4m
readiness-deployment-7869b5d679-vd55d   1/1       Running   0          4m
readiness-deployment-7869b5d679-vxb6g   1/1       Running   0          4m
{{< /output >}}
トラフィックは最初のpodには送られません。ready列でこのpodに対するレディネスプローブが失敗しており、準備ができていないと認識されています。 

<!--
We will now check for the replicas that are available to serve traffic when a service is pointed to this deployment.
-->
serviceがこのdeploymentに向けられている状態で、どのレプリカがトラフィックを受け入れらるようになっているか確認します。

```
kubectl describe deployment readiness-deployment | grep Replicas:
```

<!--
The output looks like below:
-->
次のように表示されます:

{{< output >}}
Replicas:               3 desired | 3 updated | 3 total | 2 available | 1 unavailable
{{< /output >}}

<!--
When the readiness probe for a pod fails, the endpoints controller removes the pod from list of endpoints of all services that match the pod.
-->
podに対するレディネスプローブが失敗した場合、エンドポイントコントローラは全てのserviceのエンドポイントからpodを外します。

<!--
#### Challenge: 
**How would you restore the pod to Ready status?**
{{%expand "Expand here to see the solution" %}}
Run the below command with the name of the pod to recreate the **/tmp/healthy** file. Once the pod passes the probe, it gets marked as ready and will begin to receive traffic again.
-->
#### チャレンジ: 
**どうしたらpodをReadyに戻せるでしょうか?**
{{%expand "答えはこちら" %}}
次のコマンドをpodに実行し、**/tmp/healthy**を再作成します。podがプローブに成功すると、readyとなり再びトラフィックを受け取るようになります。

```
kubectl exec -it <YOUR-READINESS-POD-NAME> -- touch /tmp/healthy
```
```
kubectl get pods -l app=readiness-deployment
```
{{% /expand %}}
