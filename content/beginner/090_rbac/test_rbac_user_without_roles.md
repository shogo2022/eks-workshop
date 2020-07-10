---
title: "新しいユーザのテスト"
date: 2018-10-03T10:14:46-07:00
draft: false
weight: 40
---

<!--
Up until now, as the cluster operator, you've been accessing the cluster as the admin user.  Let's now see what happens when we access the cluster as the newly created rbac-user.
-->
ここまでは、addminユーザとしてクラスターに接続していました。新しく作ったrbac-userとしてアクセスして何が怒るかみてみましょう。

<!--
Issue the following command to source the rbac-user's AWS IAM user environmental variables:
-->
次のコマンドを実行し、rbac-userのAWS IAMユーザを環境変数に設定します:

<!--
```
. rbacuser_creds.sh
```
By running the above command, you've now set AWS environmental variables which should override the default admin user or role.  To verify we've overrode the default user settings, run the following command:
-->
```
. rbacuser_creds.sh
```
このコマンドを実行することで、でdフォルトのadminユーザまたはロールを上書きするようにAWS環境変数が設定されました。デフォルトの設定が上書きされたことを確かめるためには、次のコマンドを実行します:

```
aws sts get-caller-identity
```

<!--
You should see something similar to below, where we're now making API calls as rbac-user:
-->
rbac-userとしてAPIコールを投げているのが、以下のように見えるはずです:

{{< output >}}

{
"Account": <AWS Account ID>,
"UserId": <AWS User ID>,
"Arn": "arn:aws:iam::<AWS Account ID>:user/rbac-user"
}

{{< /output >}}

<!--
Now that we're making calls in the context of the rbac-user, lets quickly make a request to get all pods:
-->
rbac-userのコンテキストでリクエストをしている状態で、全てのpodを取得するリクエストを投げてみましょう:

```
kubectl get pods -n rbac-test
```

<!--
You should get a response back similar to:
-->
次のようなレスポンスが返ってくるはずです:

{{< output >}}
No resources found.  Error from server (Forbidden): pods is forbidden: User "rbac-user" cannot list resource "pods" in API group "" in the namespace "rbac-test"
{{< /output >}}

<!--
We already created the rbac-user, so why did we get that error?  
-->
rbac-userは作成してあるのに、なぜエラーが出るのでしょう?

<!--
Just creating the user doesn't give that user access to any resources in the cluster.  In order to achieve that, we'll need to define a role, and then bind the user to that role.  We'll do that next.
-->
ユーザを作成しただけではクラスター内のどのリソースにもアクセスできません。ロールを定義し、ユーザをロールに紐づける作業が必要です。次はそれを行います。