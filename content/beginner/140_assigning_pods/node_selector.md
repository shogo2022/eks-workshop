---
title: "nodeSelector"
date: 2019-04-09T00:00:00-03:00
weight: 10
draft: false
---

<!--
nodeSelector is the simplest recommended form of node selection constraint. nodeSelector is a field of PodSpec. It specifies a map of key-value pairs. For the pod to be eligible to run on a node, the node must have each of the indicated key-value pairs as labels (it can have additional labels as well). The most common usage is one key-value pair.
-->
nodeSelectorはノード選択を制御するうえで、推奨されている最もシンプルな方法です。nodeSelectorはPodSpecの一項目です。キー・バリューペアで指定します。podがノードで動くためには、ノードは指定されたキー・バリューペアをラベルに持っていなければなりません。もっともよく使われるのは、キー・バリューペアひとつです。

<!--
#### Attach a label to the node
-->
#### ノードにラベルをアタッチする

<!--
Run kubectl get nodes to get the names of your cluster’s nodes. 
```
kubectl get nodes
```
Output will be like 
{{< output >}}
NAME                                           STATUS    ROLES     AGE       VERSION
ip-192-168-15-64.us-west-2.compute.internal    Ready     <none>    8d        v1.12.7
ip-192-168-38-150.us-west-2.compute.internal   Ready     <none>    8d        v1.12.7
ip-192-168-86-147.us-west-2.compute.internal   Ready     <none>    7d23h     v1.12.7
ip-192-168-92-222.us-west-2.compute.internal   Ready     <none>    8d        v1.12.7
{{< /output >}}
Pick out the one that you want to add a label to, and then run 
```
kubectl label nodes <node-name> <label-key>=<label-value> 
```
to add a label to the node you’ve chosen. 
-->
クラスタのノード一覧を取得するため、kubectl get nodesを実行します。
```
kubectl get nodes
```
Output will be like 
{{< output >}}
NAME                                           STATUS    ROLES     AGE       VERSION
ip-192-168-15-64.us-west-2.compute.internal    Ready     <none>    8d        v1.12.7
ip-192-168-38-150.us-west-2.compute.internal   Ready     <none>    8d        v1.12.7
ip-192-168-86-147.us-west-2.compute.internal   Ready     <none>    7d23h     v1.12.7
ip-192-168-92-222.us-west-2.compute.internal   Ready     <none>    8d        v1.12.7
{{< /output >}}
ラベルをつけたいノードを一つ選び、以下を実行します
```
kubectl label nodes <node-name> <label-key>=<label-value> 
```
これで選択したノードにラベルがつきました。 

<!--
For example, if my node name is ‘ip-192-168-15-64.us-west-2.compute.internal’ and my desired label is ‘disktype=ssd’, then I can run 
```
kubectl label nodes ip-192-168-15-64.us-west-2.compute.internal disktype=ssd
```
You can verify that it worked by re-running kubectl get nodes --show-labels and checking that the node now has a label. You can also use kubectl describe node "nodename" to see the full list of labels of the given node.
-->
例としてもしノードの名前が‘ip-192-168-15-64.us-west-2.compute.internal’で、つけるラベルが‘disktype=ssd’の場合は、以下を実行します 
```
kubectl label nodes ip-192-168-15-64.us-west-2.compute.internal disktype=ssd
```
うまくいったかを確認するには、kubectl get nodes --show-labels を実行し、ノードにラベルがついているかを確認します。指定したノードの全てのラベルをリストするには、kubectl describe node "nodename"を使います。

```
kubectl get nodes --show-labels
```
Output will be like
{{< output >}}
NAME                                           STATUS    ROLES     AGE       VERSION   LABELS
ip-192-168-15-64.us-west-2.compute.internal    Ready     <none>    8d        v1.12.7   alpha.eksctl.io/cluster-name=eksworkshop-eksctl,alpha.eksctl.io/instance-id=i-064fdae0afd3cbe8b,alpha.eksctl.io/nodegroup-name=ng-cd62916d,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=m5.large,beta.kubernetes.io/os=linux,disktype=ssd,failure-domain.beta.kubernetes.io/region=us-west-2,failure-domain.beta.kubernetes.io/zone=us-west-2d,kubernetes.io/hostname=ip-192-168-15-64.us-west-2.compute.internal
ip-192-168-38-150.us-west-2.compute.internal   Ready     <none>    8d        v1.12.7   alpha.eksctl.io/cluster-name=eksworkshop-eksctl,alpha.eksctl.io/instance-id=i-0420598c17da0a4b4,alpha.eksctl.io/nodegroup-name=ng-cd62916d,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=m5.large,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=us-west-2,failure-domain.beta.kubernetes.io/zone=us-west-2c,kubernetes.io/hostname=ip-192-168-38-150.us-west-2.compute.internal
ip-192-168-86-147.us-west-2.compute.internal   Ready     <none>    7d23h     v1.12.7   alpha.eksctl.io/cluster-name=eksworkshop-eksctl,alpha.eksctl.io/instance-id=i-02e33f4429c64e628,alpha.eksctl.io/nodegroup-name=ng-cd62916d,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=m5.large,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=us-west-2,failure-domain.beta.kubernetes.io/zone=us-west-2b,kubernetes.io/hostname=ip-192-168-86-147.us-west-2.compute.internal
ip-192-168-92-222.us-west-2.compute.internal   Ready     <none>    8d        v1.12.7   alpha.eksctl.io/cluster-name=eksworkshop-eksctl,alpha.eksctl.io/instance-id=i-02eadff5d2af1ce12,alpha.eksctl.io/nodegroup-name=ng-cd62916d,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=m5.large,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=us-west-2,failure-domain.beta.kubernetes.io/zone=us-west-2b,kubernetes.io/hostname=ip-192-168-92-222.us-west-2.compute.internal
{{< /output >}}
<!--
#### Add a nodeSelector field to your pod configuration
Take whatever pod config file you want to run, and add a nodeSelector section to it, like this. For example, if this is my pod config:
-->
#### podの設定にnodeSelectorを追加する
適当なpodnの設定ファイルを使い、以下のようにnodeSelectorセクションを追加します。ここでは例として、私のpod設定をお見せします:
{{< output >}}
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
{{< /output >}}
<!--
Then add a nodeSelector like so:
-->
nodeSelectorを追加します:
```
cat <<EoF > ~/environment/pod-nginx.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  nodeSelector:
    disktype: ssd
EoF
```
<!--
Then you run
-->
適用します
```
kubectl apply -f ~/environment/pod-nginx.yaml
```
<!--
And the Pod will get scheduled on the node that you attached the label to. You can verify that it worked by running
-->
Podはラベルをつけたノードに配置されます。うまくいったことを確かめるには以下を実行します
```
kubectl get pods -o wide
```
And looking at the “NODE” that the Pod was assigned to
{{< output >}}
NAME      READY     STATUS    RESTARTS   AGE       IP              NODE                                          NOMINATED NODE
nginx     1/1       Running   0          12s       192.168.10.13   ip-192-168-15-64.us-west-2.compute.internal   <none>
{{< /output >}}

