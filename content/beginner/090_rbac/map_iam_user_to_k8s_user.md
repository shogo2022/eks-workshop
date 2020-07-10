---
title: "IAMユーザのK8sへの紐づけ"
date: 2018-10-03T10:14:46-07:00
draft: false
weight: 30
---

<!--
Next, we'll define a k8s user called rbac-user, and map to it's IAM user counterpart.  Run the following to get the existing ConfigMap and save into a file called aws-auth.yaml:
```
kubectl get configmap -n kube-system aws-auth -o yaml > aws-auth.yaml
```
Next append the rbac-user mapping to the existing configMap
-->
次にrbac-userというk8sユーザを定義し、IAMユーザと紐づけます。次のコマンドを実行し、現在のConfigMapを取得しaws-auth.yamlとして保存します:
```
kubectl get configmap -n kube-system aws-auth -o yaml > aws-auth.yaml
```
そして既存のconfigMapにrbac-userの紐づけを追加します

```
cat << EoF >> aws-auth.yaml
data:
  mapUsers: |
    - userarn: arn:aws:iam::${ACCOUNT_ID}:user/rbac-user
      username: rbac-user
EoF
```

<!--
Some of the values may be dynamically populated when the file is created.  To verify everything populated and was created correctly, run the following:
-->
いくつかの値はファイルが作成された時に動的に入ります。全てが入力され、正しく作成されているか確認するには次を実行します:

```
cat aws-auth.yaml
```

<!--
And the output should reflect that rolearn and userarn populated, similar to:
-->
rolearnとuserarnが入力されており、以下のようになっています:

{{< output >}}
apiVersion: v1
kind: ConfigMap
metadata:
  name: aws-auth
  namespace: kube-system
data:
  mapUsers: |
    - userarn: arn:aws:iam::123456789:user/rbac-user
      username: rbac-user
{{< /output >}}

<!--
Next, apply the ConfigMap to apply this mapping to the system:
-->
次にシステムにこの割り当てを定期ようするためにConfigMapを適用します:
```
kubectl apply -f aws-auth.yaml
```
