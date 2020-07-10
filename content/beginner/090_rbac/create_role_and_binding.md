---
title: "ロールと紐づけ"
date: 2018-10-03T10:14:46-07:00
draft: false
weight: 50
---

<!--
As mentioned earlier, we have our new user rbac-user, but its not yet bound to any roles.  In order to do that, we'll need to switch back to our default admin user.
-->
先に出たように、rbac-userはありますがどのロールにも紐づいていません。それを行うには、adminユーザに戻る必要があります。

<!--
Run the following to unset the environmental variables that define us as rbac-user:
-->
次のコマンドを実行してrbac-userに設定されている環境変数を消しましょう:

```
unset AWS_SECRET_ACCESS_KEY
unset AWS_ACCESS_KEY_ID
```

<!--
To verify we're the admin user again, and no longer rbac-user, issue the following command:
-->
rbac-userでなくadminユーザであることを確認するために、次のコマンドを実行します:

```
aws sts get-caller-identity
```

<!--
The output should show the user is no longer rbac-user:
-->
rbac-userでないことが表示されているはずです:

{{< output >}}
{
"Account": <AWS Account ID>,
"UserId": <AWS User ID>,
"Arn": "arn:aws:iam::<your AWS account ID>:assumed-role/eksworkshop-admin/i-123456789"
}
{{< /output >}}

<!--
Now that we're the admin user again, we'll create a role called pod-reader that provides list, get, and watch access for pods and deployments, but only for the rbac-test namespace.  Run the following to create this role:
-->
adminユーザになったので、rbac-testという名前空間でpodとdeploymentのlist、getそしてwatchのみが許可されているpod-readerというロールを作成します。このロールを作成するには次のコマンドを実行します:

```
cat << EoF > rbacuser-role.yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: rbac-test
  name: pod-reader
rules:
- apiGroups: [""] # "" indicates the core API group
  resources: ["pods"]
  verbs: ["list","get","watch"]
- apiGroups: ["extensions","apps"]
  resources: ["deployments"]
  verbs: ["get", "list", "watch"]
EoF
```

<!--
We have the user, we have the role, and now we're bind them together with a RoleBinding resource.  Run the following to create this RoleBinding:
-->
ユーザができ、ロールができ、そしてRoleBindingリソースで二つを紐づけます。次を実行してRoleBindingを作成します:

```
cat << EoF > rbacuser-role-binding.yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: read-pods
  namespace: rbac-test
subjects:
- kind: User
  name: rbac-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
EoF
```

<!--
Next, we apply the Role, and RoleBindings we created:
-->
次にロールを適用、そしてRoleBindingを適用します:

```
kubectl apply -f rbacuser-role.yaml
kubectl apply -f rbacuser-role-binding.yaml
```
