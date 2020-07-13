---
title: "Affinityと反affinity"
date: 2019-04-09T00:00:00-03:00
weight: 11
draft: false
---

<!--
#### Affinity and anti-affinity
nodeSelector provides a very simple way to constrain pods to nodes with particular labels. The affinity/anti-affinity feature, currently in beta, greatly extends the types of constraints you can express. The key enhancements are:
-->
#### Affinityと反affinity
nodeSelectorは特定のラベルがついたノードにpodを制限する最も簡単な方法です。affinity/反affinity(anti-affinity)機能は、現在はbetaですが、表現できる制限の幅を大きく広げてくれます。主要な拡張機能は以下の通り:

<!--
- The language is more expressive (not just “AND of exact match”)
- You can indicate that the rule is “soft”/“preference” rather than a hard requirement, so if the scheduler can’t satisfy it, the pod will still be scheduled
- You can constrain against labels on other pods running on the node (or other topological domain), rather than against labels on the node itself, which allows rules about which pods can and cannot be co-located
-->
- 言語表現が豊か("ANDの完全一致"だけではない)
- ルールに厳格(hard)な要求だけではなく"寛容(soft)"/"優先(preference)"を指定でき、要求が満たされない時でもpodを配置することができる
- ノード(もしくは他のトポロジードメイン)で実行されている他のpodのラベルに基づいて制限でき、podが同居できるかのルールを作れる

The affinity feature consists of two types of affinity, “node affinity” and “inter-pod affinity/anti-affinity”. Node affinity is like the existing nodeSelector (but with the first two benefits listed above), while inter-pod affinity/anti-affinity constrains against pod labels rather than node labels, as described in the third item listed above, in addition to having the first and second properties listed above.

<!--
#### Node affinity (beta feature)
Node affinity was introduced as alpha in Kubernetes 1.2. Node affinity is conceptually similar to nodeSelector – it allows you to constrain which nodes your pod is eligible to be scheduled on, based on labels on the node.
-->
#### ノードaffinity(ベータ機能)
ノードaffinityはKubernetes 1.2でアルファとして実装されました。ノードaffinityはコンセプトとしてはnodeSelectorと似ていて、ラベルに基づいてpodが実行できるノードを制御できます。

<!--
There are currently two types of node affinity, called `requiredDuringSchedulingIgnoredDuringExecution` and `preferredDuringSchedulingIgnoredDuringExecution`. 
-->
現在は2種類のnode affinityがあり、それぞれ `requiredDuringSchedulingIgnoredDuringExecution` と `preferredDuringSchedulingIgnoredDuringExecution` と呼ばれています。

<!--
You can think of them as “hard” and “soft” respectively, in the sense that the former specifies rules that must be met for a pod to be scheduled onto a node (just like nodeSelector but using a more expressive syntax), while the latter specifies preferences that the scheduler will try to enforce but will not guarantee. The “IgnoredDuringExecution” part of the names means that, similar to how nodeSelector works, if labels on a node change at runtime such that the affinity rules on a pod are no longer met, the pod will still continue to run on the node. 
-->
これらはそれぞれ"厳格(hard)"と"寛容(soft)"と思ってください。前者はnodeSelectorと同じ(ただし、言語表現はもっと豊か)で、podがノードで動くためには条件が合う必要がありますが、後者は条件を満たすようにスケジューラは動きますが保証はしません。“IgnoredDuringExecution”の部分は、nodeSelectorの挙動と同じで、稼働中にノードのラベルが変更され、affinityルールと合わなくなったとしても、podはそのノードで引き続き実行され続けるということを意味しています。

<!--
Thus an example of `requiredDuringSchedulingIgnoredDuringExecution` would be “only run the pod on nodes with Intel CPUs” and an example `preferredDuringSchedulingIgnoredDuringExecution` would be “try to run this set of pods in availability zone XYZ, but if it’s not possible, then allow some to run elsewhere”.
-->
したがって、 `requiredDuringSchedulingIgnoredDuringExecution` の例としては"Intel CPUのノードでのみ実行すること"となり、 `preferredDuringSchedulingIgnoredDuringExecution` の例としては"このpodはアベイラビリティゾーンXYZで動かして欲しい、無理だったら他のところでも可能"となります。

<!--
Node affinity is specified as field nodeAffinity of field affinity in the PodSpec.
-->
ノードaffinityはPodSpecのaffinityフィールドの中にnodeAffinityとして指定します。

<!--
Let's see an example of a pod that uses node affinity:
--->
ノードaffinityを使ったpodの例をみてみましょう:

<!--
We are going to create another label in the same node that in the last example:
-->
最後の例で使ったノードに別のラベルを追加します:

```
kubectl label nodes ip-192-168-15-64.us-west-2.compute.internal azname=az1
```
<!--
And create an affinity:
-->
affinityを作成します:
```
cat <<EoF > ~/environment/pod-with-node-affinity.yaml
apiVersion: v1
kind: Pod
metadata:
  name: with-node-affinity
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: azname
            operator: In
            values:
            - az1
            - az2
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: another-node-label-key
            operator: In
            values:
            - another-node-label-value
  containers:
  - name: with-node-affinity
    image: us.gcr.io/k8s-artifacts-prod/pause:2.0
EoF
```

<!--
This node affinity rule says the pod can only be placed on a node with a label whose key is `azname` and whose value is either `az1` or `az2`. In addition, among nodes that meet that criteria, nodes with a label whose key is `another-node-label-key` and whose value is `another-node-label-value` should be preferred.
-->
このノードaffinityルールは、キーが `azname` 、バリューが `az1` もしくは `az2` というラベルがついたノードでのみpodが実行されることを強制しています。追加で、この条件を満たしたノードの内、キーが `another-node-label-key` でバリューが `another-node-label-value` というラベルを持ったノードが優先的に使われます。

<!--
Let's apply this 
-->
これを適用しましょう
```
kubectl apply -f ~/environment/pod-with-node-affinity.yaml
```
<!--
And check if it worked with `kubectl get pods -o wide` 
-->
うまくいったかを `kubectl get pods -o wide` で確認します
{{< output >}}
NAME                 READY     STATUS    RESTARTS   AGE       IP               NODE                                          NOMINATED NODE
nginx                1/1       Running   0          35m       192.168.10.13    ip-192-168-15-64.us-west-2.compute.internal   <none>
with-node-affinity   1/1       Running   0          29s       192.168.14.121   ip-192-168-15-64.us-west-2.compute.internal   <none>
{{< /output >}}
<!--
Now let's try to put the affinity in another node
We are going to put the label in a different node so first, let's clean the label and delete the Pod.
-->
他のノードにaffinityをつけてみましょう
まずはPodを消し、ラベルをきれいにしてから、他のノードにラベルをつけます。
```
kubectl delete -f ~/environment/pod-with-node-affinity.yaml
kubectl label nodes ip-192-168-15-64.us-west-2.compute.internal azname-
```
<!--
We are putting the label to the node ip-192-168-86-147.us-west-2.compute.internal now
-->
ip-192-168-86-147.us-west-2.compute.internalのノードにラベルをつけます
```
kubectl label nodes ip-192-168-86-147.us-west-2.compute.internal azname=az1
kubectl apply -f ~/environment/pod-with-node-affinity.yaml
```
<!--
And check if it works with `kubectl get pods -o wide` 
-->
うまくいったかを `kubectl get pods -o wide` で確認します
{{< output >}}
NAME                 READY     STATUS    RESTARTS   AGE       IP               NODE                                           NOMINATED NODE
nginx                1/1       Running   0          43m       192.168.10.13    ip-192-168-15-64.us-west-2.compute.internal    <none>
with-node-affinity   1/1       Running   0          42s       192.168.68.249   ip-192-168-86-147.us-west-2.compute.internal   <none>
{{< /output >}}

<!--
You can see the operator In being used in the example. The new node affinity syntax supports the following operators: `In, NotIn, Exists, DoesNotExist, Gt, Lt`. You can use `NotIn` and `DoesNotExist` to achieve node anti-affinity behavior.
-->
例では In というオペレータが使われています。ノードaffinityは次のオペレータをサポートしています: `In, NotIn, Exists, DoesNotExist, Gt, Lt` 。反affinity(anti-affinity)をするには、 `NotIn` と `DoesNotExist` を使います。

<!--
- If you specify both `nodeSelector` and `nodeAffinity`, both must be satisfied for the pod to be scheduled onto a candidate node.
- If you specify multiple `nodeSelectorTerms` associated with `nodeAffinity` types, then the pod can be scheduled onto a node if one of the `nodeSelectorTerms` is satisfied.
- If you specify multiple `matchExpressions` associated with `nodeSelectorTerms`, then the pod can be scheduled onto a node only if all `matchExpressions` can be satisfied.
- If you remove or change the label of the node where the pod is scheduled, the pod won’t be removed. In other words, the affinity selection works only at the time of scheduling the pod.
-->
- `nodeSelector` と `nodeAffinity` の両方がある場合は、podがノードで走るためには両方が満たされている必要がある。
- 複数の `nodeSelectorTerms` が `nodeAffinity` に紐づいていた場合は、 `nodeSelectorTerms` の内の一つでも満たされていればpodは配置される。
- 複数の `matchExpressions` が `nodeSelectorTerms` に紐づいていた場合は、すべての `matchExpressions` が満たされた場合のみpodは配置される
- podが配置された後にノードのラベルの削除、変更を行ったとしても、podは削除されない。言い換えると、あffinityの選択はpodが配置されるときにしか起こらない。

<!--
The weight field in `preferredDuringSchedulingIgnoredDuringExecution` is in the range 1-100. For each node that meets all of the scheduling requirements (resource request, RequiredDuringScheduling affinity expressions, etc.), the scheduler will compute a sum by iterating through the elements of this field and adding “weight” to the sum if the node matches the corresponding MatchExpressions. This score is then combined with the scores of other priority functions for the node. The node(s) with the highest total score are the most preferred.
-->
`preferredDuringSchedulingIgnoredDuringExecution` のweuight項目は1-100の間です。配置要件(リソース要求、RequiredDuringSchedulingなど)にあったノードそれぞれに対して、スケジューラは要件にあった項目の"weight"を合算します。この値は他の優先順位付け機能と合わさり、この値が最も高いノードがもっとも優先されることになります。